# 06b — Connect timeout, application timeout và retry

**Buổi gốc:** 06 · **Thời lượng:** 30–45 phút.  
**Trước bài:** [06a](06a-refused-timeout-reset.md).  
**Môi trường:** HTTP/TCP lab riêng, client có thể giới hạn thời gian và số lần thử.

## Mục tiêu

- Chỉ ra giai đoạn kết nối bị kẹt thay vì gom mọi lỗi vào “mạng chậm”.
- Giải thích retry hữu hạn không thay cho tìm nguyên nhân.

## Mô hình cần hiểu

Ứng dụng có thể hết thời gian trước khi TCP connect xong hoặc sau khi kết nối đã thiết lập nhưng chưa nhận phản hồi mong đợi. Phạm vi timer phụ thuộc công cụ, nên cần xem trợ giúp thực tế. Retry làm phát sinh lần thử mới; nó không tạo listener, sửa route hay bảo đảm thao tác ghi dữ liệu an toàn khi lặp.

## Dự đoán trước khi chạy

1. Server nhận TCP nhưng trì hoãn HTTP response: capture khác gì với SYN không được trả lời?
2. Tăng timeout có thể làm request hoàn tất ở case nào, và chỉ khiến chờ lâu hơn ở case nào?

## Challenge

1. So sánh một case chưa connect được với một server lab đã accept nhưng chậm phản hồi. Dùng `curl -v` và các giới hạn thời gian phù hợp sau khi đọc `curl --help`/manual của bản cài.
2. Ghi mốc bắt đầu, kết nối, phản hồi/timeout; đối chiếu capture và trạng thái server.
3. Thử số lần retry nhỏ, hữu hạn cho request đọc dữ liệu lab; đếm số lần thật và giải thích kết quả. Không áp retry mù cho thao tác có side effect.

## Break & Fix

Nhận một case không gắn nhãn từ người dạy. Chọn phép đo giúp biết “chưa connect” hay “đã connect, đang đợi app” trước khi đề xuất sửa. Không coi retry thành công một lần là bằng chứng đã loại bỏ nguyên nhân.

## Bằng chứng và điều kiện đạt

- [ ] Có hai timeline khác nhau và packet/socket evidence xác định giai đoạn kẹt.
- [ ] Có bảng retry hữu hạn, giải thích giới hạn và rủi ro của lặp request.
- [ ] Sửa đúng nguyên nhân case, kiểm tra lại và dọn lab; lưu [notes](../templates/LESSON_NOTE.md).

**Tiếp theo:** [07a — Resolver và bản ghi DNS](07a-resolver-va-dns-records.md).
