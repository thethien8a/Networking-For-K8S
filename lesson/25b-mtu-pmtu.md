# 25b — MTU, fragmentation và PMTU khi troubleshoot

**Buổi gốc:** 25 · **Thời lượng:** 45–60 phút.  
**Trước bài:** [25a](25a-pod-egress-snat.md).  
**Môi trường:** path lab riêng có thể quan sát, và topology Linux cô lập nếu cần thay MTU; không sửa NIC quản trị hoặc CNI cluster dùng chung.

## Mục tiêu

- Phân biệt interface MTU, path MTU và kích thước payload ứng dụng.
- Điều tra case gói nhỏ được/gói lớn lỗi bằng phép đo, không đoán MTU từ tên CNI.

## Mô hình cần hiểu

MTU giới hạn kích thước packet ở link; path MTU chịu ràng buộc bởi link nhỏ nhất trên đường xét. Encapsulation thêm overhead nên usable size cho inner packet có thể nhỏ hơn. PMTU discovery, fragmentation và TCP segmentation tương tác theo protocol/config; số byte payload của công cụ ping không đồng nhất với toàn bộ IP packet.

## Dự đoán trước khi chạy

1. Ping nhỏ thành công có loại trừ lỗi đường truyền với packet lớn hơn không?
2. Một HTTP response lớn có nhất thiết tương ứng một packet IP rất lớn không?

## Challenge

1. Đọc MTU các interface quan sát được và ghi data path/CNI mode từ bài 15; chưa điền overhead bằng con số học thuộc.
2. Chọn công cụ phù hợp Linux thực tế như `tracepath` hoặc ping có kiểm soát kích thước/DF sau khi kiểm tra manual. Dự đoán trước rồi thử hai phía một ngưỡng có ý nghĩa, tính cả headers.
3. Nếu cần tạo lỗi, dùng topology namespace riêng có rollback MTU/rule của bài; không sửa node network để ép failure.
4. Đối chiếu capture, ICMP/errors, retransmits và app traffic nhỏ/lớn; phân biệt bằng chứng về PMTU với suy đoán do timeout.

## Break & Fix

Packet lớn lỗi còn có thể do rate limiting, policy hoặc server behavior. Chọn control phân biệt và thay một biến; không dùng hạ MTU toàn cluster làm fix khi chưa chứng minh. Hoàn nguyên MTU/rules lab và kiểm tra ngưỡng cũ cùng app control.

## Bằng chứng và điều kiện đạt

- [ ] Có MTU inventory, kích thước thử/header assumptions, kết quả hai phía ngưỡng và bằng chứng path phù hợp.
- [ ] Giải thích small-ping success, payload-vs-packet và ảnh hưởng encapsulation đúng mức đã xác minh.
- [ ] Có case lab/đối chứng, rollback được kiểm tra và [notes](../templates/LESSON_NOTE.md); nếu môi trường không cho đo đủ thì ghi phần thực hành còn thiếu.

**Tiếp theo:** [26a — Incident triage](26a-incident-triage.md).
