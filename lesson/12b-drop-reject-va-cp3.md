# 12b — DROP, REJECT, rollback và Checkpoint 3

**Buổi gốc:** 12 · **Thời lượng:** 45–60 phút.  
**Trước bài:** [12a](12a-firewall-va-conntrack.md).  
**Môi trường:** topology hai subnet qua router namespace riêng, console/rollback sẵn sàng theo [LAB.md](../LAB.md).

## Mục tiêu

- So sánh DROP và REJECT bằng phản hồi thật, xác định rule/hook gây lỗi.
- Hoàn thành [CP3](../CHECKPOINTS.md#cp3--sau-12b) với cấu hình, capture và cleanup thực tế.

## Mô hình cần hiểu

DROP bỏ packet mà không gửi phản hồi từ rule đó; REJECT chủ động phản hồi theo loại được cấu hình. Biểu hiện ở client còn phụ thuộc protocol, path và timers. Counter/rule evidence cùng capture mạnh hơn việc chỉ thấy chữ “timeout” hay “refused”.

## Dự đoán trước khi chạy

1. Cùng một TCP port bị DROP và bị REJECT có khác về thời gian/packet phản hồi không?
2. Nếu rule stateful cho phép established traffic, dùng lại kết nối cũ có thể làm bài thử sai thế nào?

## Challenge

1. Dùng topology CP3 có HTTP baseline; xác nhận tài nguyên thuộc lab và lệnh rollback chính xác trước khi thêm rule.
2. Lần lượt thử rule DROP và REJECT cho một luồng; giữa hai lượt hoàn nguyên, mở kết nối TCP mới và giữ control không bị chặn.
3. Nhận thêm một rule gây lỗi chưa nêu vị trí; tìm chain/hook và điều kiện match bằng quan sát rồi sửa tối thiểu.
4. Chạy đánh giá CP3: chứng minh hai subnet qua router hoạt động, xử lý lỗi route và firewall, rồi dọn tài nguyên/rules của chuỗi lab.

## Break & Fix

Không flush firewall hoặc conntrack toàn host. Không xóa namespace/tài nguyên không thuộc bài. Cleanup phải có output xác minh không còn tài nguyên thử và kết nối quản trị/control vẫn hoạt động; nếu giữ thứ gì, chưa gọi là đã dọn hết.

## Bằng chứng và điều kiện đạt

- [ ] Có bảng DROP/REJECT với client timing, counters/capture và kết nối mới/control.
- [ ] Có đủ topology/routes/capture, root cause routing/firewall và lệnh rollback đã thực hiện của CP3.
- [ ] Lưu [notes](../templates/LESSON_NOTE.md), ghi kết quả CP3 vào [PROGRESS.md](../PROGRESS.md); thiếu capture/rollback thực tế thì chưa đạt cổng.

**Tiếp theo:** [13a — Network namespace của Pod](13a-pod-shared-network-namespace.md), chỉ khi CP3 đã đạt.
