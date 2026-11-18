# Case 2 — Dịch vụ feature-cache bị Timeout khi truy cập Cross-Node qua CNI Flannel Overlay

- Ngày/giờ, người điều tra: 2026-11-17 22:42 (Asia/Ho_Chi_Minh), Học viên & LLM Gia sư
- Bài học / môi trường / phiên bản: Bài 26b / Mac k3d-de-k3d / K3s v1.33.6+k3s1, Flannel CNI (VXLAN mode), iptables proxy-mode
- Phạm vi lab và quyền được xác nhận: Namespace `nfk-26b-case2`, Node `k3d-de-k3d-agent-0`, Node `k3d-de-k3d-agent-1`

## 1. Triệu chứng và ảnh hưởng

- **Nguồn:**
  - `client-cross` (IP `10.42.3.85`, Node `k3d-de-k3d-agent-0`)
  - `client-local` (IP `10.42.1.70`, Node `k3d-de-k3d-agent-1`)
- **Đích:** Service `feature-cache` (`10.43.34.185:80`) / Pod `feature-cache` (`10.42.1.69:8080`, Node `k3d-de-k3d-agent-1`)
- **Triệu chứng:**
  - `client-cross` gọi sang `feature-cache` bị treo rồi gặp lỗi `Connection timed out after 2000 milliseconds` (curl exit code 28).
  - `client-local` (cùng node với cache) gọi vào `feature-cache` thành công ngay lập tức (HTTP `200 OK`).
- **Phần vẫn chạy:**
  - DNS phân giải tên miền `feature-cache` ra IP `10.43.34.185` thành công trong ~1ms.
  - Pod `feature-cache` ở trạng thái `1/1 Running`, `Ready: true`.
  - Luồng nội bộ cùng node (`client-local`) hoạt động 100%.

## 2. Topology và baseline

```text
[ Node: agent-0 (192.168.155.3) ]          [ Node: agent-1 (192.168.155.4) ]
┌───────────────────────────────┐          ┌────────────────────────────────────────────────────────┐
│ [ Pod: client-cross ]         │          │ [ Pod: client-local ]            [ Pod: feature-cache ]│
│ IP: 10.42.3.85                │          │ IP: 10.42.1.70                   IP: 10.42.1.69:8080   │
└──────────────┬────────────────┘          └───────────┬───────────────────────────────▲────────────┘
               │                                       │                               │
               ▼ (Đóng gói Flannel VXLAN)              ▼ (Qua bridge cbr0 nội bộ)      │
       [ UDP dpt:8472 ]                                └───────────────────────────────┤
               │                                                                       │
               ▼ (Mạng Host)                                                           │
       192.168.155.4:8472 ──► [ iptables INPUT: DROP s=192.168.155.3 dpt:8472 ] ──X Gói tin bị DROP!
```

## 3. Timeline điều tra

| Thời điểm | Giả thuyết / dự đoán | Phép đo và nơi chạy | Output thật / bằng chứng | Kết luận / bước tiếp |
|---|---|---|---|---|
| 22:02 | Khảo sát trạng thái Control Plane | `kubectl -n nfk-26b-case2 get pods,svc -o wide` | 3 Pod Running 1/1; Service có Endpoint `10.42.1.69:8080` | Control Plane hoàn toàn bình thường |
| 22:16 | Bắt chữ ký lỗi tại client khác Node | `kubectl -n nfk-26b-case2 exec client-cross -- curl -v --connect-timeout 2 http://feature-cache` | `Connection timed out after 2011 ms (exit code 28)` | Chữ ký là Silent DROP, không phải TCP RST; DNS đã resolve xong |
| 22:20 | Đối chứng với client cùng Node | `kubectl -n nfk-26b-case2 exec client-local -- curl -v --connect-timeout 2 http://feature-cache` | `HTTP/1.0 200 OK` | Loại trừ tầng ứng dụng backend và socket lắng nghe |
| 22:37 | Control test bỏ qua Service VIP | `kubectl -n nfk-26b-case2 exec client-cross -- curl -v --connect-timeout 2 http://10.42.1.69:8080` | `Connection timed out after 2001 ms` | Loại trừ tầng Service / Kube-proxy; đứt ở tầng liên Node |
| 22:38 | Soi firewall / iptables trên Node nhận (`agent-1`) | `docker exec k3d-de-k3d-agent-1 iptables -L INPUT -n -v --line-numbers` | `num 2: 2 pkts DROP udp -- 192.168.155.3 0.0.0.0/0 udp dpt:8472` | Bắt quả tang rule DROP chặn cổng Flannel VXLAN từ Node agent-0 |

## 4. Root cause đã kiểm chứng

- **Cơ chế gây lỗi:** CNI Flannel chạy chế độ VXLAN encapsulation (UDP dport 8472). Khi Pod trên `agent-0` gửi gói tin tới Pod trên `agent-1`, gói tin Pod-to-Pod được bọc trong gói UDP 8472 gửi tới IP host của `agent-1`. Trên Node `agent-1`, một rule iptables trong chain `INPUT` đã `DROP` toàn bộ lưu lượng UDP 8472 từ IP `192.168.155.3` (Host IP của `agent-0`).
- **Vì sao timeout?** Gói tin VXLAN bị kernel của `agent-1` âm thầm vứt bỏ (silent drop) trước khi kịp decapsulate chuyển tiếp vào veth của Pod `feature-cache`. Client không nhận được phản hồi nên retransmit SYN tới khi hết timeout (exit code 28).
- **Vì sao local-node vẫn chạy?** `client-local` và `feature-cache` cùng nằm trên `agent-1`. Gói tin đi trực tiếp qua Linux bridge nội bộ `cbr0`, hoàn toàn không qua interface vật lý của host và không bị bọc VXLAN, do đó không đi qua chain INPUT của host underlay.

## 5. Fix tối thiểu và rollback

- **Fix tối thiểu:** Xóa chính xác rule DROP trong chain INPUT trên node `agent-1` bằng lệnh:
  `docker exec k3d-de-k3d-agent-1 iptables -D INPUT -s 192.168.155.3 -p udp --dport 8472 -j DROP`
  Tuyệt đối không dùng `iptables -F` để tránh làm sập hạ tầng firewall/kube-proxy của node.
- **Rollback plan:** Thêm lại rule DROP nếu cần tái hiện kiểm thử:
  `docker exec k3d-de-k3d-agent-1 iptables -I INPUT 2 -s 192.168.155.3 -p udp --dport 8472 -j DROP`

## 6. Post-fix và regression checks

Đã thực hiện kiểm chứng thực tế tại bài 26b vào lúc 2026-11-17 22:42:
- [x] Luồng từng bị lỗi qua Service đã phục hồi:
  - `client-cross` gọi `http://feature-cache` -> HTTP/1.0 200 OK.
- [x] Luồng trực tiếp Pod IP liên Node đã thông:
  - `client-cross` gọi `http://10.42.1.69:8080` -> HTTP/1.0 200 OK.
- [x] Control test luồng nội bộ không bị ảnh hưởng:
  - `client-local` gọi `http://feature-cache` -> HTTP/1.0 200 OK.

## 7. Dọn dẹp và bàn giao

- Đã hoàn tất xử lý Case 2 và kiểm chứng đầy đủ.
- Dọn dẹp: Xóa toàn bộ namespace `nfk-26b-case2` (`kubectl delete namespace nfk-26b-case2`). Không để sót tài nguyên tạm.
