# 17b — Service không có backend: điều tra selector mismatch

**Buổi gốc:** 17 · **Thời lượng:** 30–45 phút.  
**Trước bài:** [17a](17a-service-selector-endpointslice.md).  
**Môi trường:** Service/backend lab riêng, không sửa labels của ứng dụng dùng chung.

## Mục tiêu

- Quan sát label/selector thay đổi kéo theo EndpointSlice và traffic thế nào.
- Sửa đúng selection thay vì recreate Service hoặc restart cả cluster.

## Mô hình cần hiểu

Selector là điều kiện trên labels, không phải tên Pod hay tên ứng dụng trong đầu người viết YAML. Pod Running vẫn có thể không được chọn hoặc chưa ready để phục vụ theo cấu hình. Endpoint cập nhật có độ trễ hội tụ; cần quan sát trạng thái và thời điểm thay vì giả định đổi nhãn là traffic đổi tức thì.

## Dự đoán trước khi chạy

1. Đổi nhãn một trong hai backend khác gì với đổi selector khiến không Pod nào khớp?
2. Pod IP vẫn gọi được mà ClusterIP lỗi gợi ý gì, và chưa loại trừ được gì?

## Challenge

1. Lưu selector/labels/EndpointSlice và kết quả Pod IP/Service IP baseline.
2. Thay label của một backend lab, quan sát membership và traffic; hoàn nguyên rồi thử case không còn backend phù hợp.
3. Dùng label query với selector thật để giải thích vì sao Pod được/chưa được chọn; kiểm tra readiness và EndpointSlice conditions khi liên quan.
4. Sửa nhãn hoặc selector theo ý định thiết kế ban đầu, không làm rộng selector để bắt cả Pod không thuộc ứng dụng.

## Break & Fix

Nhận case Service không có backend hữu dụng và chưa biết do selector hay readiness. Chọn phép đo phân biệt trước khi sửa. Không chỉnh EndpointSlice do controller quản lý như cách chữa selector; nó có thể bị controller ghi lại.

## Bằng chứng và điều kiện đạt

- [ ] Có bảng label/selector → EndpointSlice → traffic trước/sau, kèm output thật.
- [ ] Giải thích được selection/readiness và sửa đúng phạm vi, không kéo nhầm backend khác.
- [ ] Có control request, hoàn nguyên labels thử và [notes](../templates/LESSON_NOTE.md).

**Tiếp theo:** [18a — Service proxy và packet steering](18a-service-proxy-data-plane.md).
