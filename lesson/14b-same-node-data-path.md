# 14b — Quan sát Pod-to-Pod data path cùng node

**Buổi gốc:** 14 · **Thời lượng:** 30–45 phút.  
**Trước bài:** [14a](14a-cni-va-same-node-placement.md).  
**Môi trường:** hai Pod lab cùng node; node-level quan sát chỉ khi có quyền, không nâng quyền tự động.

## Mục tiêu

- Vẽ đường đi theo CNI thực tế, phân biệt quan sát với suy luận từ tài liệu.
- Tách lỗi listener khỏi lỗi đường truyền Pod.

## Mô hình cần hiểu

Hai Pod cùng node vẫn đi qua ranh giới network namespace. Cách frame/packet được chuyển ở phía node tùy CNI và data plane; ví dụ veth–bridge không phải chân lý cho mọi cluster. Packet capture ở một điểm không thấy packet có thể do đường đi/capture point, chưa chứng minh packet bị drop ở đó.

## Dự đoán trước khi chạy

1. Theo CNI đã xác minh, bạn nghĩ packet đi qua những interface/cơ chế nào?
2. Nếu server đổi port hoặc bind loopback, node placement và CNI có nhất thiết thay đổi không?

## Challenge

1. Từ Pod client/server, đọc interface/address/route/listener bằng công cụ và quyền sẵn có.
2. Bổ sung quan sát phía node hoặc tài liệu cấu hình đúng version khi được phép. Với mỗi đoạn data path, gắn nhãn observed/documented/unknown.
3. Chạy request có dấu hiệu nhận diện ở backend; nối tuple và thời gian với output/capture phù hợp.
4. Nhận một lỗi listen port/interface trên server lab, chọn phép đo phân biệt với path failure rồi sửa đúng ứng dụng.

## Break & Fix

Không đổi CNI hoặc recreate cluster khi listener sai. Nếu thiếu quyền quan sát node, giữ đoạn node là giới hạn chưa xác minh và dùng bằng chứng endpoints thật, không vẽ bridge/VXLAN như đã thấy. Dọn tài nguyên hoặc bàn giao cho bài cross-node.

## Bằng chứng và điều kiện đạt

- [ ] Có sơ đồ same-node với nguồn chứng cứ từng đoạn và request backend thực nhận.
- [ ] Có lỗi listener và trước/sau fix, giải thích vì sao chưa phải lỗi CNI.
- [ ] Có [notes](../templates/LESSON_NOTE.md), post-fix/control test và trạng thái cleanup/bàn giao.

**Tiếp theo:** [15a — Underlay và overlay](15a-underlay-overlay-cross-node.md).
