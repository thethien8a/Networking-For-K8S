# 10a — Network namespace và veth pair

**Buổi gốc:** 10 · **Thời lượng:** 45–60 phút.  
**Trước bài:** [09b](09b-sockets-va-connectivity-matrix.md).  
**Môi trường:** Linux lab riêng có quyền tạo netns/veth; không chạy trên macOS hoặc runtime VM dùng chung.

## Mục tiêu

- Hiểu network isolation khác process isolation.
- Tạo hai namespace nối trực tiếp bằng một veth pair, đọc trạng thái từ đúng namespace.

## Mô hình cần hiểu

Network namespace có interfaces, addresses, routes, sockets và loopback riêng. Veth pair là cặp interface ảo nối hai đầu; chuyển một đầu sang namespace khác tạo một link giữa hai network stacks. Chỉ tạo namespace chưa tự có đường nối hoặc địa chỉ usable cho bài.

## Dự đoán trước khi chạy

1. Server listen loopback trong namespace A có tự xuất hiện ở loopback namespace B không?
2. Gán IP nhưng quên đưa link/loopback cần dùng lên có thể làm phép thử nào thất bại?

## Challenge

1. Chọn tên tài nguyên riêng, hai IP cùng subnet không chồng lấn lab; ghi topology và lệnh dọn trước khi tạo.
2. Tạo hai namespace và veth pair, đặt mỗi đầu vào đúng namespace. Kiểm tra bằng `ip netns` và `ip link` trong từng nơi.
3. Cấu hình addresses và trạng thái link/loopback cần thiết chỉ trong lab này; dùng ping rồi HTTP để chứng minh kết nối.
4. So sánh routes/listeners bên trong A, B và host. Ghi rõ interface nào thuộc đâu.

## Break & Fix

Với một link chưa UP hoặc đầu veth bị đặt sai chỗ, suy luận từ interface membership/state trước khi chạy lại cả chuỗi lệnh. Không đổi NIC/forwarding của host để chữa mô hình hai namespace cùng subnet.

## Bằng chứng và điều kiện đạt

- [ ] Có sơ đồ netns–veth, addresses, `ip link` và kết quả ping/HTTP thật.
- [ ] Giải thích loopback và socket isolation, xác định được lỗi link/membership.
- [ ] Có lệnh cleanup cụ thể; nếu giữ cho 10b, ghi tài nguyên và kế hoạch chuyển topology trong [notes](../templates/LESSON_NOTE.md).

**Tiếp theo:** [10b — Linux bridge](10b-linux-bridge.md).
