# 08a — URL, HTTP Host, TLS SNI và chứng chỉ

**Buổi gốc:** 08 · **Thời lượng:** 45–60 phút.  
**Trước bài:** [07b](07b-ip-duoc-hostname-loi.md).  
**Môi trường:** HTTP/TLS lab riêng có hostname và certificate/trust setup phù hợp; không cần tên miền công cộng.

## Mục tiêu

- Phân biệt TCP destination, tên dùng cho DNS, HTTP Host và tên TLS cần xác thực.
- Phân loại HTTP error và TLS validation failure, không nhầm với lỗi kết nối TCP.

## Mô hình cần hiểu

URL chứa hostname có thể chi phối cả DNS lookup, HTTP Host và TLS SNI/hostname verification. Đổi riêng Host header không nhất thiết đổi tên dùng trong TLS. `curl --resolve` có thể ghim ánh xạ tên–port–IP cho phép thử trong khi vẫn giữ hostname URL; kiểm tra cú pháp theo bản cài trước khi dùng.

## Dự đoán trước khi chạy

1. Gọi IP trực tiếp rồi đổi Host header có luôn tương đương gọi đúng HTTPS hostname không?
2. HTTP 404, lỗi certificate hostname và connection refused thuộc ba giai đoạn nào?

## Challenge

1. Với endpoint lab, ghi URL hostname, TCP IP/port, Host header, TLS name và chuỗi tin cậy dự kiến.
2. Dùng `curl -v` so sánh HTTP route đúng/sai Host. Với TLS, so sánh hostname phù hợp/không phù hợp trong lab có certificate được kiểm soát.
3. Thử `--resolve` khi phù hợp để tách DNS khỏi Host/TLS; ghi biến nào đổi, biến nào giữ nguyên.
4. Chú thích các mốc resolution → TCP connect → TLS → HTTP response trên output.

## Break & Fix

Nhận case hostname resolve đúng nhưng Host routing hoặc TLS validation sai. Xác định lỗi thuộc routing ứng dụng, hostname hay trust trước khi sửa. `-k` nếu dùng để khoanh vùng chỉ là bypass kiểm tra, không phải kết quả HTTPS an toàn đã sửa; test cuối phải xác thực chứng chỉ đúng.

## Bằng chứng và điều kiện đạt

- [ ] Có bảng các tên/địa chỉ và output HTTP/TLS trước/sau khác biệt có kiểm soát.
- [ ] Phân biệt DNS, TCP, HTTP error, TLS hostname và trust failure bằng bằng chứng.
- [ ] Có request TLS cuối xác thực đúng, dọn server/certificate lab theo ownership và lưu [notes](../templates/LESSON_NOTE.md).

**Tiếp theo:** [08b — NAT và CP2](08b-nat-port-mapping-va-cp2.md).
