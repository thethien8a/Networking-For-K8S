# 01a — HTTP request và network endpoint

**Buổi gốc:** 01 · **Thời lượng:** 30–45 phút.  
**Trước bài:** [00 — Khởi động](00-khoi-dong.md).  
**Môi trường:** HTTP server trên Linux lab riêng; client cùng máy trước, client khác nếu đã có đường truy cập.

## Mục tiêu

- Chỉ ra client, server, process, IP và port trong một request.
- Đặt application, TCP, IP và link vào mô hình TCP/IP; hiểu OSI chỉ là bản đồ định hướng, không phải bảy bước lệnh cần học thuộc.

## Mô hình cần hiểu

Ứng dụng client kết nối tới một endpoint, không kết nối tới “cả máy” theo nghĩa chung chung. Với HTTP qua TCP, dữ liệu ứng dụng đi trong TCP segment, IP packet và frame trên đường link; chiều nhận tháo các lớp tương ứng. Request loopback không đi qua NIC vật lý, nên phải ghi đúng topology thực nghiệm.

## Dự đoán trước khi chạy

1. Nếu server chạy nhưng chưa listen ở port bạn gọi, request có tới được ứng dụng không?
2. Request tới `127.0.0.1` có nhất thiết rời máy không? Đổi client sang máy khác sẽ đổi điều gì?

## Challenge

1. Chọn port còn trống và thư mục dữ liệu thử, không chứa thông tin cá nhân; chạy một HTTP server tối giản bằng công cụ lab đã có, chẳng hạn Python nếu đã cài.
2. Từ cùng Linux lab, dùng `curl -v` gọi server. Ghi URL, IP đích, port đích và HTTP status.
3. Dùng `ss -lntp` hoặc quyền quan sát tương ứng để tìm listener. Không giả định port từ ví dụ trùng port thực tế.
4. Vẽ đường đi ứng dụng client → network stack → ứng dụng server; phân biệt phần nào đã quan sát và phần nào là mô hình giải thích.

## Kiểm chứng và đối chứng

Gọi một port khác không có listener và so sánh với request thành công. Đừng gọi lỗi HTTP như 404 là bằng chứng TCP chưa kết nối. Ghi cách dừng đúng server; có thể giữ cho 01b nếu bàn giao rõ process/port và phạm vi dữ liệu.

## Bằng chứng và điều kiện đạt

- [ ] Có output `curl` và listener tương ứng, ghi rõ nơi chạy lệnh.
- [ ] Chỉ được endpoint và phân biệt process, IP, port, packet/frame bằng lời của mình.
- [ ] Sơ đồ không vẽ NIC vật lý cho luồng loopback; ghi chú theo [mẫu](../templates/LESSON_NOTE.md).

**Tiếp theo:** [01b — Loopback và listen address](01b-loopback-va-listen-address.md).
