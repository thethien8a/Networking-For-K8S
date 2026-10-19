# 02b — IP plan 4 VM và prefix không đồng nhất

**Buổi gốc:** 02 · **Thời lượng:** 30–45 phút.  
**Trước bài:** [02a](02a-ipv4-va-cidr.md).  
**Môi trường:** thiết kế trên giấy; thực nghiệm chỉ trên hai host/namespace lab cô lập được chuẩn bị, không sửa NIC VM công ty.

## Mục tiêu

- Lập bảng IP/prefix/gateway minh họa cho bốn VM.
- Dự đoán khác biệt route/ARP khi hai phía hiểu subnet không giống nhau.

## Mô hình cần hiểu

Mỗi host tự quyết định đích nào là on-link theo địa chỉ, prefix và routes của nó. Prefix không đồng nhất có thể làm hai phía chọn next hop khác nhau; không có quy tắc “khác prefix là luôn mất mạng”. Khả năng kết nối còn phụ thuộc topology, gateway và đường về.

## Dự đoán trước khi chạy

1. Nếu A coi B cùng subnet nhưng B coi A ngoài subnet, mỗi phía sẽ tìm MAC của ai?
2. Một gateway ngoài subnet có thể được coi là hợp lệ chỉ vì số IP nhìn gần giống không?

## Challenge

1. Chọn một dải private minh họa, ghi rõ chưa phải dải đã được doanh nghiệp cấp. Lập bảng cho 1 control-plane + 3 worker, không dùng network/broadcast làm host.
2. Kiểm tra đủ chỗ cho bốn VM và gateway, địa chỉ không trùng, gateway phù hợp với prefix. Ghi nguy cơ overlap với LAN/VPN là việc phải xác minh, không khẳng định dải đang rảnh.
3. Nhận topology lab hai phía có prefix không đồng nhất. Dự đoán đường đi từng chiều, rồi xem route selection/ARP do LLM hỗ trợ quan sát; chưa cần tự dựng netns ở giai đoạn này.

## Break & Fix

So sánh hai trạng thái prefix nhất quán/không nhất quán trong lab riêng. Nếu chỉ có bài mô phỏng, ghi rõ phần thực nghiệm còn thiếu. Không sửa prefix trên máy đang SSH và không kết luận do prefix nếu route/ARP thực tế không ủng hộ.

## Bằng chứng và điều kiện đạt

- [ ] Có IP plan 4 VM với network, usable range, prefix và gateway được giải thích.
- [ ] Có quan sát route/ARP của case lab hai chiều, hoặc tiếp tục `dang-hoc`/`bi-chan` nếu chưa thực nghiệm được.
- [ ] Phân biệt kế hoạch minh họa với địa chỉ được phép dùng thực tế; lưu [notes](../templates/LESSON_NOTE.md) và hoàn nguyên thay đổi lab.

**Tiếp theo:** [03a — Ethernet, MAC và ARP](03a-ethernet-mac-arp.md).
