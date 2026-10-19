# 09b — Sockets, listener và connectivity matrix

**Buổi gốc:** 09 · **Thời lượng:** 30–45 phút.  
**Trước bài:** [09a](09a-linux-network-inventory.md).  
**Môi trường:** Ubuntu lab riêng có một HTTP/TCP service thử, không thay network config host.

## Mục tiêu

- Bổ sung sockets/listeners vào inventory và chứng minh từng kết nối từ đúng vị trí client.
- Khoanh vùng listen sai IP/port trước khi đổ lỗi cho network infrastructure.

## Mô hình cần hiểu

Một connectivity matrix cần nguồn, đích, protocol/port, expected result và actual result. “Máy A ping máy B được” không thay cho “client A gọi TCP port P của service B được”. Listener có thể tồn tại nhưng chỉ bind loopback, và thông tin process có thể bị giới hạn bởi quyền quan sát.

## Dự đoán trước khi chạy

1. Bạn mong đợi `ss -lntup` cho thấy gì với server thử, và có thấy process name nếu không đủ quyền không?
2. Kiểm tra từ server tới chính nó có thay được kết quả từ một client khác không?

## Challenge

1. Ghép `ss -lntup` với inventory 09a; xác nhận IP/port/protocol của service thử.
2. Lập matrix ít nhất bốn phép thử khác nhau về vị trí nguồn, địa chỉ đích hoặc port; có một control kỳ vọng thất bại.
3. Người dạy đổi listener của service lab sang địa chỉ hoặc port khác, chưa công bố giá trị. Điều tra bằng `ss`, client output và route khi cần.
4. Sửa đúng cấu hình service, chạy lại cả matrix, không thay interface/route/firewall của host.

## Break & Fix

Nếu thiếu công cụ hoặc quyền xem process, ghi hạn chế và dùng bằng chứng listener/traffic phù hợp; không cài agent đặc quyền để tiện xem. Control thất bại cũng cần giải thích, không chỉ giữ những hàng thành công.

## Bằng chứng và điều kiện đạt

- [ ] Có inventory sockets và matrix trước/sau thay đổi với output thật.
- [ ] Giải thích được lỗi listener và loại trừ ít nhất một giả thuyết route/DNS không phù hợp.
- [ ] Dọn service thử và lưu [notes](../templates/LESSON_NOTE.md).

**Tiếp theo:** [10a — Namespace và veth](10a-network-namespace-va-veth.md).
