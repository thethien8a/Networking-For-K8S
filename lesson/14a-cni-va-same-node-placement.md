# 14a — CNI, Pod IP và hai Pod cùng node

**Buổi gốc:** 14 · **Thời lượng:** 30–45 phút.  
**Trước bài:** [13b](13b-bind-address-va-hostnetwork.md).  
**Môi trường:** Kubernetes lab có worker khả dụng; quyền xem nodes/Pod placement và thành phần mạng được cho phép.

## Mục tiêu

- Phân biệt node IP, Pod IP, Pod CIDR và vai trò của CNI.
- Đặt hai Pod lên cùng worker rồi chứng minh placement và connectivity.

## Mô hình cần hiểu

CNI là giao diện và hệ sinh thái plugin dùng để thiết lập networking cho container workload trong môi trường runtime tích hợp nó. Tên CNI chưa tự mô tả đầy đủ data path; cấu hình triển khai mới quyết định có bridge, routing hay cơ chế khác. Pod IP không phải IP của process trên máy Mac.

## Dự đoán trước khi chạy

1. Cùng node có làm hai Pod tự chia sẻ localhost không?
2. Nhìn hai Pod có IP “gần nhau” có chứng minh chúng nằm cùng node không?

## Challenge

1. Ghi nodes, roles, addresses và thông tin Pod CIDR được công bố nếu có; không suy ra toàn bộ IPAM chỉ từ một trường trống/có giá trị.
2. Xác định CNI từ deployment/config/docs lab phù hợp quyền đọc; ghi phần chưa xác minh trong [LAB.md](../LAB.md).
3. Dùng node placement dựa trên labels thật để đặt server và client lên cùng worker, không đổi labels/tình trạng node dùng chung.
4. Chứng minh nodeName/Pod IP thực tế, gọi trực tiếp Pod IP và kiểm tra listener phù hợp, chưa dùng Service.

## Kiểm chứng và đối chứng

Pod Pending vì placement/resource không phải ngay lập tức là lỗi mạng. Xác nhận cả hai Pod thực sự chạy trên node đã chọn trước khi dùng kết quả làm same-node baseline. Không mặc định mọi CNI tạo một Linux bridge giống bài 10.

## Bằng chứng và điều kiện đạt

- [ ] Có placement, node/Pod IP và HTTP/TCP connectivity qua Pod IP thật.
- [ ] Giải thích CNI và mối quan hệ với namespace/interface/routes, không gán chức năng Service discovery cho nó một cách mặc định.
- [ ] Có [notes](../templates/LESSON_NOTE.md), CNI facts đã xác minh và ownership của Pod giữ cho 14b.

**Tiếp theo:** [14b — Quan sát same-node data path](14b-same-node-data-path.md).
