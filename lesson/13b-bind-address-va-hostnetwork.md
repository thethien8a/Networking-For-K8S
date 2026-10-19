# 13b — Bind address của Pod và trường hợp hostNetwork

**Buổi gốc:** 13 · **Thời lượng:** 30–45 phút.  
**Trước bài:** [13a](13a-pod-shared-network-namespace.md).  
**Môi trường:** Pod lab riêng; `hostNetwork` chỉ thực nghiệm nếu lab và policy cho phép, không cần bật để hoàn thành phần so sánh khái niệm.

## Mục tiêu

- Debug sự khác biệt localhost trong Pod với truy cập từ Pod khác.
- Giải thích `hostNetwork` thay đổi ranh giới mạng và có thể tạo xung đột port trên node.

## Mô hình cần hiểu

Server bind loopback trong Pod có thể phục vụ container cùng Pod nhưng không phục vụ kết nối tới Pod IP theo cách bind đó. `hostNetwork` dùng network namespace của node thay vì mạng Pod riêng thông thường; đây không phải nút “sửa mọi lỗi CNI”. Node kind là container Linux, không phải network namespace của máy Mac.

## Dự đoán trước khi chạy

1. Cùng một listener bind `127.0.0.1`, client cùng Pod và client khác Pod sẽ thấy gì?
2. Với hostNetwork trên node kind, “localhost của node” có tự trở thành localhost của Mac không?

## Challenge

1. Lập matrix client cùng Pod/khác Pod × localhost/Pod IP với listener loopback; ghi dự đoán trước.
2. Quan sát listener và traffic; chỉ đổi bind của server lab để đáp ứng hướng kết nối mong muốn, rồi đo lại.
3. Đọc ngữ nghĩa `hostNetwork` theo tài liệu/version hiện tại và vẽ so sánh Pod thường/hostNetwork. Nếu không chạy thực nghiệm phần này, ghi rõ đó là phân tích cấu hình.

## Break & Fix

Nhận case “sidecar gọi được, Pod khác không gọi được”. Kiểm tra bind, port và namespace trước khi đụng CNI. Không thêm hostNetwork hoặc privileged mode để né lỗi ứng dụng; nếu có thực nghiệm hostNetwork phải chọn port không xung đột và dọn ngay sau bài.

## Bằng chứng và điều kiện đạt

- [ ] Có matrix và output trước/sau sửa bind trong Pod thực tế.
- [ ] Giải thích đúng ranh giới container/Pod/node/Mac và khác biệt hostNetwork; gắn nhãn mức kiểm chứng.
- [ ] Dọn Pod/tài nguyên bài tạo hoặc bàn giao rõ; lưu [notes](../templates/LESSON_NOTE.md).

**Tiếp theo:** [14a — CNI và same-node placement](14a-cni-va-same-node-placement.md).
