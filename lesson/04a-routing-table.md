# 04a — Gateway, routing table và longest-prefix match

**Buổi gốc:** 04 · **Thời lượng:** 30–45 phút.  
**Trước bài:** [03b](03b-neighbor-cache-va-gateway.md).  
**Môi trường:** Linux lab, chỉ đọc routes trong bài này.

## Mục tiêu

- Đọc connected route, default route, next hop và interface ra.
- Dự đoán route theo prefix cụ thể nhất trong các route đủ điều kiện.

## Mô hình cần hiểu

Default route chỉ là lựa chọn khi không có route phù hợp cụ thể hơn trong quá trình lookup. Connected route biểu diễn đích trên link, không phải mọi packet đều đi qua gateway. Bài nhập môn dùng routing đơn giản; nếu host có policy routing hoặc nhiều bảng, ghi rõ giới hạn và đối chiếu lookup thực tế thay vì ép vào ví dụ một bảng.

## Dự đoán trước khi chạy

1. Route `/25` và `/24` cùng khớp một đích: route nào cụ thể hơn?
2. Có default route có đồng nghĩa mọi đích đều truy cập được không?

## Challenge

1. Đọc `ip route`; chỉ ra subnet on-link, default gateway và interface tương ứng.
2. LLM chọn 5 địa chỉ gồm loopback, cùng subnet, ngoài subnet và một trường hợp route cụ thể hơn nếu có. Bạn điền interface/next hop/source IP dự đoán trước khi kiểm tra.
3. Dùng `ip route get <IP>` cho từng đích; so sánh với dự đoán. Địa chỉ trong dấu `<>` phải thay bằng đích thật của bài.
4. Với một routing table giả lập có prefix chồng lấn, giải thích quyết định từng bước; không cài routes giả lập lên host.

## Kiểm chứng và đối chứng

Route lookup thành công chỉ chứng minh kernel chọn được đường, không chứng minh gateway, firewall hoặc server hoạt động. Nối kết quả route với MAC next hop đã học, nhưng chưa kết luận reachability từ đó.

## Bằng chứng và điều kiện đạt

- [ ] Có bảng 5 dự đoán và output lookup, giải thích chỗ khác dự đoán.
- [ ] Đọc đúng connected/default route và longest-prefix match qua ví dụ route chồng lấn.
- [ ] Phân biệt “có route” với “kết nối thành công”; lưu [notes](../templates/LESSON_NOTE.md).

**Tiếp theo:** [04b — Sửa lỗi route và CP1](04b-route-fault-va-cp1.md).
