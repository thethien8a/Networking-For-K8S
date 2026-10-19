# 03a — Ethernet, MAC và ARP

**Buổi gốc:** 03 · **Thời lượng:** 30–45 phút.  
**Trước bài:** [02b](02b-ip-plan-va-prefix.md).  
**Môi trường:** Linux lab có một link Ethernet/virtual Ethernet phù hợp và quyền capture phạm vi lab.

## Mục tiêu

- Phân biệt địa chỉ L2 và L3; nhận diện ARP request/reply.
- Giải thích switch, broadcast domain và neighbor cache ở mức đủ để đọc một capture.

## Mô hình cần hiểu

Trên link Ethernet IPv4, host cần ánh xạ IP next hop sang MAC để gửi frame. ARP request tìm chủ của một IPv4 trên link bằng broadcast, reply thường là unicast. Switch chuyển frame theo MAC trong broadcast domain; router chuyển IP packet giữa các mạng, không kéo ARP broadcast đi khắp Internet.

## Dự đoán trước khi chạy

1. Muốn tới host cùng subnet, bạn cần MAC của host đó hay luôn là MAC gateway?
2. Nếu neighbor cache đã có entry dùng được, mỗi request HTTP có nhất thiết phát một ARP request mới không?

## Challenge

1. Ghi IP/prefix/MAC của interface lab và IP của một peer cùng subnet hoặc gateway được phép kiểm tra.
2. Xem `ip neigh`; chọn interface và capture filter ARP phù hợp bằng `tcpdump` trong lab.
3. Tạo traffic có giới hạn tới peer. Đọc sender/target IP và MAC trong ARP, tách biệt với packet IP của phép thử.
4. Vẽ một link có hai host và switch/bridge khái niệm; đánh dấu phạm vi broadcast.

## Kiểm chứng và đối chứng

Không thấy ARP mới chưa chứng minh ARP hỏng: kiểm tra cache, interface capture và loại link. Không flush cache host đang dùng để ép có output; bài 03b sẽ làm mới entry có kiểm soát trong lab riêng.

## Bằng chứng và điều kiện đạt

- [ ] Có neighbor output và ARP request/reply thực tế, hoặc ghi thiếu bằng chứng để tiếp tục ở 03b, chưa đánh dấu đạt.
- [ ] Chỉ đúng L2/L3 addresses, giải thích vì sao ARP giới hạn trên link và liên quan đến next hop.
- [ ] Capture không chứa traffic ngoài phạm vi được phép; lưu [notes](../templates/LESSON_NOTE.md).

**Tiếp theo:** [03b — Neighbor cache và MAC của gateway](03b-neighbor-cache-va-gateway.md).
