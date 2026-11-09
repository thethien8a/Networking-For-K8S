# Incident Report — Case B: Client không kết nối được Service do Lệch TargetPort

- **Ngày/giờ, người điều tra:** 2026-11-09 15:16 (Asia/Ho_Chi_Minh), ssc1_4 (học viên) & Antigravity (gia sư)
- **Bài học / môi trường / phiên bản:** Bài 20a / k3d-de-k3d (K3s v1.33.6+k3s1, kube-proxy iptables mode)
- **Phạm vi lab và quyền được xác nhận:** Namespace `nfk-20a-b`, Service `data-service`, Pod `data-api`, Pod `client`

---

## 1. Triệu chứng và ảnh hưởng

- **Nguồn:** Pod `client` (namespace `nfk-20a-b`, IP `10.42.3.45`)
- **Đích:** Service `data-service` (namespace `nfk-20a-b`, port 80)
- **Giao thức:** HTTP/TCP port 80
- **Triệu chứng lỗi:**
  ```text
  * Host data-service:80 was resolved.
  * IPv4: 10.43.122.253
  *   Trying 10.43.122.253:80...
  * connect to 10.43.122.253 port 80 from 10.42.3.45 port 37236 failed: Connection refused
  * Failed to connect to data-service port 80 after 1 ms: Could not connect to server
  curl: (7) Failed to connect to data-service port 80 after 1 ms: Could not connect to server
  ```
- **Tình trạng:** DNS phân giải tốt (0ms) ra ClusterIP `10.43.122.253`. Kết nối TCP handshake bị từ chối ngay lập tức (1ms).

---

## 2. Topology và baseline

- **Client:** Pod `client` chạy trong `nfk-20a-b`.
- **Target Service:** `data-service` (ClusterIP `10.43.122.253`, port 80, targetPort 9000).
- **Backend Pod:** `data-api` (IP `10.42.1.34`), nhãn `app=data-api`, container Python HTTP server listen port 8080.
- **Service selector:** Khớp nhãn `app=data-api`.
- **EndpointSlice:** `data-service-wvxbb` có endpoint `10.42.1.34:9000`.

---

## 3. Timeline điều tra

| Thời điểm | Giả thuyết / dự đoán | Phép đo và nơi chạy | Output thật / bằng chứng | Kết luận / bước tiếp |
|---|---|---|---|---|
| 14:56 | Tái hiện lỗi từ client | `kubectl exec client -- curl -v ... http://data-service` | `Connection refused` sau 1ms; DNS ra IP `10.43.122.253` | Loại trừ DNS; lỗi ở tầng Data plane |
| 14:56 | Kiểm tra EndpointSlice | `kubectl get endpointslices -l kubernetes.io/service-name=data-service` | `PORTS: 9000, ENDPOINTS: 10.42.1.34` | EndpointSlice CÓ IP (khác hoàn toàn Case A); loại trừ selector mismatch |
| 15:15 | Kiểm tra socket thực tế bên trong Pod | `kubectl exec data-api -- netstat -tlpn` | `tcp 0 0 0.0.0.0:8080 LISTEN 1/python` | Process Python đang lắng nghe trên cổng 8080, không phải 9000 |
| 15:15 | So sánh Service targetPort | `kubectl describe svc data-service` | `TargetPort: 9000/TCP`, `Endpoints: 10.42.1.34:9000` | Xác nhận Root Cause: targetPort (9000) lệch so với listener (8080) |
| 15:16 | Sửa targetPort phục hồi kết nối | `kubectl patch svc data-service -p '{"spec":{"ports":[{"port":80,"targetPort":8080}]}}'` | `service/data-service patched` | Đã patch cấu hình Service |
| 15:16 | Kiểm tra EndpointSlice | `kubectl get endpointslices -l kubernetes.io/service-name=data-service` | `PORTS: 8080, ENDPOINTS: 10.42.1.34` | EndpointSlice đã đổi port đích sang 8080 |
| 15:16 | Nghiệm thu sau fix | `kubectl exec client -- curl -v http://data-service` | `HTTP/1.0 200 OK` (Directory listing) | Sự cố được khắc phục hoàn toàn |

