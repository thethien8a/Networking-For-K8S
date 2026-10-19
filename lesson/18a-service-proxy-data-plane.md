# 18a — Service proxy và packet steering theo cluster thật

**Buổi gốc:** 18 · **Thời lượng:** 30–45 phút.  
**Trước bài:** [17b](17b-selector-mismatch.md).  
**Môi trường:** ClusterIP lab hoạt động; quan sát proxy/rules/maps theo quyền và data plane thực tế.

## Mục tiêu

- Phân biệt Service API với cơ chế chuyển traffic đến backend.
- Nhận diện kube-proxy mode hoặc cơ chế thay thế theo cấu hình thật, không mặc định iptables.

## Mô hình cần hiểu

ClusterIP cần data plane xử lý packet/connection tới backend; nó không nhất thiết có một process listen ở ClusterIP:port để thấy trong `ss`. kube-proxy và các mode như iptables/nftables là một nhóm triển khai; CNI/data plane eBPF có thể thay thế tùy cấu hình. Phân biệt các mode ở mức cơ chế rồi kiểm tra mode đang dùng, không suy từ firewall tool trên OS. DNAT là một cơ chế cần hiểu, nhưng phải xác minh nó có mô tả đúng flow đang xét không.

## Dự đoán trước khi chạy

1. Không thấy ClusterIP listener trong `ss` có đủ kết luận Service hỏng không?
2. Đổi backend có thể làm metadata và rule/map cập nhật ở những thành phần nào?

## Challenge

1. Xác định proxy/data plane từ cấu hình, component status và tài liệu đúng version; cập nhật phần đã xác minh trong [LAB.md](../LAB.md).
2. Dự đoán flow client Pod → ClusterIP:port → backend Pod:port và chiều về.
3. Gửi request mới có response nhận diện backend; quan sát rule/map/counter/capture phù hợp khi quyền cho phép, không chỉ tìm iptables bằng thói quen.
4. Vẽ packet path thực tế, đánh dấu bước đổi địa chỉ hoặc steering được xác minh và điểm còn unknown.

## Kiểm chứng và đối chứng

Không sửa rules/maps do proxy quản lý để thử backend. Một kết nối keep-alive có thể tiếp tục tới một backend, nên không lấy chuỗi HTTP requests trong cùng connection làm bằng chứng thuật toán phân phối tổng quát.

## Bằng chứng và điều kiện đạt

- [ ] Có bằng chứng loại proxy/data plane, source/version cho phần giải thích.
- [ ] Có request tới ClusterIP và backend identity, sơ đồ phân biệt API metadata với packet steering.
- [ ] Ghi rõ giới hạn quan sát, không mặc định kube-proxy/iptables; lưu [notes](../templates/LESSON_NOTE.md).

**Tiếp theo:** [18b — Port, targetPort và listener](18b-port-targetport-listener.md).
