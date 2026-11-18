# Case 1 — Service metadata-store Connection Refused dù Pod và Endpoints đều Ready

- Ngày/giờ, người điều tra: 2026-11-17 21:40 (Asia/Ho_Chi_Minh), Học viên & LLM Gia sư
- Bài học / môi trường / phiên bản: Bài 26a / Mac k3d-de-k3d / K3s v1.33.6+k3s1, Flannel CNI, iptables proxy-mode
- Phạm vi lab và quyền được xác nhận: Namespace `nfk-26a-case1`, Pod `data-ingest`, Pod `metadata-store`, Service `metadata-store`

## 1. Triệu chứng và ảnh hưởng

- **Nguồn:** Pod `data-ingest` (IP `10.42.3.84`, Node `k3d-de-k3d-agent-0`)
- **Đích:** Service `metadata-store` (`10.43.131.242:80`) / Pod `metadata-store` (`10.42.1.67:8080`, Node `k3d-de-k3d-agent-1`)
- **Giao thức/Port:** HTTP trên TCP port 80 / 8080
- **Triệu chứng:** Client gọi Service bị `Connection refused` ngay lập tức (~1-2ms, curl exit code 7).
- **Phần vẫn chạy:**
  - DNS phân giải tên miền `metadata-store` ra ClusterIP `10.43.131.242` thành công trong ~1ms.
  - `kubectl get pods -n nfk-26a-case1` hiển thị `metadata-store` ở trạng thái `1/1 Running`, `Ready: true`.
  - `kubectl get endpoints -n nfk-26a-case1` hiển thị `10.42.1.67:8080`.
- **Cách tái hiện:**
  `kubectl -n nfk-26a-case1 exec data-ingest -- curl -v --connect-timeout 3 http://metadata-store`

## 2. Topology và baseline

```text
[ data-ingest ] (IP: 10.42.3.84 on agent-0)
      │
      ▼ (HTTP request via ClusterIP 10.43.131.242:80)
[ Service: metadata-store ] (EndpointSlice: 10.42.1.67:8080)
      │
      ▼ (Flannel VXLAN cross-node)
[ metadata-store ] (IP: 10.42.1.67 on agent-1)
      ├── eth0 (10.42.1.67) ──► [ NO LISTENER ON 10.42.1.67:8080 / 0.0.0.0:8080 ] ──► Sends TCP RST!
      └── lo   (127.0.0.1)  ──► [ LISTEN 127.0.0.1:8080 ] ◄── Exec ReadinessProbe HTTP 200 OK
```

## 3. Timeline điều tra

| Thời điểm | Giả thuyết / dự đoán | Phép đo và nơi chạy | Output thật / bằng chứng | Kết luận / bước tiếp |
|---|---|---|---|---|
| 21:12 | Khởi tạo hiện trường và kiểm tra Control Plane | `kubectl -n nfk-26a-case1 get pods,svc,endpoints -o wide` | Pod Running 1/1, Ready: true; Service có Endpoint `10.42.1.67:8080` | Control plane metadata hoàn toàn bình thường |
| 21:15 | Client không kết nối được; cần bắt chữ ký lỗi | `kubectl -n nfk-26a-case1 exec data-ingest -- curl -v --connect-timeout 3 http://metadata-store` | `Host metadata-store:80 was resolved -> 10.43.131.242`, `Connection refused after 1 ms (exit code 7)` | DNS thành công; loại trừ DROP/Firewall timeout; lỗi là TCP RST |
| 21:38 | Giả thuyết A: Lỗi Service/Kube-proxy; Giả thuyết B: Lỗi chính Pod backend | Control test: `kubectl -n nfk-26a-case1 exec data-ingest -- curl -v --connect-timeout 3 http://10.42.1.67:8080` | `Trying 10.42.1.67:8080... Connection refused after 0 ms` | Gọi thẳng Pod IP vẫn bị TCP RST -> Loại trừ Service, Kube-proxy, EndpointSlice |
| 21:40 | Kiểm tra Socket lắng nghe trên Pod backend | `kubectl -n nfk-26a-case1 exec metadata-store -- netstat -tlpn` | `tcp 0 0 127.0.0.1:8080 0.0.0.0:* LISTEN 1/python3` | Bắt quả tang tiến trình chỉ bind trên `127.0.0.1:8080`, không nghe trên `0.0.0.0:8080` |
| 21:42 | Đối chứng vì sao ReadinessProbe vẫn báo Ready | `kubectl -n nfk-26a-case1 exec metadata-store -- python3 -c "import urllib.request; print(urllib.request.urlopen('http://127.0.0.1:8080').status)"` | Trả về `200` | Probe chạy nội bộ trong netns Pod qua loopback nên thành công, đánh lừa Kubelet |

