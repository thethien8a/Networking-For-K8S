# 15a — Underlay, overlay và Pod khác node

**Buổi gốc:** 15 · **Thời lượng:** 30–45 phút.  
**Trước bài:** [14b](14b-same-node-data-path.md).  
**Môi trường:** cluster cá nhân có ít nhất hai worker khả dụng và same-node baseline.

## Mục tiêu

- Phân biệt mạng nối nodes với mạng địa chỉ Pods.
- Chứng minh Pod-to-Pod cross-node và xác định có encapsulation hay routing theo triển khai thật.

## Mô hình cần hiểu

Underlay là mạng cung cấp kết nối giữa các node trong mô hình đang xét; overlay có thể tạo mạng logic trên đó bằng encapsulation. VXLAN là một ví dụ, không phải cơ chế bắt buộc của Kubernetes. Trong kind, lớp node-container/runtime tạo thêm ranh giới so với các VM nối qua VMware.

## Dự đoán trước khi chạy

1. Pod đích ở node khác làm data path thêm những đoạn nào so với same-node?
2. Nếu capture thấy outer packet giữa hai node, inner Pod addresses nằm ở đâu? Nếu không có overlay thì cần giải thích thế nào?

## Challenge

1. Đặt client lên worker 1, server lên worker 2 bằng labels/placement thực tế; xác minh nodeName và Pod IP.
2. Test Pod IP trực tiếp, giữ cùng ứng dụng/port và request như baseline same-node.
3. Đối chiếu CNI mode/config/docs và quan sát routes/interfaces/capture được phép để xác định cơ chế cross-node.
4. Vẽ hai chiều source Pod → source node → node network → destination node → destination Pod, chỉ rõ phần nào là container/VM boundary.

## Kiểm chứng và đối chứng

Không gọi traffic node-to-node thành công là bằng chứng mọi Pod path đều tốt. Không suy VXLAN từ việc đọc một bài blog dùng CNI khác. MTU có thể bị ảnh hưởng bởi encapsulation, nhưng chưa đo thì không điền con số hoặc kết luận lỗi MTU.

## Bằng chứng và điều kiện đạt

- [ ] Có cross-node placement và kết nối qua Pod IP, so với same-node baseline.
- [ ] Có sơ đồ theo CNI thật, source/version của nhận định và giới hạn quan sát.
- [ ] Phân biệt underlay/overlay và kind/VM, lưu [notes](../templates/LESSON_NOTE.md) cùng tài nguyên giữ cho 15b.

**Tiếp theo:** [15b — Đo và khoanh vùng cross-node](15b-cross-node-evidence-va-fault.md).
