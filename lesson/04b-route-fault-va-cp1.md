# 04b — Khoanh vùng lỗi routing và Checkpoint 1

**Buổi gốc:** 04 · **Thời lượng:** 45–60 phút.  
**Trước bài:** [04a](04a-routing-table.md).  
**Môi trường:** topology Linux lab cô lập do LLM hỗ trợ chuẩn bị; không đổi default route của host đang SSH.

## Mục tiêu

- Dùng IP/prefix, ARP và route để phân biệt các nguyên nhân mất kết nối.
- Hoàn thành [CP1](../CHECKPOINTS.md#cp1--sau-04b) trước khi học giao thức sâu hơn.

## Mô hình cần hiểu

Một địa chỉ hợp lệ chưa bảo đảm có đường đi đúng, next hop reachable hay đường về. Chẩn đoán cần chỉ ra điều kiện nào bị vi phạm, không chỉ thêm route cho đến khi ping được. Lỗi route cụ thể hơn có thể che route tổng quát vốn đúng.

## Dự đoán trước khi chạy

1. Nếu một route cụ thể trỏ sai next hop, default route đúng có tự cứu được luồng đó không?
2. Phép đo nào giúp tách lỗi chọn route với lỗi không tìm được MAC gateway?

## Challenge

1. Ghi baseline của một kết nối và routing/neighbor state trong lab riêng; chuẩn bị lệnh hoàn nguyên chính xác.
2. Người dạy tạo một lỗi route có giới hạn trong topology lab, chưa công bố nguyên nhân. Học viên ghi giả thuyết trước khi đo.
3. So sánh IP/prefix, `ip route get`, neighbor state và traffic phù hợp; chọn một thay đổi tối thiểu rồi kiểm tra lại.
4. Thực hiện đề CP1: subnet membership, 5 route decisions, giải thích next hop và ít nhất 3 lý do IP hợp lệ vẫn không liên lạc.

## Break & Fix

Không flush routes hay “reset network”. Nếu chưa đủ môi trường để thực hành, có thể phân tích đề trên giấy nhưng CP1 chưa đạt. Hoàn nguyên fault và kiểm tra luồng từng lỗi lẫn một luồng control vốn hoạt động.

## Bằng chứng và điều kiện đạt

- [ ] Có dự đoán, route/ARP evidence, root cause và trước/sau fix trong [notes](../templates/LESSON_NOTE.md).
- [ ] Có rollback/dọn lab được kiểm tra, không để mất kết nối quản trị.
- [ ] Đạt toàn bộ tiêu chí CP1 và ghi kết quả trung thực vào [PROGRESS.md](../PROGRESS.md).

**Tiếp theo:** [05a — Port và socket](05a-port-socket-tcp-udp.md), chỉ khi CP1 đã đạt.
