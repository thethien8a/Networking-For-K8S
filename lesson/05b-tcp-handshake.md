# 05b — TCP handshake và connection state

**Buổi gốc:** 05 · **Thời lượng:** 30–45 phút.  
**Trước bài:** [05a](05a-port-socket-tcp-udp.md).  
**Môi trường:** hai đầu TCP trong lab riêng, quyền `tcpdump` trên interface/loopback đúng luồng.

## Mục tiêu

- Nhận diện SYN → SYN-ACK → ACK và gắn với tuple/socket.
- So sánh mở kết nối thành công với gọi port không có listener.

## Mô hình cần hiểu

Handshake thiết lập trạng thái kết nối TCP trước khi trao đổi dữ liệu ứng dụng thông thường. Server trả HTTP lỗi sau handshake vẫn là một kết nối TCP đã thành công. Một capture cần được đọc theo tuple và thời gian; nhiều request song song có thể trộn các cuộc hội thoại.

## Dự đoán trước khi chạy

1. SYN do đầu nào gửi, SYN-ACK có source/destination port như thế nào so với SYN?
2. Nếu gọi port đóng trên host reachable, bạn mong đợi gì khác với port đang listen?

## Challenge

1. Chọn một TCP listener lab, mở capture có filter đích/port hẹp trước khi tạo kết nối.
2. Tạo một kết nối mới; chú thích ba bước handshake, chiều packet và tuple. Kiểm tra socket state khi giữ kết nối nếu công cụ cho phép.
3. Dừng listener đúng cách hoặc chọn port đã kiểm tra không có listener, rồi lặp phép thử và capture.
4. Vẽ handshake bằng mũi tên hai chiều; đánh dấu chỗ dữ liệu HTTP xuất hiện trong phép thử.

## Break & Fix

Nếu không thấy đủ ba bước, kiểm tra capture bắt đầu lúc nào, interface nào và có đang tái sử dụng kết nối cũ không. Port đóng có thể trả RST nhưng firewall/path có thể làm quan sát khác; kết luận theo capture thực tế, không ép theo hình mẫu.

## Bằng chứng và điều kiện đạt

- [ ] Có capture handshake được chú thích và mối liên hệ với socket/listener.
- [ ] Có đối chứng port mở/đóng, giải thích RST nếu quan sát được và phần nào chưa đủ bằng chứng.
- [ ] Không nhầm UDP với handshake TCP; dọn process/capture và lưu [notes](../templates/LESSON_NOTE.md).

**Tiếp theo:** [06a — Refused, timeout và reset](06a-refused-timeout-reset.md).
