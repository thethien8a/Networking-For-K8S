# 20a — Hai mini-incident Service, không biết trước nguyên nhân

**Buổi gốc:** 20 · **Thời lượng:** 45–60 phút.  
**Trước bài:** [19b](19b-dns-resolution-path.md).  
**Môi trường:** hai case độc lập trong cluster lab riêng, hoàn nguyên hoặc tách tài nguyên giữa các case.

## Mục tiêu

- Tự chọn phép đo phân biệt selection, mapping, listener, proxy và policy.
- Viết hai báo cáo điều tra với root cause và post-fix checks, không chỉ lưu lời giải.

## Mô hình cần hiểu

Triệu chứng “gọi Service không được” không xác định tầng lỗi. Chứng cứ về đối tượng API và thử kết nối phải bổ sung cho nhau. Case độc lập giúp bạn kiểm tra phương pháp chẩn đoán thay vì nhớ sửa đúng một trường YAML từ bài trước.

## Dự đoán trước khi chạy

1. Hãy nêu hai giả thuyết cho triệu chứng được giao và một phép đo mà kết quả sẽ khác giữa chúng.
2. Nếu EndpointSlice có address đúng, bạn đã loại trừ được gì và còn phải kiểm tra gì?

## Challenge

1. Người dạy giao case A chỉ với triệu chứng, nguồn/đích và phạm vi được phép. Fault catalogue của buổi 20 phải bao phủ selector và targetPort/listener, nhưng chưa gắn nguyên nhân với case.
2. Học viên ghi dự đoán, thu evidence tối thiểu, xác nhận root cause rồi sửa đúng một chỗ.
3. Chạy lại phép thử ban đầu và control; hoàn nguyên tài nguyên/fault trước case B.
4. Lặp với case B có nguyên nhân khác. Viết hai [incident reports](../templates/INCIDENT_REPORT.md), có thể chia nhiều phiên nếu quá thời gian.

## Break & Fix

Không restart tất cả Pods/Service/CNI để tìm may mắn. Khi bí, dùng Hint 1/2/3 theo [AGENTS.md](../AGENTS.md); sau xem lời giải cần tự giải một biến thể tương đương trước khi nhận đạt.

## Bằng chứng và điều kiện đạt

- [ ] Có hai reports riêng, root cause khác nhau, mỗi report có giả thuyết và bằng chứng bác bỏ lựa chọn sai.
- [ ] Có post-fix/control checks và rollback/dọn tài nguyên giữa case.
- [ ] Ghi link reports trong [notes](../templates/LESSON_NOTE.md); chưa đánh dấu CP5 cho tới khi hoàn thành case DNS ở 20b.

**Tiếp theo:** [20b — DNS incident và CP5](20b-dns-incident-va-cp5.md).
