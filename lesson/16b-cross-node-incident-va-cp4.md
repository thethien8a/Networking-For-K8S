# 16b — Cross-node incident và Checkpoint 4

**Buổi gốc:** 16 · **Thời lượng:** 45–60 phút.  
**Trước bài:** [16a](16a-cni-events-logs-triage.md).  
**Môi trường:** case lỗi được dựng sẵn trong cluster lab cô lập, nguyên nhân chưa công bố.

## Mục tiêu

- Điều tra case same-node hoạt động, cross-node lỗi mà không dựa vào đáp án.
- Hoàn thành [CP4](../CHECKPOINTS.md#cp4--sau-16b) và chỉ rõ giới hạn kind so với VMware.

## Mô hình cần hiểu

“Chỉ khác node mới lỗi” là một dấu hiệu khoanh vùng, chưa đủ để gọi CNI là root cause. Cần xác nhận placement, listener, Pod path và node network bằng bằng chứng liên kết. Sơ đồ data path phải phản ánh CNI mode thật, kể cả khi một số đoạn chỉ có tài liệu thay vì capture.

## Dự đoán trước khi chạy

1. Hai giả thuyết nào giải thích được triệu chứng, và phép thử nào cho kết quả khác nhau giữa chúng?
2. Nếu chuyển Pod cùng node làm lỗi biến mất, bạn đã sửa nguyên nhân hay chỉ né đoạn đường lỗi?

## Challenge

1. Nhận topology/triệu chứng và phạm vi được phép, chưa nhận nguyên nhân. Ghi baseline same/cross-node và packet path dự đoán.
2. Kiểm tra Pod → node → node network → Pod đích theo thứ tự do giả thuyết dẫn dắt; dùng events/logs/route/capture phù hợp.
3. Xác minh root cause bằng phép thử đối lập hoặc quan sát cấu hình liên quan, rồi áp fix nhỏ nhất được phép.
4. Viết [incident report](../templates/INCIDENT_REPORT.md) và review CP4, gồm ít nhất hai khác biệt kind/VM ảnh hưởng suy luận.

## Break & Fix

Không coi reschedule về same-node là fix cross-node đã đạt. Sau sửa, giữ placement khác node và test lại cùng ứng dụng/port, cộng control same-node. Hoàn nguyên fault và dọn phần bài sở hữu; ghi phần không thể quan sát ở VMware thay vì đoán vSwitch/VLAN.

## Bằng chứng và điều kiện đạt

- [ ] Có report root cause, CNI-specific data path, câu lệnh/quan sát và trước/sau/control tests.
- [ ] Có rollback xác minh và phân tích giới hạn môi trường.
- [ ] Đạt toàn bộ CP4, liên kết [notes](../templates/LESSON_NOTE.md) và cập nhật [PROGRESS.md](../PROGRESS.md).

**Tiếp theo:** [17a — Service và EndpointSlice](17a-service-selector-endpointslice.md), chỉ khi CP4 đã đạt.
