# 09a — Đọc trạng thái mạng một Linux host

**Buổi gốc:** 09 · **Thời lượng:** 30–45 phút.  
**Trước bài:** [08b](08b-nat-port-mapping-va-cp2.md) và CP2 đạt.  
**Môi trường:** Ubuntu lab; chỉ quan sát trong bài này.

## Mục tiêu

- Lập inventory interface, addresses, routes và neighbors mà không thay cấu hình.
- Vẽ đúng ranh giới host/VM/container thay vì gom tất cả virtual NIC vào một mạng.

## Mô hình cần hiểu

Interface là điểm gắn vào link; address/prefix và routes mô tả cách host dùng các link đó. Loopback, NIC ảo của VM và interface container không cùng vai trò dù đều xuất hiện trong output. Output thuộc network namespace đang đọc, không đại diện cho toàn bộ runtime hay mọi Pod.

## Dự đoán trước khi chạy

1. Từ môi trường đang dùng, bạn mong đợi có interface nào, IP nào và default gateway nào?
2. Một interface có IP nhưng link không hoạt động thì inventory và connectivity có thể lệch ra sao?

## Challenge

1. Ghi hostname, OS và nơi truy cập; dùng `ip -br addr`, `ip link`, `ip route`, `ip neigh` để thu thập trạng thái.
2. Lập bảng interface → loại/phạm vi → IP/prefix → route liên quan. Ghi “chưa xác định” nếu chưa có bằng chứng về loại interface.
3. Vẽ host và các link đã thấy; chỉ ra next hop cho một đích cùng subnet và một đích ngoài subnet bằng route lookup.
4. So sánh với sơ đồ bạn dự đoán; giải thích một khác biệt thực tế.

## Kiểm chứng và đối chứng

Inventory không phải chứng nhận connectivity. Không xóa interface “lạ” hoặc sửa route để làm output đẹp. Nếu quan sát VM công ty, chỉ dùng lệnh và dữ liệu được phép, ẩn danh trước khi lưu/chia sẻ.

## Bằng chứng và điều kiện đạt

- [ ] Có bảng inventory và output gắn rõ máy/network namespace.
- [ ] Sơ đồ khớp interface/IP/routes đã quan sát, tách giả định còn thiếu.
- [ ] Có giải thích route cho hai loại đích và [ghi chú](../templates/LESSON_NOTE.md), không phát sinh thay đổi mạng.

**Tiếp theo:** [09b — Sockets và connectivity matrix](09b-sockets-va-connectivity-matrix.md).
