# 17a — Service, selector và EndpointSlice

**Buổi gốc:** 17 · **Thời lượng:** 30–45 phút.  
**Trước bài:** [16b](16b-cross-node-incident-va-cp4.md) và CP4 đạt.  
**Môi trường:** Kubernetes lab, hai backend có response nhận diện được và client Pod riêng.

## Mục tiêu

- Phân biệt Pod IP với ClusterIP và quan hệ Service → EndpointSlice → backend Pod.
- Đọc selector/labels và backend readiness thay vì suy từ tên Deployment.

## Mô hình cần hiểu

Trong bài dùng Service có selector, controller đối chiếu Pod labels trong cùng namespace để quản lý EndpointSlices. ClusterIP là địa chỉ ảo của Service, không phải một Pod cần tìm trên node. Endpoint metadata mô tả đích để data plane sử dụng; nó không tự chứng minh listener hay kết nối thật hoạt động.

## Dự đoán trước khi chạy

1. Service tồn tại nhưng không có Pod khớp selector thì traffic sẽ đi tới backend nào?
2. Thay IP một Pod có bắt client dùng ClusterIP phải đổi địa chỉ cấu hình ngay không?

## Challenge

1. Triển khai ít nhất hai backend Pods lab có response phân biệt được, xác nhận listener và readiness.
2. Tạo ClusterIP Service có selector phù hợp. Trước khi apply, dự đoán backend addresses/ports trong EndpointSlice.
3. Xem Service, Pod labels và `kubectl get endpointslice` với phạm vi namespace/Service đúng; nối từng address với Pod thật.
4. Gọi trực tiếp từng Pod IP rồi gọi ClusterIP, ghi backend thực nhận request.

## Kiểm chứng và đối chứng

Không khẳng định mỗi request luân phiên chính xác qua từng backend: connection reuse, session affinity và data plane có thể ảnh hưởng phân phối. Dùng các kết nối mới khi cần so sánh và ghi những gì thực sự thấy.

## Bằng chứng và điều kiện đạt

- [ ] Có manifest/selector, labels và EndpointSlice được đối chiếu với backend Pods.
- [ ] Có request Pod IP và ClusterIP với response nhận diện backend.
- [ ] Giải thích Service IP khác Pod IP, lưu [notes](../templates/LESSON_NOTE.md) và tài nguyên giữ cho 17b.

**Tiếp theo:** [17b — Selector mismatch](17b-selector-mismatch.md).
