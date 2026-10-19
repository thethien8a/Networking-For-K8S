# 19a — CoreDNS, namespace, short name và FQDN

**Buổi gốc:** 19 · **Thời lượng:** 30–45 phút.  
**Trước bài:** [18b](18b-port-targetport-listener.md).  
**Môi trường:** hai namespace lab, một Service thử và Pod có công cụ DNS thích hợp.

## Mục tiêu

- Dự đoán tên Service resolve được từ cùng/khác namespace.
- Đọc Pod resolver config, search suffix và `ndots` theo cấu hình thật.

## Mô hình cần hiểu

Tên Service dạng `<service>.<namespace>.svc.<cluster-domain>` cần cluster domain đã xác minh, không tự chốt `cluster.local`. Tên ngắn phụ thuộc search configuration và namespace của client. CoreDNS phục vụ DNS trong nhiều cluster, nhưng tên Service DNS, IP và deployment thực tế phải được kiểm tra thay vì mặc định.

## Dự đoán trước khi chạy

1. Client namespace A hỏi short name của Service namespace B sẽ tìm tên nào trước?
2. Cùng một short name từ hai namespace có thể trỏ hai Service khác nhau không?

## Challenge

1. Ghi DNS policy/config và `/etc/resolv.conf` của client Pod; xác định nameserver, search list, `ndots` và cluster suffix từ bằng chứng.
2. Tạo Service lab ở namespace B; từ client B và client A thử short name, tên kèm namespace và FQDN thực tế.
3. Lập matrix client namespace × tên truy vấn × kết quả/địa chỉ trả về. Dùng tên tuyệt đối có dấu chấm cuối khi cần tách search behavior, ghi công cụ cụ thể.
4. Gọi endpoint sau resolution để phân biệt “tên ra IP đúng” với “ứng dụng truy cập được”.

## Kiểm chứng và đối chứng

Không tạo cùng tên Service ở nhiều namespace rồi mặc định chúng là một đối tượng. `dig` và resolver ứng dụng có thể áp search khác nhau theo flags/tool; ghi cách query và tên thực sự hỏi trước khi so sánh.

## Bằng chứng và điều kiện đạt

- [ ] Có resolver config và matrix cùng/khác namespace với short name/FQDN.
- [ ] Giải thích search suffix/ndots theo output, dùng cluster domain thực tế.
- [ ] Có request tới endpoint và [notes](../templates/LESSON_NOTE.md), ownership các namespace lab rõ ràng.

**Tiếp theo:** [19b — Lần theo DNS resolution path](19b-dns-resolution-path.md).
