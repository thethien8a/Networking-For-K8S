# 02a — IPv4, subnet mask và CIDR

**Buổi gốc:** 02 · **Thời lượng:** 30–45 phút.  
**Trước bài:** [01b](01b-loopback-va-listen-address.md).  
**Môi trường:** giấy/Markdown và Linux lab để đọc địa chỉ, không thay cấu hình.

## Mục tiêu

- Tách network bits/host bits và đổi prefix sang subnet mask.
- Xác định network address, broadcast và usable range của subnet IPv4 thông thường; nhận diện private IPv4.

## Mô hình cần hiểu

IPv4 có 32 bit; `/n` cho biết bao nhiêu bit thuộc prefix mạng. IP chỉ có ý nghĩa về subnet membership khi đi kèm prefix. Với các bài subnet thông thường ở đây, network/broadcast không gán cho host; `/31` và `/32` có ngữ nghĩa riêng, không áp dụng máy móc công thức usable của `/24` cho chúng.

## Dự đoán trước khi chạy

1. `10.20.30.10/27` và `10.20.30.40/27` có cùng subnet không? Nếu dùng `/24` thì sao?
2. Đổi prefix nhưng giữ IP có làm thay đổi network address và tập đích được coi là on-link không?

## Challenge

1. Viết nhị phân octet có ranh giới prefix của `10.20.30.10/27`; tự tìm mask, network, broadcast, usable range.
2. Làm tương tự với một `/26` và một `/23` do LLM đưa, trong đó có địa chỉ ở hai phía ranh giới subnet.
3. Đọc IP/prefix thật của Linux lab; tính network bằng tay rồi mới kiểm chứng bằng công cụ tính subnet có sẵn hoặc thư viện chuẩn.
4. Phân loại ví dụ private/public/loopback; không coi toàn bộ `172.x.x.x` là private.

## Kiểm chứng và đối chứng

Giải thích bằng bit vì sao hai địa chỉ có ba octet đầu giống nhau vẫn có thể khác subnet, và hai địa chỉ khác octet thứ ba có thể cùng `/23`. Bài này không yêu cầu sửa IP của máy.

## Bằng chứng và điều kiện đạt

- [ ] Có bài tính độc lập và kết quả đối chiếu, gồm ít nhất một prefix không nằm ở ranh giới octet.
- [ ] Giải thích membership dựa trên prefix, không dựa vào “IP trông giống nhau”.
- [ ] Ghi giả định subnet thông thường và không nhầm private IP với bảo đảm an toàn; lưu [notes](../templates/LESSON_NOTE.md).

**Tiếp theo:** [02b — IP plan và prefix không đồng nhất](02b-ip-plan-va-prefix.md).
