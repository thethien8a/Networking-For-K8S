# Ghi chú và bằng chứng học tập

Chưa có phiên học nào được ghi nhận. Không tạo sẵn ghi chú có kết quả giả.

- Mỗi bài dùng `notes/<ID>.md`, ví dụ `notes/01a.md`, tạo từ [LESSON_NOTE](../templates/LESSON_NOTE.md).
- Nếu có nhiều lần thử, nối thêm mục lần 2/3 với ngày và thay đổi; giữ dự đoán/output thất bại có giá trị.
- Case riêng có thể dùng `notes/20a-case-1.md`, `notes/26a-case-1.md`, `notes/27b-incident.md` theo [INCIDENT_REPORT](../templates/INCIDENT_REPORT.md), rồi link từ ghi chú bài và `PROGRESS.md`.
- Báo cáo cuối khóa dùng `notes/network-readiness-report.md` theo [mẫu capstone](../templates/NETWORK_READINESS_REPORT.md).
- Đường dẫn trên là quy ước cho file tương lai, không phải các ghi chú đã tồn tại.

Mỗi output ghi rõ ngày, máy/context/namespace, lệnh, dự đoán và ý nghĩa. Đánh dấu nội dung mô phỏng; không đặt output mẫu cạnh output thật mà thiếu nhãn.

Không lưu secrets, kubeconfig, private key hoặc dữ liệu công ty chưa được phép chia sẻ. Pcap/log có thể nhạy cảm: chỉ capture phạm vi lab, lọc/redact trước khi lưu và kiểm tra chính sách trước khi đưa vào Git hoặc gửi cho LLM. Trạng thái hoàn thành chỉ ghi ở [PROGRESS.md](../PROGRESS.md).
