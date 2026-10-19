# 27a — Spark driver/executor: dựng bản đồ kết nối thật

**Buổi gốc:** 27 · **Thời lượng:** 45–60 phút sau khi môi trường Spark sẵn sàng.  
**Trước bài:** [26b](26b-post-fix-regression.md).  
**Môi trường:** Kubernetes lab đủ tài nguyên, Spark/image tương thích architecture và version được xác minh; mọi cài đặt mới cần đồng ý trước.

## Mục tiêu

- Hiểu driver discovery, executor registration và các luồng control/data liên quan đến job thật.
- Chứng minh driver/executor placement, addresses, DNS và listeners thay vì chép config từ version khác.

## Mô hình cần hiểu

Driver và executors phải tìm tới đúng network endpoint mà phía kia được cấu hình/quảng bá. Deploy mode và phiên bản quyết định các chi tiết cấu hình; một hostname hợp lệ từ máy submit không nhất thiết hợp lệ từ executor Pod. Pod Pending, image pull, OOM hoặc lỗi ứng dụng không tự động là network failure.

## Dự đoán trước khi chạy

1. Executor cần resolve/connect tới những endpoint nào theo deploy mode đang chọn?
2. Driver và executor nằm khác node thì kiến thức data path nào từ bài 15 cần dùng lại?

## Challenge

1. Ghi Spark version, deploy mode, image, Kubernetes/CNI versions, nguồn tài liệu chính thức và giới hạn tài nguyên trong [LAB.md](../LAB.md)/notes.
2. Tra đúng docs release trước khi lấy config keys, advertised host, Service và ports; không tự điền số port hoặc tên property từ trí nhớ.
3. Chạy một job nhỏ, dữ liệu giả, không phụ thuộc dữ liệu công ty; ghi driver/executor Pods, node placement, IP/DNS/listeners và kết quả job.
4. Xác nhận có driver–executor cross-node traffic bằng placement/log/traffic evidence phù hợp; vẽ control và data connections thực sự liên quan, tách phần mới là mô hình.

## Kiểm chứng và đối chứng

Nếu chưa có Spark lab tương thích hoặc thiếu tài nguyên, ghi `bi-chan` và kế hoạch chuẩn bị riêng; đọc log mẫu không được coi là chạy job. Không đổi cluster/CNI chỉ để ép một config ví dụ hoạt động. Giữ một baseline job thành công trước khi tạo fault ở 27b.

## Bằng chứng và điều kiện đạt

- [ ] Có version/deploy-mode facts và nguồn chính thức cho config/ports thực dùng.
- [ ] Có job thành công thật, driver/executor placement và sơ đồ discovery/listener/control/data có bằng chứng tương ứng.
- [ ] Có [notes](../templates/LESSON_NOTE.md), manifest/config đã ẩn secrets và ownership tài nguyên giữ cho 27b.

**Tiếp theo:** [27b — Executor không tới driver](27b-spark-network-incident.md).
