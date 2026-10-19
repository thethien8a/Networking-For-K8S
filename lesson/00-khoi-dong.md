# 00 — Khởi động: biết mình học ở đâu và lưu gì

**Thời lượng:** 20–30 phút, không tính vào 56 bài chính.  
**Trước bài:** đọc [README](../README.md) và [quy tắc lab](../LAB.md). Không cần cài toàn bộ công cụ ngay.

## Mục tiêu

- Phân biệt máy Mac, Linux lab, node kind và VM công ty; biết môi trường nào được phép tạo lỗi.
- Xác nhận điểm xuất phát và chuẩn bị cách lưu ghi chú/tiến độ.

## Đánh giá đầu vào

Trả lời bằng hiểu biết hiện tại, không cần tra trước: IP và port dùng để xác định gì? `localhost` của một container có phải máy Mac không? DNS thành công có chứng minh ứng dụng truy cập được không? Bạn đã dùng Pod/Service và Linux CLI đến đâu?

LLM dùng câu trả lời để chọn cách giải thích, không dùng để bỏ qua bài hoặc ghi kỹ năng đã đạt khi chưa có bằng chứng.

## Dự đoán trước khi kiểm tra

1. Bạn nghĩ HTTP server chạy trong Ubuntu lab có thể được truy cập từ những client nào?
2. Nếu gõ `ip netns` trên macOS, có đang thao tác cùng kernel với Linux lab không?

## Challenge

1. Ghi thời lượng học thực tế và mục tiêu cá nhân vào [hồ sơ](../STUDENT_PROFILE.md), chỉ phần bạn xác nhận.
2. Xác định một Linux lab riêng cho các bài netns/firewall và một cluster cá nhân cho phần Kubernetes. Nếu chưa có, ghi kế hoạch/blocker; không tự tạo cluster hoặc VM chỉ để điền bảng.
3. Kiểm tra read-only những công cụ đã có trên đúng máy; điền phần xác minh được vào [LAB.md](../LAB.md). Công cụ chưa cần dùng có thể để chưa xác minh.
4. Thống nhất nơi lưu `notes/<ID>.md` theo [mẫu ghi chú](../templates/LESSON_NOTE.md). Tạo ghi chú bài 00 khi thực sự học.

## Kiểm chứng và an toàn

Chỉ cần sẵn sàng cho bài 01a để bắt đầu phần chính; chưa cần CNI hỗ trợ policy hay Spark. Không dán kubeconfig/token hoặc thông tin công ty chưa được phép chia sẻ. Không thay cấu hình mạng trong bài này.

## Bằng chứng và điều kiện đạt

- [ ] Xác định được máy thực thi và một HTTP lab riêng khả dụng cho bài 01a; có blocker cụ thể nếu chưa có.
- [ ] Nêu đúng ranh giới lab cá nhân và môi trường công ty; thống nhất không fault injection ở công ty.
- [ ] Có ghi chú bài 00 với đánh giá đầu vào, thông tin đã xác nhận và kế hoạch cho phần môi trường còn thiếu.

**Tiếp theo:** [01a — HTTP request và endpoint](01a-http-request-va-endpoint.md). Chỉ bắt đầu khi môi trường bài đầu đã sẵn sàng.
