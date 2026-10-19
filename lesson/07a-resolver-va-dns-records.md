# 07a — Từ hostname qua resolver đến DNS record

**Buổi gốc:** 07 · **Thời lượng:** 30–45 phút.  
**Trước bài:** [06b](06b-timeout-retry-va-dieu-tra.md).  
**Môi trường:** Linux lab có `dig` hoặc `nslookup`, hostname được phép truy vấn và endpoint thử.

## Mục tiêu

- Nhận diện A, AAAA, CNAME, resolver, nameserver và caching cơ bản.
- Phân biệt tra DNS trực tiếp với cơ chế phân giải tên mà ứng dụng thực dùng.

## Mô hình cần hiểu

Ứng dụng có thể dùng cơ chế resolver của OS, trong đó `/etc/hosts` và DNS là những nguồn khác nhau theo cấu hình hệ thống. `dig` hỏi DNS trực tiếp, nên không dùng nó để chứng minh `/etc/hosts` có được ứng dụng đọc hay không. `/etc/resolv.conf` là một đầu mối cấu hình quan trọng trên Linux, nhưng có thể được dịch vụ quản lý sinh ra.

## Dự đoán trước khi chạy

1. Một tên có trong hosts file nhưng không có DNS record: ứng dụng và `dig` có bắt buộc trả kết quả giống nhau không?
2. Resolve được A/AAAA có bảo đảm TCP port đích đang mở không?

## Challenge

1. Đọc resolver config của đúng Linux lab; ghi nameserver và cơ chế quản lý nếu xác minh được, không sửa file.
2. Truy vấn A/AAAA và một CNAME do người dạy chọn. Đọc status, answer, TTL và server trả lời; ghi trường hợp không có bản ghi theo thực tế.
3. Vẽ ứng dụng → resolver → nguồn hosts/DNS → địa chỉ, tách rõ lookup DNS trực tiếp.
4. Gọi cùng endpoint bằng tên và IP trong một bài HTTP đơn giản; ghi rõ nếu HTTP Host khác khiến response khác, chưa quy ngay thành lỗi DNS.

## Kiểm chứng và đối chứng

TTL/caching ở nhiều lớp có thể làm hai lần tra khác nhau. Không xóa cache toàn hệ thống chỉ để làm bài. Nếu kết quả ứng dụng khác `dig`, kiểm tra đường resolver và nguồn dữ liệu thay vì kết luận DNS “sai ngẫu nhiên”.

## Bằng chứng và điều kiện đạt

- [ ] Có output truy vấn với các trường quan trọng được giải thích và sơ đồ resolver.
- [ ] Phân biệt A/AAAA/CNAME, hosts file và DNS query; không nhầm name resolution với TCP success.
- [ ] Có [notes](../templates/LESSON_NOTE.md), không thay resolver của máy công ty/host đang dùng.

**Tiếp theo:** [07b — IP chạy nhưng hostname lỗi](07b-ip-duoc-hostname-loi.md).
