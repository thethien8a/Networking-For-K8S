# 11a — Router namespace nối hai subnet

**Buổi gốc:** 11 · **Thời lượng:** 45–60 phút.  
**Trước bài:** [10b](10b-linux-bridge.md).  
**Môi trường:** Linux lab riêng; forwarding chỉ cấu hình trong router namespace của bài.

## Mục tiêu

- Tạo hai subnet qua một router namespace với static routes.
- Theo dõi cả request và response, không chỉ đường đi tới server.

## Mô hình cần hiểu

Router namespace có hai interface L3, mỗi interface ở một subnet. Endpoint cần route tới mạng còn lại; router cần khả năng forward traffic và đường đi phù hợp. Không cần NAT chỉ để cho hai subnet lab đã có route hai chiều giao tiếp.

## Dự đoán trước khi chạy

1. A có route tới B nhưng B không biết đường về A thì HTTP có thể thất bại ở bước nào?
2. Vì sao kết nối tới IP của chính router khác với traffic đi xuyên qua router?

## Challenge

1. Vẽ A–R–B, chọn hai subnet riêng không overlap và bốn IP interface. Lập route table dự kiến cho cả ba namespace.
2. Tạo veth links A↔R và R↔B; cấu hình địa chỉ, link/loopback, static routes và forwarding trong R sau khi xác nhận đúng namespace.
3. Chạy service ở B, kiểm tra từ A bằng IP; capture ở hai phía R nếu được phép.
4. Chú thích source/destination IP, next hop/MAC từng link và route chiều trả về; không thêm NAT để che route thiếu.

## Kiểm chứng và đối chứng

Giữ một phép thử A→R và một phép thử A→B để tách local delivery với forwarding. Không bật forwarding global trên Linux host hoặc VM công ty. Ghi giá trị/cấu hình trước thay đổi và cách dọn topology.

## Bằng chứng và điều kiện đạt

- [ ] Có topology, addresses, routes ba namespace và kết nối thực tế giữa hai subnet.
- [ ] Có bằng chứng hai chiều và giải thích router cần làm gì khi nhận packet transit.
- [ ] Có [notes](../templates/LESSON_NOTE.md), rollback và ownership nếu giữ cho 11b/12.

**Tiếp theo:** [11b — Return path và forwarding](11b-return-path-va-forwarding.md).
