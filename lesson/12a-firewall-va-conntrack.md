# 12a — Packet filtering, hooks và connection tracking

**Buổi gốc:** 12 · **Thời lượng:** 30–45 phút.  
**Trước bài:** [11b](11b-return-path-va-forwarding.md).  
**Môi trường:** router namespace/endpoint namespace của Linux lab; chọn nftables hoặc iptables theo backend thực tế.

## Mục tiêu

- Phân loại local input, local output và forwarded traffic.
- Hiểu conntrack là trạng thái flow của kernel, không đồng nhất với process socket trong `ss`.

## Mô hình cần hiểu

Traffic tới chính namespace, traffic do nó phát và traffic đi xuyên qua nó gặp các hook khác nhau. INPUT/FORWARD/OUTPUT là tên quen thuộc trong iptables; nftables dùng base chains gắn hooks và có thể có tên chain tùy chọn. Stateful filtering có thể cho phép flow đã thiết lập trong khi kết nối mới bị chặn.

## Dự đoán trước khi chạy

1. Chặn local input trên router có nhất thiết chặn A→B transit không?
2. Một kết nối TCP cũ còn chạy có chứng minh rule cho kết nối mới đang mở không?

## Challenge

1. Xác định firewall tool/backend đang dùng trong lab; đọc rules tại đúng namespace bằng quyền đã cho phép.
2. Vẽ ba luồng: A→R, R→B, A→B. Đánh dấu hook/chain logic liên quan, không chép tên chain nft từ ví dụ chưa kiểm tra.
3. Trong namespace lab, tạo một rule thử có phạm vi nhỏ và counter phù hợp; dự đoán luồng nào bị ảnh hưởng rồi đo.
4. Nếu có quyền/công cụ, đối chiếu conntrack state/counters với socket và packet; nếu thiếu, giải thích giới hạn quan sát, không bịa output.

## Kiểm chứng và đối chứng

Đọc toàn bộ điều kiện/order liên quan trước khi kết luận một rule đã match. Không sửa backend hoặc rules do runtime quản lý. Rule thử phải có cách gỡ chính xác và được hoàn nguyên ngay sau đo, trừ khi bàn giao có ghi rõ cho 12b.

## Bằng chứng và điều kiện đạt

- [ ] Có phân loại đúng ba luồng, rules/counters và phép thử xác nhận một rule lab ảnh hưởng đúng phạm vi.
- [ ] Giải thích conntrack khác socket, và kết nối cũ khác kết nối mới.
- [ ] Có rollback đúng rule, kiểm tra sau gỡ và [notes](../templates/LESSON_NOTE.md).

**Tiếp theo:** [12b — DROP/REJECT và CP3](12b-drop-reject-va-cp3.md).
