# 06a — Refused, timeout và reset nói lên điều gì?

**Buổi gốc:** 06 · **Thời lượng:** 45–60 phút.  
**Trước bài:** [05b](05b-tcp-handshake.md).  
**Môi trường:** Linux lab cô lập; rule DROP chỉ áp dụng luồng thử và có rollback, không chặn kết nối quản trị.

## Mục tiêu

- Phân biệt triệu chứng connection refused, timeout và reset bằng nhiều nguồn bằng chứng.
- Tránh suy ra root cause chỉ từ thông báo client.

## Mô hình cần hiểu

Refused thường đi kèm phản hồi từ chối kết nối; timeout chỉ nói rằng không hoàn tất một bước trong thời gian giới hạn; reset là TCP bị hủy bằng RST. Một server đóng bình thường bằng FIN không tự động tạo reset, nên case reset phải được dựng và xác nhận đúng, không chỉ “đóng server”.

## Dự đoán trước khi chạy

1. Đích không có listener và luồng bị DROP có khác về phản hồi packet không?
2. Client nhìn thấy timeout có đủ để kết luận firewall là thủ phạm không?

## Challenge

1. Người dạy chuẩn bị ba case lab có kiểm soát: không có listener, traffic bị DROP và kết nối bị reset thật sự. Học viên biết phạm vi an toàn, chưa biết case nào đang nhận.
2. Với mỗi case, ghi lỗi/timing từ client có timeout hữu hạn, trạng thái listener bằng `ss` và capture phù hợp.
3. Lập bảng triệu chứng → giả thuyết → phép đo phân biệt → kết luận. Kiểm tra trạng thái server thay vì chỉ nhìn phía client.

## Break & Fix

Sửa từng case bằng thay đổi tối thiểu, rồi dùng đúng phép thử ban đầu để xác minh. Với DROP, chuẩn bị và thực hiện hoàn nguyên đúng rule thử nghiệm; không flush firewall. Cơ chế firewall chi tiết sẽ học ở buổi 12, nên phần thiết lập ban đầu do LLM hướng dẫn có giới hạn.

## Bằng chứng và điều kiện đạt

- [ ] Có ba tình huống thực tế và bảng phân tích, gồm packet evidence giúp phân biệt.
- [ ] Giải thích được vì sao FIN, RST và im lặng không tương đương nhau.
- [ ] Có post-fix/control tests và xác nhận rule/process thử nghiệm đã dọn; lưu [notes](../templates/LESSON_NOTE.md).

**Tiếp theo:** [06b — Timeout, retry và điều tra](06b-timeout-retry-va-dieu-tra.md).
