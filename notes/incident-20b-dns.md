# Incident Report — Case C (Bài 20b): Client không phân giải được tên miền Service do bẫy dnsPolicy: Default

- **Ngày/giờ, người điều tra:** 2026-11-09 15:42 (Asia/Ho_Chi_Minh), ssc1_4 (học viên) & Antigravity (gia sư)
- **Bài học / môi trường / phiên bản:** Bài 20b / k3d-de-k3d (K3s v1.33.6+k3s1, CoreDNS)
- **Phạm vi lab và quyền được xác nhận:** Namespace `nfk-20b`, Pod `analytics-client`, Service `analytics-service`, Pod `analytics-backend`

---

## 1. Triệu chứng và ảnh hưởng

- **Nguồn:** Pod `analytics-client` (namespace `nfk-20b`)
- **Đích:** Service `analytics-service` (namespace `nfk-20b`, port 80)
- **Giao thức:** HTTP/TCP port 80
- **Triệu chứng lỗi:**
  ```text
  * Could not resolve host: analytics-service
  * shutting down connection #0
  curl: (6) Could not resolve host: analytics-service
  ```
- **Tình trạng:** Quá trình phân giải tên miền thất bại hoàn toàn ngay từ bước đầu tiên (exit code 6), không thể thiết lập kết nối TCP vì không tìm thấy địa chỉ IP.

---

## 2. Topology và baseline

- **Client:** Pod `analytics-client` chạy trong `nfk-20b`.
- **Target Service:** `analytics-service` (ClusterIP `10.43.190.138`, port 80, targetPort 8080).
- **Backend Pod:** `analytics-backend` (IP `10.42.1.35`), container Python HTTP server listen port 8080.
- **Control test Baseline:** Gọi trực tiếp bằng ClusterIP `curl http://10.43.190.138:80` trả về `HTTP/1.0 200 OK` ngay lập tức!
  $\rightarrow$ Khẳng định Data Plane, Service routing, kube-proxy và Backend Pod hoàn toàn khỏe mạnh.

---

## 3. Timeline điều tra

| Thời điểm | Giả thuyết / dự đoán | Phép đo và nơi chạy | Output thật / bằng chứng | Kết luận / bước tiếp |
|---|---|---|---|---|
| 15:25 | Tái hiện lỗi từ client | `kubectl exec analytics-client -- curl -v ... http://analytics-service` | `Could not resolve host: analytics-service` (exit code 6) | Tầng phân giải tên miền bị đứt gãy |
| 15:25 | Control test Data Plane | `kubectl exec analytics-client -- curl -v http://10.43.190.138:80` | `HTTP/1.0 200 OK` (760 bytes HTML) | Data Plane 100% bình thường; lỗi cô lập ở tầng DNS |
| 15:40 | Kiểm tra cấu hình Resolver | `kubectl exec analytics-client -- cat /etc/resolv.conf` | `nameserver 192.168.155.1`, `options ndots:0`, không có search domain | Nameserver trỏ ra Node ngoài; thiếu hoàn toàn cấu hình cluster DNS |
| 15:41 | Soi manifest Pod client | `kubectl get pod analytics-client -o jsonpath='{.spec.dnsPolicy}'` | `Default` | Phát hiện Root Cause: Pod cấu hình `dnsPolicy: Default` |
| 15:42 | Sửa tối thiểu và recreate Pod | `sed 's/dnsPolicy: Default/dnsPolicy: ClusterFirst/' \| kubectl replace --force` | Pod replaced, Ready condition met | Khôi phục chính sách DNS chuẩn của K8s |
| 15:42 | Kiểm tra lại resolver | `kubectl exec analytics-client -- cat /etc/resolv.conf` | `nameserver 10.43.0.10`, `search nfk-20b.svc...`, `ndots:5` | Resolver đã nhận đúng CoreDNS |
| 15:42 | Nghiệm thu sau fix | `kubectl exec analytics-client -- curl -v http://analytics-service` | `Host resolved to 10.43.190.138` $\rightarrow$ `HTTP/1.0 200 OK` | Sự cố được giải quyết triệt để |

---

## 4. Root cause đã kiểm chứng

- **Cơ chế gây lỗi:**
  Pod `analytics-client` được khai báo trường `spec.dnsPolicy: Default`. Trong Kubernetes, giá trị `Default` không phải là mặc định của cluster, mà có nghĩa là **kế thừa file `/etc/resolv.conf` của chính Node máy chủ hạ tầng** (`192.168.155.1`).
  Node hạ tầng chỉ biết DNS internet hoặc mạng cục bộ bên ngoài, hoàn toàn không biết gì về hậu tố `.svc.cluster.local` hay tên các Service nội bộ. Khi Pod truy vấn `analytics-service`, DNS của Node trả về `NXDOMAIN`.
- **Bác bỏ các giả thuyết khác:**
  - *Lỗi CoreDNS cụm bị sập:* Bác bỏ vì sau khi sửa client trỏ về CoreDNS `10.43.0.10`, truy vấn phân giải ngay lập tức ra `10.43.190.138`.
  - *Lỗi Service selector / targetPort / Pod chết:* Bác bỏ vì control test gọi thẳng ClusterIP `10.43.190.138:80` đã chạy thành công `200 OK` từ trước khi sửa DNS.

---

## 5. Fix tối thiểu và rollback

- **Fix tối thiểu:** Recreate Pod với `dnsPolicy: ClusterFirst` (hoặc bỏ trường này để nhận giá trị mặc định của cụm):
  ```bash
  kubectl get pod analytics-client -n nfk-20b -o yaml | sed 's/dnsPolicy: Default/dnsPolicy: ClusterFirst/' | kubectl replace --force -f -
  ```
- **Rollback (nếu cần hoàn nguyên):** Đổi lại `dnsPolicy: Default` và replace Pod.

---

## 6. Post-fix và regression checks

| Luồng kiểm tra | Trước fix | Sau fix | Bằng chứng | Ý nghĩa |
|---|---|---|---|---|
| `curl http://analytics-service` | Exit code 6 (`Could not resolve host`) | `HTTP/1.0 200 OK` | Host resolved to `10.43.190.138`, HTML nhận đủ 760B | Tên miền phân giải và kết nối thành công |
| `/etc/resolv.conf` | `nameserver 192.168.155.1` (Node) | `nameserver 10.43.0.10` (CoreDNS) | Có đủ search domain `nfk-20b.svc...` và `ndots:5` | Pod kế thừa đúng kiến trúc cluster DNS |
| Control test ClusterIP | `HTTP/1.0 200 OK` | `HTTP/1.0 200 OK` | Vẫn phản hồi ổn định | Luồng data plane không bị ảnh hưởng |

---

## 7. Dọn dẹp và bàn giao

- Toàn bộ namespace `nfk-20b` đã được xóa sạch sẽ (`kubectl delete ns nfk-20b`).
- Cụm k3d trở lại trạng thái sạch hoàn toàn.

---

## 8. Bài học và Mental Model

- **Bẫy thuật ngữ Kubernetes `dnsPolicy`:**
  - `ClusterFirst`: Mặc định chuẩn của Kubernetes (dùng CoreDNS `10.43.0.10`).
  - `Default`: Kế thừa của Host Node (`192.168.155.1`), làm hỏng toàn bộ kết nối tới Service nội bộ theo tên.
- **Giá trị của Control Test:** Khi một kết nối thất bại, phép thử gọi trực tiếp bằng ClusterIP là nhát dao sắc bén nhất để tách biệt rạch ròi: Lỗi thuộc về tầng DNS hay tầng Data Plane/Service.
