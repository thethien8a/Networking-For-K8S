# 19b — Lần theo Pod resolver → DNS Service → CoreDNS

**Buổi gốc:** 19 · **Thời lượng:** 30–45 phút.  
**Trước bài:** [19a](19a-coredns-service-names.md).  
**Môi trường:** cluster lab; DNS fault chỉ ở client/tài nguyên lab, không làm hỏng DNS toàn cluster để thử.

## Mục tiêu

- Phân biệt tên hỏi sai với DNS path/server failure.
- Dùng DNS Service/EndpointSlice, Pod resolver và logs để chứng minh đoạn lỗi.

## Mô hình cần hiểu

Truy vấn DNS từ Pod cần tới đúng resolver theo cấu hình, và resolver cần dữ liệu/phụ thuộc phù hợp để trả lời. Tên không tồn tại không đồng nghĩa DNS server chết. CoreDNS Pod Running cũng chưa chứng minh DNS Service path hoặc upstream queries hoạt động với client đang xét.

## Dự đoán trước khi chạy

1. NXDOMAIN cho một tên sai và timeout cho cả tên nội bộ đúng khác nhau ở đâu?
2. Nếu gọi app qua ClusterIP được nhưng tên không resolve, bước đo nào tránh đụng application deployment?

## Challenge

1. Xác định DNS Service thật, endpoints và resolver Pod/config trong quyền được cấp; đừng mặc định tên resource.
2. Ghi baseline truy vấn một Service FQDN đúng và một tên không tồn tại từ cùng client.
3. Nhận một fault lab ở tên/cấu hình DNS của client hoặc case dựng sẵn an toàn. Theo resolver config → DNS Service/endpoints → server/logs liên quan, tùy giả thuyết.
4. Dùng direct IP request làm control đúng Host/port, sửa một biến và kiểm tra lại các truy vấn ban đầu.

## Break & Fix

Không scale CoreDNS về 0, đổi ConfigMap DNS dùng chung hoặc restart toàn cluster để có lỗi. Nếu policy chặn DNS, kiểm tra thực thi và path thật; không suy từ việc YAML policy tồn tại. Case cần thay đổi cluster-wide phải chuyển sang cluster disposable được đồng ý trước.

## Bằng chứng và điều kiện đạt

- [ ] Có query/status/output, DNS resource evidence và root cause của case được phân biệt với tên không tồn tại bình thường.
- [ ] Có before/after fix cùng control IP và truy vấn tên đúng.
- [ ] Hoàn nguyên client config/fault và lưu [notes](../templates/LESSON_NOTE.md).

**Tiếp theo:** [20a — Hai mini-incident Service](20a-service-mini-incidents.md).
