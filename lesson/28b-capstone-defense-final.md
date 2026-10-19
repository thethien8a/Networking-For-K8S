# 28b — Bảo vệ capstone và đánh giá Final

**Buổi gốc:** 28 · **Thời lượng:** 45–60 phút mỗi lượt bảo vệ/hoàn thiện.  
**Trước bài:** [28a](28a-network-readiness-report.md), các checkpoint trước đã đạt và case Spark thực hành đã có.  
**Môi trường:** report, notes và lab cá nhân để kiểm chứng bổ sung; không triển khai/thay đổi hạ tầng công ty.

## Mục tiêu

- Bảo vệ quyết định thiết kế và quy trình troubleshoot trước câu hỏi chưa biết trước.
- Hoàn thành [Final](../CHECKPOINTS.md#final--sau-28b), chỉ rõ những gì chưa xác minh ngoài quyền hiện có.

## Mô hình cần hiểu

Report tốt phải được người khác dùng lại: họ biết chạy ở đâu, mong đợi quan sát gì và khi nào chuyển nhóm xử lý. Không phải mọi unknown đều phải được giải quyết bằng quyền cao hơn; quan trọng là không che chúng dưới một kết luận chắc chắn. Kết quả capstone không cấp quyền thay đổi mạng hoặc phê duyệt triển khai.

## Dự đoán trước khi review

1. Nếu người khác chỉ có report mà không có lịch sử chat, họ có tìm được nguồn của từng port/CIDR/CNI behavior không?
2. Khi bị hỏi một packet path chưa từng capture, bạn sẽ nêu giả định và đề xuất phép kiểm chứng thế nào?

## Challenge

1. Người dạy review đủ tiêu chí Final và chọn câu hỏi biến thể: worker↔worker lỗi, DNS nội bộ lỗi, Service/backend lệch, external access lỗi hoặc executor không tới driver.
2. Học viên giải thích request/response path, giả thuyết cạnh tranh, phép đo read-only, ranh giới guest-vs-infrastructure và rollback nếu được phép sửa.
3. Review case Spark 27b bằng bằng chứng/version/config thật, phân biệt lỗi network với application/resource failure.
4. Sửa report ở phần thiếu bằng chứng/khó tái sử dụng; kiểm chứng thêm chỉ trên lab cá nhân hoặc theo phê duyệt riêng. Nêu kết luận readiness có điều kiện và unknowns còn tồn tại.

## Break & Fix

Nếu một nhận định quan trọng không có nguồn hoặc bằng chứng, hạ mức khẳng định về planned/unknown rồi bổ sung kế hoạch xác minh; không làm đẹp báo cáo bằng output tưởng tượng. Một thiết kế thay thế phải ghi trade-off và điều kiện áp dụng, không tuyên bố kind chứng minh VMware chắc chắn hoạt động.

## Bằng chứng và điều kiện đạt

- [ ] Có report hoàn chỉnh, nguồn/versions, connectivity matrix, runbook, trách nhiệm và risk register dùng lại được.
- [ ] Bảo vệ được các câu hỏi biến thể bằng lập luận có evidence, đủ case Spark và mọi tiêu chí Final.
- [ ] Lưu review/điểm còn yếu vào [notes](../templates/LESSON_NOTE.md), cập nhật Final trong [PROGRESS.md](../PROGRESS.md); mọi tài nguyên lab còn giữ có owner/kế hoạch dọn.

**Tiếp theo:** dùng [PROGRESS.md](../PROGRESS.md) chọn nội dung ôn và thử nghiệm tiếp theo. Chỉ ghi hoàn thành khóa khi 56 bài chính và các cổng đều đạt, không chỉ vì đã tới file cuối.