## 4. Root cause đã kiểm chứng

- **Cơ chế gây lỗi:** Tiến trình backend bên trong Pod `metadata-store` chỉ bind vào địa chỉ loopback `127.0.0.1:8080`.
- **Vì sao Control Plane xanh?** Kubelet cấu hình `readinessProbe` dạng `exec` chạy curl/python nội bộ trong Network Namespace của Pod. Probe truy vấn qua interface `lo` (`127.0.0.1`), nên nhận được HTTP 200 OK. Kubelet báo cáo Pod `Ready`, và API Server đưa Pod IP vào `EndpointSlice`.
- **Vì sao Data Plane chết?** Client bên ngoài (`data-ingest`) gửi gói tin tới IP `10.42.1.67` đi vào interface `eth0` của Pod. Vì không có tiến trình nào lắng nghe trên `0.0.0.0:8080` hay `10.42.1.67:8080`, Linux Kernel của Pod lập tức gửi trả gói tin **TCP RST**, gây ra lỗi `Connection refused` trong 1ms.
- **Bác bỏ giả thuyết đối lập:**
  - Không phải lỗi DNS vì client đã resolve đúng ClusterIP `10.43.131.242`.
  - Không phải lỗi NetworkPolicy hay Firewall DROP vì kết nối không bị timeout mà bị refused ngay lập tức.
  - Không phải lỗi Kube-proxy / Service targetPort vì gọi trực tiếp Pod IP cũng bị `Connection refused`.

## 5. Fix tối thiểu và rollback

- **Fix tối thiểu:** Cập nhật manifest của Pod `metadata-store`, thay đổi tham số bind socket của tiến trình HTTP server từ `('127.0.0.1', 8080)` thành `('0.0.0.0', 8080)` (`INADDR_ANY`). Giữ nguyên cổng container 8080 và Service selector `app=metadata-store`.
- **Rollback plan:** Trong trường hợp phát sinh lỗi hồi quy hoặc cần tái hiện lại sự cố, rollback bằng cách thay đổi bind address trở lại `('127.0.0.1', 8080)`.

## 6. Post-fix và regression checks

Đã thực hiện kiểm chứng thực tế tại bài 26b vào lúc 2026-11-17 21:59:
- [x] Soi lại `netstat -tlpn` hiển thị `0.0.0.0:8080 LISTEN`:
  - `tcp 0 0 0.0.0.0:8080 0.0.0.0:* LISTEN 1/python3`
- [x] Gọi qua Service `http://metadata-store` trả về `200 OK`:
  - `curl -v --connect-timeout 3 http://metadata-store` -> `HTTP/1.0 200 OK`, nhận đầy đủ HTML payload.
- [x] Gọi trực tiếp Pod IP trả về `200 OK` (Control test):
  - `curl -v --connect-timeout 3 http://10.42.1.68:8080` -> `HTTP/1.0 200 OK`, không còn bị TCP RST.
- [x] Probe nội bộ của Pod: ReadinessProbe vẫn `200 OK`, Pod duy trì `1/1 Ready`.

## 7. Dọn dẹp và bàn giao

- Đã hoàn tất xử lý Case 1 và kiểm chứng đầy đủ.
- Dọn dẹp: Đã xóa toàn bộ namespace `nfk-26a-case1` (`kubectl delete namespace nfk-26a-case1`). Không để sót tài nguyên rác.

## 8. Bài học và điểm chưa rõ

- **Tín hiệu phát hiện sớm:** Lỗi `Connection refused` (TCP RST) xảy ra tức thì cả khi gọi qua Service VIP lẫn gọi trực tiếp Pod IP.
- **Phép đo hiệu quả nhất:**
  1. Control test gọi trực tiếp Pod IP để phân lập tầng Service vs tầng Pod.
  2. Lệnh `netstat -tlpn` bắt quả tang IP cụ thể trong cột `Local Address`.
