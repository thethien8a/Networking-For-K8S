# 25a — Pod egress, source address và SNAT

**Buổi gốc:** 25 · **Thời lượng:** 45–60 phút.  
**Trước bài:** [24b](24b-networkpolicy-dns-egress-va-cp6.md) và CP6 đạt.  
**Môi trường:** Pod/node lab và endpoint ngoài cluster nhưng thuộc lab được phép đo; không dùng dịch vụ công ty hoặc public echo service để lộ dữ liệu.

## Mục tiêu

- So sánh reachability từ Pod và từ node tới cùng external endpoint.
- Xác định source address bên ngoài quan sát được và phạm vi kết luận về SNAT/return path.

## Mô hình cần hiểu

Egress có thể đổi source ở CNI/node hoặc ở lớp runtime/router khác, tùy cấu hình. Endpoint thấy cùng một IP cho Pod và node chưa đủ xác định điểm NAT nằm ở đâu. Cần đối chiếu topology, routes/rules và packet/peer evidence; HTTP forwarded headers không tự chứng minh source IP của kết nối TCP.

## Dự đoán trước khi chạy

1. Endpoint ngoài cluster sẽ thấy Pod IP, node IP hay một địa chỉ khác? Giả định nào dẫn đến dự đoán đó?
2. Node gọi được nhưng Pod không gọi được giúp loại trừ điều gì, còn chưa loại trừ điều gì?

## Challenge

1. Chọn endpoint lab có thể quan sát TCP peer/capture, ghi IP/port, DNS và quyền truy cập; đo baseline từ node và Pod.
2. Lập bảng source quan sát tại Pod/node/endpoint và route chiều về; gắn nhãn điểm dịch đã xác minh và còn suy đoán.
3. Người dạy tạo case Pod vào nội bộ được nhưng không tới external endpoint, chỉ trên lab cô lập và có rollback.
4. Lần lượt kiểm tra DNS, TCP/listener, route, egress policy và SNAT/return path theo giả thuyết; sửa đúng một biến, test lại cả node/Pod/internal control.

## Break & Fix

Không thêm MASQUERADE rộng hoặc tắt egress policy để chữa mọi lỗi. Nếu source bị đổi ở nhiều lớp, ghi từng ranh giới chưa thể quan sát; không gọi CNI SNAT là root cause khi packet evidence chỉ chứng minh NAT ở đâu đó.

## Bằng chứng và điều kiện đạt

- [ ] Có node-vs-Pod reachability matrix và source address evidence tại endpoint thật của lab.
- [ ] Có root cause case egress, trước/sau/control và giải thích đường về.
- [ ] Có rollback/cleanup và [notes](../templates/LESSON_NOTE.md), không lộ địa chỉ/dữ liệu công ty.

**Tiếp theo:** [25b — MTU và PMTU](25b-mtu-pmtu.md).
