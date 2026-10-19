# 18b — port, targetPort, listener hay NetworkPolicy?

**Buổi gốc:** 18 · **Thời lượng:** 30–45 phút.  
**Trước bài:** [18a](18a-service-proxy-data-plane.md).  
**Môi trường:** Service/backend lab riêng; chỉ đọc policy đang có, chưa cần tự viết policy ở bài này.

## Mục tiêu

- Lần theo client port → Service port → backend target port → listener thật.
- Phân biệt mapping sai với listener/path/policy failure bằng phép thử có đối chứng.

## Mô hình cần hiểu

Client gọi Service `port`, còn backend nhận ở `targetPort` được giải quyết theo cấu hình. Khai báo `containerPort` không bật ứng dụng. EndpointSlice có address không có nghĩa backend listen đúng port; NetworkPolicy hoặc path cũng có thể chặn một tuple vốn được map đúng.

## Dự đoán trước khi chạy

1. Service port và listener port khác nhau có luôn là lỗi không?
2. Pod IP:listener-port chạy nhưng Service IP:port lỗi thì cần kiểm tra những mắt xích nào tiếp?

## Challenge

1. Lập bảng client destination, Service port/targetPort, EndpointSlice port và listener thật bằng output hiện tại.
2. Người dạy tạo một lỗi mapping hoặc listener trong lab riêng; học viên chưa biết trường bị đổi.
3. Test trực tiếp backend đúng tuple và qua Service, từ cùng client, với cùng request. Kiểm tra selection/readiness trước khi đổ lỗi proxy.
4. Đọc policy áp lên Pod nếu có, ghi khả năng thực thi của CNI; chỉ sửa trường được bằng chứng xác nhận sai.

## Break & Fix

Không đổi cả Service port, targetPort và application config cùng lúc. Không tắt policy để “thử nhanh” khi chưa có scope/rollback. Nếu đã dùng named port, kiểm tra tên và mapping thực tế thay vì đoán từ số cổng mẫu.

## Bằng chứng và điều kiện đạt

- [ ] Có bảng mapping tới listener và output direct/backend so với Service trước/sau.
- [ ] Xác minh đúng root cause, loại trừ ít nhất một giả thuyết selection/proxy/policy phù hợp case.
- [ ] Có control, rollback manifest và [notes](../templates/LESSON_NOTE.md).

**Tiếp theo:** [19a — CoreDNS và Service names](19a-coredns-service-names.md).
