# 03b — Neighbor cache và MAC của gateway

**Buổi gốc:** 03 · **Thời lượng:** 30–45 phút.  
**Trước bài:** [03a](03a-ethernet-mac-arp.md).  
**Môi trường:** link của Linux lab riêng; chỉ thay neighbor entry thuộc lab, không tác động host công ty.

## Mục tiêu

- So sánh cache còn dùng được với lần phải resolve lại neighbor.
- Phân biệt IP đích cuối và MAC next hop khi đích ở mạng khác.

## Mô hình cần hiểu

IP packet có thể hướng đến một đích xa nhưng frame ở hop đầu mang MAC gateway. Neighbor state mô tả việc theo dõi peer trên link; trạng thái như STALE không tự nó chứng minh kết nối đã hỏng. Đường ra ngoài subnet cần cả quyết định routing lẫn khả năng tới next hop.

## Dự đoán trước khi chạy

1. Khi truy cập IP ngoài subnet, neighbor table có nhất thiết chứa MAC của IP đích đó không?
2. Sau khi xóa đúng một entry lab, traffic đầu tiên và traffic sau đó có thể khác ở đâu?

## Challenge

1. Chụp trạng thái neighbor trước traffic; đo một luồng cùng subnet và một luồng qua gateway nếu topology cho phép.
2. Trong phạm vi lab đã xác nhận, làm mới đúng neighbor entry thử nghiệm, không flush toàn bảng. Capture lại ARP và traffic.
3. Lập bảng đích cuối → next hop → MAC ở hop đầu, chú thích phần quan sát và giả định.

## Break & Fix

Phân tích case neighbor entry sai hoặc gateway không trả ARP bằng bằng chứng lab được chuẩn bị. Phân biệt “chưa tìm được MAC next hop” với “ứng dụng đích không listen”; không sửa DNS để chữa lỗi ARP. Hoàn nguyên entry thử nghiệm và xác minh kết nối trước khi kết thúc.

## Bằng chứng và điều kiện đạt

- [ ] Có capture trước/sau làm mới cache và chú thích ARP request/reply.
- [ ] Giải thích IP đích khác MAC next hop, không coi trạng thái cache riêng lẻ là root cause.
- [ ] Có kết quả kiểm tra sau hoàn nguyên và [ghi chú](../templates/LESSON_NOTE.md).

**Tiếp theo:** [04a — Đọc routing table](04a-routing-table.md).
