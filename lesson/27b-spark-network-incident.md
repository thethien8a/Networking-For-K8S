# 27b — Executor khác node không kết nối được driver

**Buổi gốc:** 27 · **Thời lượng:** 45–60 phút, được chia tiếp nếu job/lab cần thời gian.  
**Trước bài:** [27a](27a-spark-driver-executor-baseline.md) với baseline job thành công.  
**Môi trường:** Spark trên cluster lab riêng, fault chỉ ảnh hưởng ứng dụng thử và có rollback.

## Mục tiêu

- Phân biệt driver hostname/Service/port/path failure với application/resource error.
- Chứng minh fix bằng cả kết nối executor→driver và kết quả job, không chỉ Pod Running.

## Mô hình cần hiểu

Thông báo executor không đăng ký được có thể liên quan discovery, listener, route hoặc policy, nhưng cũng cần xem executor đã chạy được chưa. Logs ứng dụng phải được ghép với Pod status, node/IP/DNS và connection evidence đúng thời điểm. Tăng retry hay timeout không thay cho endpoint reachable và cấu hình quảng bá đúng.

## Dự đoán trước khi chạy

1. Lỗi DNS tên driver và driver listen sai port sẽ khác nhau ở phép đo nào từ executor context?
2. Một executor bị OOM có thể tạo triệu chứng gì dễ nhầm với mất kết nối?

## Challenge

1. Người dạy chọn một lỗi lab theo deploy mode thật, ví dụ discovery/Service driver hoặc chặn flow thử có scope rõ; chỉ giao triệu chứng, chưa nêu nguyên nhân.
2. Xác nhận driver/executor thực nằm khác node; đọc Pod state/events/resources để không bỏ qua lỗi ngoài networking.
3. Thu DNS/Service/listener/route/traffic và driver/executor logs phù hợp quyền. Đề xuất phép thử phân biệt trước khi chỉnh Spark config.
4. Sửa tối thiểu theo tài liệu đúng version, chạy lại cùng job và một control; viết [incident report](../templates/INCIDENT_REPORT.md) để đưa vào Final.

## Break & Fix

Không chuyển tất cả Pods cùng node, dùng hostNetwork hoặc mở mọi port làm fix cuối. Nếu fault chỉ làm job fail vì resource/image thay vì network path, ghi đúng và chuẩn bị lại case network; không đổi nhãn nguyên nhân để khớp đề.

## Bằng chứng và điều kiện đạt

- [ ] Có incident driver/executor cross-node thực tế, root cause và bằng chứng loại trừ application/resource hypothesis.
- [ ] Có job result và kết nối đúng sau fix, negative/control checks phù hợp, config sources theo version.
- [ ] Có rollback/cleanup đã kiểm tra và report liên kết trong [notes](../templates/LESSON_NOTE.md); chưa chạy được Spark thật thì không qua phần thực hành này.

**Tiếp theo:** [28a — Network Readiness Report](28a-network-readiness-report.md).
