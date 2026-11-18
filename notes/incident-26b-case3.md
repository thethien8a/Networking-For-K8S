# Case 3 — Dịch vụ metrics-collector bị từ chối kết nối do NetworkPolicy sai nhãn selector

- Ngày/giờ, người điều tra: 2026-11-17 23:39 (Asia/Ho_Chi_Minh), Học viên & LLM Gia sư
- Bài học / môi trường / phiên bản: Bài 26b / Mac k3d-de-k3d / K3s v1.33.6+k3s1, Kube-router NetworkPolicy controller
- Phạm vi lab và quyền được xác nhận: Namespace `nfk-26b-case3`, Pod `metrics-collector`, Pod `prometheus-scraper`, Pod `analytics-client`, NetworkPolicy `metrics-isolation`

## 1. Triệu chứng và ảnh hưởng

- **Nguồn:**
  - `prometheus-scraper` (IP `10.42.3.86`, mang nhãn `app=prometheus,role=scraper`) — Client hợp lệ được ủy quyền thu thập metrics.
  - `analytics-client` (IP `10.42.1.72`, mang nhãn `app=analytics,role=query`) — Client không phận sự (Negative control).
- **Đích:** Service `metrics-collector` (`10.43.91.124:9090`) / Pod `metrics-collector` (`10.42.1.71:9090`).
- **Triệu chứng:**
  - Client hợp lệ `prometheus-scraper` kết nối tới `metrics-collector:9090` bị từ chối tức thì: `Connection refused after 0-1 ms` (curl exit code 7).
- **Phần vẫn chạy:**
  - DNS phân giải tên miền Service `metrics-collector` thành công ra ClusterIP `10.43.91.124`.
  - Tiến trình backend trong Pod `metrics-collector` lắng nghe chuẩn xác trên wildcard: `0.0.0.0:9090 LISTEN` (`netstat -tlpn`).
  - Pod backend ở trạng thái `1/1 Running`, `Ready: true`.

## 2. Topology và baseline

```text
[ Namespace: nfk-26b-case3 ]

[ Pod: prometheus-scraper ] (role=scraper)
          │
          ▼ (HTTP GET :9090)
[ NetworkPolicy: metrics-isolation ] ──► podSelector: app=metrics-collector (ISOLATED)
          │                              ingress: from [ role=metric-scraper ] ◄── Mismatch label!
          │                              └──► Kube-router REJECT với ICMP port-unreachable (exit code 7)
          ▼
[ Pod: metrics-collector ] (app=metrics-collector, LISTEN 0.0.0.0:9090)
          ▲
          │ (HTTP GET :9090)
[ Pod: analytics-client ] (role=query) ──► Bị NetworkPolicy REJECT (Negative control)
```

## 3. Timeline điều tra

| Thời điểm | Giả thuyết / dự đoán | Phép đo và nơi chạy | Output thật / bằng chứng | Kết luận / bước tiếp |
|---|---|---|---|---|
| 22:44 | Khảo sát trạng thái Control Plane và Socket backend | `kubectl -n nfk-26b-case3 exec metrics-collector -- netstat -tlpn` | `tcp 0 0 0.0.0.0:9090 0.0.0.0:* LISTEN` | Ứng dụng bind chuẩn, loại trừ lỗi socket/loopback như Case 1 |
| 23:36 | Bắt chữ ký lỗi tại client scraper | `kubectl -n nfk-26b-case3 exec prometheus-scraper -- curl -v --connect-timeout 2 http://metrics-collector:9090` | `Failed to connect after 1 ms: Connection refused (exit code 7)` | Chữ ký từ chối kết nối tức thì |
| 23:37 | Kiểm tra chính sách mạng NetworkPolicy | `kubectl -n nfk-26b-case3 get netpol -o yaml` | `from: [podSelector: matchLabels: {role: metric-scraper}]` | Phát hiện nhãn quy định trong policy là `role: metric-scraper` |
| 23:38 | Đối chiếu nhãn thực tế của Pod client | `kubectl -n nfk-26b-case3 get pod prometheus-scraper --show-labels` | `LABELS: app=prometheus,role=scraper` | Bắt quả tang nhãn Pod là `role=scraper`, không khớp với policy |

## 4. Root cause đã kiểm chứng

- **Cơ chế gây lỗi:** Khi NetworkPolicy `metrics-isolation` áp dụng lên Pod `metrics-collector`, Pod chuyển sang chế độ **Isolated**. Theo quy tắc Default Deny Ingress, mọi lưu lượng Ingress không khớp với whitelist sẽ bị chặn. Bộ điều khiển Kube-router trong K3s cài đặt rule iptables gửi gói tin `REJECT reject-with icmp-port-unreachable` khi traffic vi phạm policy, làm client nhận lỗi `Connection refused` ngay lập tức.
- **Điểm sai sót:** NetworkPolicy khai báo nhãn ingress là `role: metric-scraper` trong khi Pod client mang nhãn `role: scraper`.

## 5. Fix tối thiểu và rollback

- **Nguyên tắc fix tối thiểu:** Chỉ sửa đúng trường nhãn `role: scraper` trong NetworkPolicy. Tuyệt đối không xóa NetworkPolicy hoặc mở `ingress: [{}]` (allow all) vì sẽ vi phạm ranh giới bảo mật cụm.
- **Lệnh áp dụng:**
  ```bash
  kubectl -n nfk-26b-case3 patch netpol metrics-isolation --type=merge -p '{"spec":{"ingress":[{"from":[{"podSelector":{"matchLabels":{"role":"scraper"}}}],"ports":[{"port":9090,"protocol":"TCP"}]}]}}'
  ```
- **Rollback plan:** Patch lại nhãn về `role: metric-scraper` nếu cần tái hiện:
  ```bash
  kubectl -n nfk-26b-case3 patch netpol metrics-isolation --type=merge -p '{"spec":{"ingress":[{"from":[{"podSelector":{"matchLabels":{"role":"metric-scraper"}}}],"ports":[{"port":9090,"protocol":"TCP"}]}]}}'
  ```

## 6. Post-fix và regression checks

Đã thực hiện kiểm chứng thực tế tại bài 26b vào lúc 2026-11-17 23:39:
- [x] **Positive Check (Client hợp lệ):**
  - `prometheus-scraper` gọi `http://metrics-collector:9090` -> `HTTP/1.0 200 OK`, nhận đầy đủ HTML payload.
- [x] **Negative Control Check (Client không phận sự):**
  - `analytics-client` (`role=query`) gọi `http://metrics-collector:9090` -> `Failed to connect: Connection refused after 0 ms (exit code 7)`.
  - Lưu lượng không phận sự vẫn bị chặn 100%, bảo đảm fix không mở rộng phạm vi ngoài ý định.

## 7. Dọn dẹp và bàn giao

- Đã hoàn tất xử lý Case 3 và kiểm chứng đầy đủ cả 2 chiều (Positive & Negative Control).
- Dọn dẹp: Xóa toàn bộ namespace `nfk-26b-case3` (`kubectl delete namespace nfk-26b-case3`). Không để sót tài nguyên tạm.
