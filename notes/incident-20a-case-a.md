# Incident Report — Case A: Client không kết nối được Service nội bộ (Connection Refused)

- **Ngày/giờ, người điều tra:** 2026-11-09 14:55 (Asia/Ho_Chi_Minh), ssc1_4 (học viên) & Antigravity (gia sư)
- **Bài học / môi trường / phiên bản:** Bài 20a / k3d-de-k3d (K3s v1.33.6+k3s1, kube-proxy iptables mode)
- **Phạm vi lab và quyền được xác nhận:** Namespace `nfk-20a-a`, Service `order-service`, Pod `backend`, Pod `client`

---

## 1. Triệu chứng và ảnh hưởng

- **Nguồn:** Pod `client` (namespace `nfk-20a-a`, IP `10.42.3.44`)
- **Đích:** Service `order-service` (namespace `nfk-20a-a`, port 80)
- **Giao thức:** HTTP/TCP port 80
- **Triệu chứng lỗi:**
  ```text
  * Host order-service:80 was resolved.
  * IPv4: 10.43.182.146
  *   Trying 10.43.182.146:80...
  * connect to 10.43.182.146 port 80 from 10.42.3.44 port 32970 failed: Connection refused
  * Failed to connect to order-service port 80 after 1 ms: Could not connect to server
  curl: (7) Failed to connect to order-service port 80 after 1 ms: Could not connect to server
  ```
- **Tình trạng:** DNS phân giải tốt (0ms) ra ClusterIP `10.43.182.146`. Kết nối TCP handshake bị từ chối ngay lập tức (1ms).

---

## 2. Topology và baseline

- **Client:** Pod `client` chạy trong `nfk-20a-a`.
- **Target Service:** `order-service` (ClusterIP `10.43.182.146`, port 80, targetPort 8080).
- **Backend Pod:** `backend` (IP `10.42.1.33`), nhãn `app=order-api, env=dev`, container Python HTTP server listen port 8080.
- **Service selector:** Ban đầu cấu hình `app=order-api, env=production`.

---

## 3. Timeline điều tra

| Thời điểm | Giả thuyết / dự đoán | Phép đo và nơi chạy | Output thật / bằng chứng | Kết luận / bước tiếp |
|---|---|---|---|---|
| 14:45 | Tái hiện lỗi từ client | `kubectl exec client -- curl -v ... http://order-service` | `Connection refused` sau 1ms; DNS ra IP `10.43.182.146` | Loại trừ DNS; lỗi ở tầng Service / Data plane |
| 14:53 | Selector mismatch hoặc backend port sai | Kiểm tra nhãn Pod vs Service selector | Pod có `env=dev`, Service tìm `env=production` | Phát hiện lệch selector giữa Service và Pod |
| 14:55 | Sửa selector phục hồi kết nối | `kubectl patch svc order-service -p '{"spec":{"selector":{"env":"dev"}}}'` | `service/order-service patched` | Đã patch cấu hình Service |
| 14:55 | Kiểm tra EndpointSlice | `kubectl get endpointslices -l kubernetes.io/service-name=order-service` | EndpointSlice xuất hiện IP `10.42.1.33:8080` | EndpointSlice đã gom được Pod backend |
| 14:55 | Nghiệm thu sau fix | `kubectl exec client -- curl -v http://order-service` | `HTTP/1.0 200 OK` (Directory listing) | Sự cố được khắc phục hoàn toàn |

---

## 4. Root cause đã kiểm chứng

- **Cơ chế gây lỗi:**
  Service `order-service` được cấu hình selector `app=order-api, env=production`. Tuy nhiên, Pod backend duy nhất trong namespace chỉ mang nhãn `app=order-api, env=dev`. Do không khớp nhãn, EndpointSlice của Service rỗng (`0 endpoints`).
- **Tại sao lại bị `Connection refused` (1ms)?**
  Trong chế độ `kube-proxy` iptables, khi một Service không có endpoint nào khả dụng, kube-proxy chèn một rule `REJECT` trong chain `KUBE-SERVICES`:
  `-A KUBE-SERVICES -d <ClusterIP>/32 -p tcp --dport 80 -m comment --comment "nfk-20a-a/order-service has no endpoints" -j REJECT`
  Gói SYN từ client gửi tới ClusterIP lập tức bị kernel từ chối bằng TCP RST / ICMP Port Unreachable.
- **Bác bỏ các giả thuyết khác:**
  - *Lỗi DNS:* Bác bỏ vì hostname `order-service` phân giải thành công 0ms ra IP `10.43.182.146`.
  - *Lỗi Firewall DROP:* Bác bỏ vì lỗi là `Connection refused` (1ms), nếu do DROP thì client phải chịu `Timeout` (2s+).
  - *Lệch targetPort:* Bác bỏ vì sau khi sửa selector, Service tự định tuyến đúng vào targetPort 8080 và trả về 200 OK ngay lập tức.

---

## 5. Fix tối thiểu và rollback

- **Fix tối thiểu:** Cập nhật selector của Service `order-service` từ `env: production` sang `env: dev`:
  ```bash
  kubectl patch svc order-service -n nfk-20a-a -p '{"spec":{"selector":{"env":"dev"}}}'
  ```
- **Rollback (nếu cần hoàn nguyên):**
  ```bash
  kubectl patch svc order-service -n nfk-20a-a -p '{"spec":{"selector":{"env":"production"}}}'
  ```

---

## 6. Post-fix và regression checks

| Luồng kiểm tra | Trước fix | Sau fix | Bằng chứng | Ý nghĩa |
|---|---|---|---|---|
| `curl http://order-service` | Exit code 7 (`Connection refused`) | `HTTP/1.0 200 OK` | Nhận trọn vẹn HTML 760 bytes | Luồng chính đã phục hồi hoàn toàn |
| `EndpointSlice` status | 0 endpoints | 1 endpoint (`10.42.1.33:8080`) | `order-service-xgzj8` có endpoint `10.42.1.33` | Endpoint controller đã đồng bộ IP Pod |

---

## 7. Dọn dẹp và bàn giao

- Toàn bộ namespace `nfk-20a-a` đã được xóa sạch sẽ (`kubectl delete ns nfk-20a-a`).
- Không để lại tài nguyên dư thừa hay ảnh hưởng đến cụm.

---

## 8. Bài học và Mental Model

- Khi gặp `Connection refused` khi gọi Service trong Kubernetes, đừng vội kết luận là do ứng dụng chết hay lệch port. **0 endpoints trong EndpointSlice cũng gây ra `Connection refused`** do rule REJECT chủ động của kube-proxy.
- Kiểm tra `EndpointSlice` (`kubectl get endpointslices`) là bước đo lường rẻ nhất và phân loại nhanh nhất giữa lỗi ở Tầng Điều phối (Selector/Labels) và Tầng Thực thi ứng dụng (Port/Listener/Bind address).
