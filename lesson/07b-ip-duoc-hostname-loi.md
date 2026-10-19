# 07b — Khi IP hoạt động nhưng hostname không hoạt động

**Buổi gốc:** 07 · **Thời lượng:** 30–45 phút.  
**Trước bài:** [07a](07a-resolver-va-dns-records.md).  
**Môi trường:** resolver/client trong lab cô lập; không đổi DNS của macOS, VM công ty hoặc runtime dùng chung.

## Mục tiêu

- Tách tên không tồn tại, resolver không trả lời và ứng dụng kết nối sai địa chỉ.
- Chọn phép thử giữ các biến khác ổn định khi so sánh tên với IP.

## Mô hình cần hiểu

“Truy cập IP được” chỉ cô lập DNS khi bạn thực sự so sánh cùng dịch vụ, port và yêu cầu ứng dụng tương đương. HTTPS/virtual hosting có thể phụ thuộc hostname ngay cả khi địa chỉ TCP đúng. DNS NXDOMAIN, answer rỗng và query timeout cũng không phải cùng một hiện tượng.

## Dự đoán trước khi chạy

1. Tên sai nhưng resolver hoạt động và resolver không reachable sẽ cho bằng chứng khác nhau thế nào?
2. Ứng dụng nhận AAAA trước trong môi trường IPv6 không hoạt động có thể khiến phép so sánh chỉ nhìn A bị thiếu gì?

## Challenge

1. Tạo baseline endpoint lab qua IP và qua tên với Host/port được kiểm soát.
2. Người dạy chuẩn bị tên không có record hoặc cấu hình resolver lỗi chỉ cho client lab. Ghi dự đoán, tra trực tiếp và quan sát resolution của ứng dụng.
3. Kiểm tra server DNS được hỏi, record/address trả về và TCP destination thực tế; chọn một sửa đổi đúng phạm vi.
4. Kiểm tra lại tên lẫn IP sau fix, hoàn nguyên cấu hình thử nghiệm.

## Break & Fix

Không thêm `/etc/hosts` làm “fix DNS” mà không ghi đó là workaround và không tìm nguyên nhân. Không dùng IP hard-code để tuyên bố service discovery đã được sửa. Nếu có cache, ghi trạng thái và ảnh hưởng thay vì tự giả định đã sạch.

## Bằng chứng và điều kiện đạt

- [ ] Có DNS output và client evidence phân biệt resolution với connect.
- [ ] Giải thích root cause của case và vì sao control qua IP hợp lệ cho phép so sánh đó.
- [ ] Có trước/sau fix, hoàn nguyên resolver lab và [notes](../templates/LESSON_NOTE.md).

**Tiếp theo:** [08a — HTTP Host và TLS](08a-http-host-va-tls.md).
