# 22b — TLS termination và lỗi hostname ở Ingress

**Buổi gốc:** 22 · **Thời lượng:** 45–60 phút.  
**Trước bài:** [22a](22a-ingress-controller-host-path.md).  
**Môi trường:** Ingress lab, certificate cho hostname thử và trust setup cục bộ được kiểm soát; không dùng private key công ty.

## Mục tiêu

- Chỉ ra TLS kết thúc ở đâu và protocol của từng đoạn sau đó.
- Tách TCP failure, TLS validation và HTTP routing/backend errors.

## Mô hình cần hiểu

TLS termination tại controller nghĩa là kết nối TLS từ client kết thúc ở controller; đoạn controller→backend dùng protocol theo cấu hình thật, không tự mặc định vẫn được mã hóa. SNI/certificate selection, hostname verification và HTTP Host/path liên quan nhưng là các phép kiểm tra khác nhau.

## Dự đoán trước khi chạy

1. HTTPS client→controller thành công có bảo đảm controller→backend cũng là HTTPS không?
2. Host routing đúng nhưng certificate không chứa hostname URL thì client xác thực ra sao?

## Challenge

1. Ghi hostname, controller endpoint và certificate/trust chain của lab; đối chiếu cấu hình TLS theo controller/version thực dùng.
2. Test HTTPS đúng hostname với xác thực trust/hostname bật; dùng name resolution override phù hợp nếu chưa có DNS lab.
3. Tạo một sai lệch hostname hoặc rule/backend trong lab, đo output TLS/HTTP và controller/backend logs để xác định tầng lỗi.
4. Vẽ hai đoạn client↔controller và controller↔backend, ghi protocol và bằng chứng cho từng đoạn.

## Break & Fix

Không dùng `-k` làm fix; nếu tạm dùng để tách tầng phải ghi bypass và test lại có xác thực sau sửa. Không lưu nội dung private key trong notes/LLM; chỉ cần thông tin công khai của certificate và cách quản lý secret lab an toàn.

## Bằng chứng và điều kiện đạt

- [ ] Có request HTTPS đúng trust/hostname và case lỗi được phân loại bằng output/logs.
- [ ] Giải thích termination, SNI/Host/certificate và encryption từng đoạn, không suy từ ổ khóa phía client.
- [ ] Có trước/sau fix và cleanup secret/controller resources thuộc bài hoặc bàn giao rõ; lưu [notes](../templates/LESSON_NOTE.md).

**Tiếp theo:** [23a — LoadBalancer bare-metal](23a-loadbalancer-bare-metal.md).
