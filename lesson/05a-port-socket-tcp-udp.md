# 05a — Port, socket, TCP và UDP

**Buổi gốc:** 05 · **Thời lượng:** 30–45 phút.  
**Trước bài:** [04b](04b-route-fault-va-cp1.md) và CP1 đạt.  
**Môi trường:** Linux lab, `ss` và công cụ tạo TCP/UDP traffic như `nc`, đã kiểm tra flags của implementation/OS thực tế.

## Mục tiêu

- Phân biệt protocol, source/destination port, listen socket và kết nối.
- Đọc 4-tuple của một kết nối TCP, không nhầm port với định danh toàn cục của ứng dụng.

## Mô hình cần hiểu

Trong TCP, một kết nối được phân biệt bằng cặp IP/port hai đầu trong ngữ cảnh protocol. Nhiều client có thể cùng kết nối tới một server port nhờ tuple khác nhau. TCP cung cấp luồng byte tin cậy có thứ tự; UDP gửi datagram không có TCP handshake, nhưng ứng dụng có thể tự xây cơ chế xác nhận/retry.

## Dự đoán trước khi chạy

1. Hai client cùng gọi một server port có cần tranh nhau đúng một kết nối không?
2. TCP listener ở một port có chứng minh UDP cùng số port cũng có người nhận không?

## Challenge

1. Mở một TCP listener trong lab, nối hai client và đọc `ss` ở hai phía khi kết nối còn tồn tại.
2. Ghi local/peer IP và port, phân biệt LISTEN với ESTABLISHED; tìm source port do client dùng.
3. Tạo một phép thử UDP riêng bằng công cụ có sẵn; ghi rõ UDP socket/traffic, không dùng kết quả TCP làm kết quả UDP.
4. Vẽ hai kết nối TCP cùng destination port nhưng khác source tuple.

## Kiểm chứng và đối chứng

Ping được không chứng minh TCP hay UDP port hoạt động. “Connected UDP socket” trong công cụ không có nghĩa đã diễn ra three-way handshake. Chọn phép đo thực sự quan sát nhận datagram thay vì coi không báo lỗi là thành công.

## Bằng chứng và điều kiện đạt

- [ ] Có output sockets và hai tuple TCP khác nhau tới cùng server port.
- [ ] Có traffic UDP và bằng chứng receiver nhận được dữ liệu.
- [ ] Giải thích đúng TCP/UDP, dừng process thử nghiệm và lưu [notes](../templates/LESSON_NOTE.md).

**Tiếp theo:** [05b — TCP handshake](05b-tcp-handshake.md).
