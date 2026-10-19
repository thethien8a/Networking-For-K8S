# 13a — Từ container namespace đến mạng dùng chung của Pod

**Buổi gốc:** 13 · **Thời lượng:** 30–45 phút.  
**Trước bài:** [12b](12b-drop-reject-va-cp3.md) và CP3 đạt.  
**Môi trường:** Kubernetes lab cá nhân, context/namespace đã xác nhận; image có công cụ phù hợp kiến trúc và quyền.

## Mục tiêu

- Ánh xạ kiến thức netns sang container và Pod.
- Chứng minh hai container cùng Pod dùng chung localhost và không gian port.

## Mô hình cần hiểu

Các container trong một Pod thông thường dùng chung network namespace, nên cùng thấy Pod IP, loopback và không gian ports. Hai Pod khác nhau thông thường có network namespaces khác nhau. Khai báo `containerPort` mô tả port trong manifest, không tự tạo listener hoặc expose dịch vụ ra ngoài.

## Dự đoán trước khi chạy

1. Container client trong cùng Pod gọi `127.0.0.1` có thể tới listener của container server không?
2. Hai container cùng Pod cùng bind một IP/protocol/port sẽ xung đột như thế nào?

## Challenge

1. Tạo Pod lab có hai container: một server, một client/debug. Chọn image/command theo công cụ thật, không giả định image nào cũng có shell hoặc `curl`.
2. Từ container client gọi server qua localhost; quan sát IP/listener trong phạm vi được phép.
3. Tạo Pod client thứ hai; so sánh localhost của Pod đó với Pod IP của server, ghi rõ server bind address.
4. Vẽ ranh giới node → Pod network namespace → hai container; so sánh với topology namespace ở bài 10.

## Kiểm chứng và đối chứng

Nếu phép thử qua Pod IP thất bại khi server chỉ bind loopback, đó chưa phải bằng chứng CNI hỏng. Có thể giữ manifest/Pod cho 13b nếu ghi ownership; không dùng namespace công ty cho thử nghiệm này.

## Bằng chứng và điều kiện đạt

- [ ] Có request localhost trong cùng Pod và phép so sánh với Pod khác, ghi đúng tên container/Pod.
- [ ] Giải thích shared network namespace, port space và vai trò giới hạn của `containerPort`.
- [ ] Có sơ đồ/output và [notes](../templates/LESSON_NOTE.md), cùng kế hoạch cleanup.

**Tiếp theo:** [13b — Bind address và hostNetwork](13b-bind-address-va-hostnetwork.md).