---

## 4. Root cause đã kiểm chứng

- **Cơ chế gây lỗi:**
  Service `data-service` được cấu hình `targetPort: 9000`. Khi kube-proxy thực hiện DNAT từ ClusterIP `10.43.122.253:80`, gói tin SYN được chuyển tiếp đến `10.42.1.34:9000`. Tuy nhiên, bên trong Pod `data-api`, tiến trình Python chỉ mở socket lắng nghe tại `0.0.0.0:8080`.
  Khi kernel của Pod `data-api` nhận gói tin SYN gửi vào cổng 9000 mà không tìm thấy bất kỳ socket nào trong bảng lắng nghe TCP (`LISTEN`), kernel của Pod lập tức gửi trả gói TCP RST về cho client.
- **Tại sao EndpointSlice vẫn có IP và Ready?**
  Kubernetes EndpointSlice đánh dấu Pod là `Ready: true` dựa trên Pod Readiness Probe (hoặc mặc định container Running khi không có probe). EndpointSlice chỉ mapping IP Pod với `targetPort` khai báo trong Service YAML, nó **không tự động kiểm tra xem cổng đó có ứng dụng lắng nghe hay không**.
- **Bác bỏ các giả thuyết khác:**
  - *Lệch selector:* Bác bỏ vì EndpointSlice đã gom đúng IP Pod `10.42.1.34`.
  - *Lỗi bind address (localhost):* Bác bỏ vì `netstat -tlpn` xác nhận Python bind `0.0.0.0:8080` (wildcard), không phải `127.0.0.1`.
  - *Tường lửa DROP:* Bác bỏ vì client nhận phản hồi tức thì sau 1ms (`Connection refused`), không phải timeout.

---

## 5. Fix tối thiểu và rollback

- **Fix tối thiểu:** Sửa `targetPort` của Service `data-service` từ `9000` sang `8080`:
  ```bash
  kubectl patch svc data-service -n nfk-20a-b -p '{"spec":{"ports":[{"port":80,"targetPort":8080}]}}'
  ```
- **Rollback (nếu cần hoàn nguyên):**
  ```bash
  kubectl patch svc data-service -n nfk-20a-b -p '{"spec":{"ports":[{"port":80,"targetPort":9000}]}}'
  ```

---

## 6. Post-fix và regression checks

| Luồng kiểm tra | Trước fix | Sau fix | Bằng chứng | Ý nghĩa |
|---|---|---|---|---|
| `curl http://data-service` | Exit code 7 (`Connection refused`) | `HTTP/1.0 200 OK` | Nhận trọn vẹn HTML 760 bytes | Luồng chính đã phục hồi hoàn toàn |
| `EndpointSlice` ports | `PORTS: 9000` | `PORTS: 8080` | `data-service-wvxbb` có port 8080 | Endpoint controller đã cập nhật port chính xác |

---

## 7. Dọn dẹp và bàn giao

- Toàn bộ namespace `nfk-20a-b` đã được xóa sạch sẽ (`kubectl delete ns nfk-20a-b`).
- Cluster trở lại trạng thái sạch.

---

## 8. Bài học và Mental Model

- **Bẫy EndpointSlice Ready:** EndpointSlice có IP và hiển thị `Ready: true` **không đảm bảo** rằng ứng dụng sẽ phục vụ được traffic nếu `targetPort` bị lệch.
- Phép đo quyết định khi nghi ngờ lỗi tầng L4/Socket là chạy `netstat -tlpn` (hoặc `ss -tlpn`) ngay trong container backend để xem chính xác socket đang bind ở IP nào và cổng nào.
