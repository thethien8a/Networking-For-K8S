# 22a — Ingress resource, controller và Host/path routing

**Buổi gốc:** 22 · **Thời lượng:** 45–60 phút.  
**Trước bài:** [21b](21b-nodeport-hostport-kind-mapping.md).  
**Môi trường:** Ingress Controller phù hợp version đã được chấp thuận/cài trong lab; hai web backends phân biệt được.

## Mục tiêu

- Phân biệt khai báo Ingress với controller thực thi và đường expose controller.
- Chứng minh hai ứng dụng được route đúng theo Host/path qua một controller.

## Mô hình cần hiểu

Ingress resource không tự tạo một reverse proxy chạy được. Controller đọc resource/class phù hợp rồi thực hiện L7 routing theo triển khai của nó; client vẫn cần tới được endpoint của controller qua mạng. Không mặc định mọi controller dùng chung annotations hoặc chuyển tiếp qua ClusterIP theo cùng một cách.

## Dự đoán trước khi chạy

1. Có Ingress YAML nhưng không có controller/class phù hợp thì request được xử lý bởi ai?
2. Gọi đúng controller IP nhưng Host sai có khác với không connect được controller không?

## Challenge

1. Xác định controller, version, IngressClass, cách client tới endpoint và phạm vi lab. Chưa có controller thì ghi chuẩn bị/blocker, không tự cài lên cluster dùng chung.
2. Deploy hai ứng dụng có response khác nhau và Services hoạt động độc lập.
3. Tạo rules dùng hai hostname hoặc paths theo controller/version thật; dự đoán request nào tới backend nào.
4. Test Host/path đúng, Host sai và path không khớp bằng request có kiểm soát; đối chiếu controller logs/config và backend identity.

## Break & Fix

Nhận case Host rule hoặc backend Service sai; xác định response đến từ controller hay app trước khi giải nghĩa 404/502. Các status này không có một root cause duy nhất cho mọi controller. Không đổi cả DNS, Ingress và backend cùng lúc.

## Bằng chứng và điều kiện đạt

- [ ] Có hai backend routed đúng, matrix Host/path gồm control không khớp và logs/output.
- [ ] Vẽ client → controller → backend Service mapping → Pod theo triển khai thật, phân biệt lookup Service với VIP forwarding.
- [ ] Có fix tối thiểu, manifest rollback và [notes](../templates/LESSON_NOTE.md), tài nguyên giữ cho 22b rõ ràng.

**Tiếp theo:** [22b — TLS termination](22b-ingress-tls-termination.md).
