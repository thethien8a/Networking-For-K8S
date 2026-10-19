# 01b — Loopback, IP của máy và listen address

**Buổi gốc:** 01 · **Thời lượng:** 30–45 phút.  
**Trước bài:** [01a](01a-http-request-va-endpoint.md).  
**Môi trường:** Linux lab riêng có HTTP server; client thứ hai nếu có connectivity đã kiểm tra.

## Mục tiêu

- Phân biệt địa chỉ client gọi với địa chỉ server bind.
- Giải thích tại sao `localhost` không đại diện cho một máy từ mọi vị trí client.

## Mô hình cần hiểu

`127.0.0.1` thuộc loopback của network namespace nơi client chạy. Listener bind loopback khác listener bind một IP interface hoặc wildcard IPv4 `0.0.0.0`; wildcard là địa chỉ bind, không phải IP đích để công bố cho client. Firewall và route vẫn có thể làm kết nối thất bại dù bind đã đúng.

## Dự đoán trước khi chạy

1. Server bind loopback: gọi bằng loopback và bằng IP interface sẽ khác nhau thế nào?
2. Client thứ hai gọi `localhost` thì đang gọi server lab hay chính nó?

## Challenge

1. Lập ma trận nguồn client × địa chỉ đích: cùng máy/khác máy, loopback/IP interface.
2. Chạy server bind loopback, quan sát `ss`, rồi kiểm tra từng ô khả dụng trong ma trận.
3. Chỉ trong lab riêng, đổi bind sang địa chỉ phù hợp với phạm vi truy cập mong muốn; không expose server ra mạng công cộng. Kiểm tra lại cùng ma trận.

## Break & Fix

Nhận một case “server chạy, local curl được, client khác không vào được”. Đưa ít nhất hai giả thuyết rồi dùng listen address và phép đo kết nối để phân biệt, không tắt firewall để thử. Nếu chưa có client thứ hai, ghi phần remote là chưa kiểm chứng, không tự điền pass.

## Bằng chứng và điều kiện đạt

- [ ] Có `ss` + `curl` trước/sau thay bind và giải thích được từng kết quả đã đo.
- [ ] Phân biệt loopback, IP interface và wildcard; không dùng `0.0.0.0` như địa chỉ server cho người dùng.
- [ ] Dừng server/dọn dữ liệu thử, hoặc ghi rõ ownership nếu giữ lab; lưu [notes](../templates/LESSON_NOTE.md).

**Tiếp theo:** [02a — IPv4 và CIDR](02a-ipv4-va-cidr.md).
