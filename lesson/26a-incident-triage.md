# 26a — Incident triage: nhiều lỗi, từng giả thuyết

**Buổi gốc:** 26 · **Thời lượng:** 45–60 phút cho lượt đầu; có thể chia tiếp nếu case chưa xong.  
**Trước bài:** [25b](25b-mtu-pmtu.md).  
**Môi trường:** 3–4 case lab tách biệt được chuẩn bị theo CNI/topology thật, không biết trước nguyên nhân.

## Mục tiêu

- Áp dụng quy trình điều tra nhất quán cho lỗi DNS, Service, cross-node hoặc policy.
- Phân biệt control plane metadata với data plane và thu đủ bằng chứng trước khi sửa.

## Mô hình cần hiểu

Điều tra tốt giảm không gian giả thuyết bằng phép đo có khả năng phân biệt, không phải chạy một danh sách lệnh thật dài. Nhiều case có thể có triệu chứng giống nhau, nên mỗi kết luận cần gắn với một flow và thời điểm cụ thể. Mỗi case chỉ thay một biến ở mỗi lần thử để còn biết tác dụng của fix.

## Dự đoán trước khi chạy

1. Bằng chứng tối thiểu nào khiến bạn chọn kiểm tra DNS thay vì CNI cho case đầu?
2. Thông tin API nhìn đúng nhưng traffic lỗi thì cần đo ở đâu để không lặp `kubectl get` mãi?

## Challenge

1. Người dạy chuẩn bị catalogue 3–4 case độc lập, chọn loại lỗi từ DNS, selection/mapping, cross-node path và policy; không gắn nhãn nguyên nhân khi giao.
2. Với từng case, ghi scope, triệu chứng, baseline/control và ít nhất hai giả thuyết cạnh tranh.
3. Chọn phép đo phân biệt tiếp theo; ghi timeline, output thật và lý do thay đổi giả thuyết. Xác nhận root cause trước khi sửa.
4. Lưu [incident report](../templates/INCIDENT_REPORT.md) từng case. Có thể xử lý một phần ở 26a và tiếp tục các case còn lại ở 26b/phiên sau; không gộp nhiều faults chưa kiểm soát vào cùng namespace.

## Break & Fix

Không restart tất cả hay thay nhiều trường cùng lúc. Fault injection chỉ trong lab riêng theo [LAB.md](../LAB.md). Nếu phải dừng giữa case, ghi resource/fault còn tồn tại, rollback và đúng phép thử tiếp theo, không bỏ lab ở trạng thái không ai biết.

## Bằng chứng và điều kiện đạt

- [ ] Có triage timeline và root cause được xác minh cho ít nhất một case đầu, không chỉ đoán từ triệu chứng.
- [ ] Có danh sách đủ 3–4 case được giao và trạng thái từng case để tiếp tục; chưa tuyên bố cả bộ đã hoàn tất.
- [ ] Có link reports và bàn giao thay đổi/fault an toàn trong [notes](../templates/LESSON_NOTE.md); bộ case chỉ hoàn thành ở 26b.

**Tiếp theo:** [26b — Fix, rollback và regression checks](26b-post-fix-regression.md).
