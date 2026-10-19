# 10b — Hai namespace qua Linux bridge

**Buổi gốc:** 10 · **Thời lượng:** 45–60 phút.  
**Trước bài:** [10a](10a-network-namespace-va-veth.md).  
**Môi trường:** Linux lab cô lập, bridge và veth riêng của bài; không enslave NIC quản trị vào bridge thử nghiệm.

## Mục tiêu

- Dựng topology namespace A ↔ veth ↔ bridge ↔ veth ↔ namespace B.
- Giải thích L2 switching, ARP và giới hạn của bridge so với router.

## Mô hình cần hiểu

Bridge chuyển frame giữa các ports theo MAC, giúp nhiều namespace cùng tham gia một L2 segment. Hai namespace cùng subnet có thể giao tiếp mà bridge không cần IP làm gateway cho chúng. Bridge không tự cung cấp L3 routing giữa các subnet khác nhau.

## Dự đoán trước khi chạy

1. Đặt IP chỉ trên hai namespace có đủ cho kết nối cùng subnet qua bridge không?
2. Nếu một đầu veth phía bridge chưa UP hoặc chưa gắn bridge, capture sẽ dừng ở đâu?

## Challenge

1. Ghi topology mới và ownership. Nếu tái dùng 10a, lập kế hoạch chuyển/dọn rõ thay vì gắn chồng topology cũ không kiểm soát.
2. Tạo bridge riêng và hai veth pairs; mỗi namespace giữ một đầu, các đầu còn lại gắn vào bridge.
3. Cấu hình hai namespace cùng subnet, kiểm tra interface state, ping và HTTP.
4. Quan sát bridge ports/MAC learning bằng công cụ có sẵn; capture ARP và một luồng IP ở các điểm cần thiết nếu được phép.

## Break & Fix

Nhận một case thiếu link UP hoặc gắn sai bridge/namespace. Chứng minh chỗ đứt bằng membership/state/capture trước khi sửa. Chỉ thao tác tài nguyên bài sở hữu; không reset bridge của container runtime.

## Bằng chứng và điều kiện đạt

- [ ] Có sơ đồ actual topology, output namespace/bridge/ports và kết nối HTTP thành công.
- [ ] Có phân tích lỗi và giải thích vì sao đây là L2, không phải router giữa hai subnet.
- [ ] Có cleanup đã kiểm tra hoặc bàn giao rõ nếu giữ tài nguyên; lưu [notes](../templates/LESSON_NOTE.md).

**Tiếp theo:** [11a — Router namespace](11a-router-namespace-hai-subnet.md).
