# 24b — Egress, DNS allowance và Checkpoint 6

**Buổi gốc:** 24 · **Thời lượng:** 45–60 phút.  
**Trước bài:** [24a](24a-networkpolicy-ingress.md).  
**Môi trường:** cluster policy-capable đã có proof từ 24a; DNS path thực tế đã ghi ở bài 19.

## Mục tiêu

- Sửa DNS egress bị chặn mà không mở toàn bộ traffic.
- Hoàn thành [CP6](../CHECKPOINTS.md#cp6--sau-24b) cho Ingress, LoadBalancer và NetworkPolicy.

## Mô hình cần hiểu

Ứng dụng gọi backend bằng hostname cần thêm flow DNS tới resolver thật. UDP và TCP DNS đều cần được xét theo resolver/topology, kể cả node-local DNS nếu đang dùng; không mặc định resolver luôn là Pod CoreDNS với một label cố định. Cho DNS qua chưa đồng nghĩa app port/backend egress cũng đã được phép.

## Dự đoán trước khi chạy

1. Khi chỉ thêm egress isolation cho client lab, gọi backend bằng IP và hostname có thể khác nhau ra sao?
2. Cho phép DNS rồi, client không hợp lệ có nên tự được vào backend không?

## Challenge

1. Lưu policy/DNS baseline, xác định chính xác Pods bị cô lập egress và resolver destination/protocols.
2. Trong lab riêng, tạo policy quá chặt ở client để tái hiện DNS lỗi; kiểm tra query, direct IP control và app flow bằng kết nối mới.
3. Bổ sung allowance tối thiểu cho DNS theo path thật và cho app flow cần thiết; kiểm tra cả UDP/TCP DNS khi topology/công cụ hỗ trợ, ghi phần chưa kiểm chứng.
4. Chạy lại matrix allowed/denied từ 24a với hostname, IP, đúng/sai source/port; tổng hợp chứng cứ Ingress bài 22, LoadBalancer bài 23 và policy để đánh giá CP6.

## Break & Fix

Không dùng allow-all egress hoặc hosts file làm fix DNS cuối. Kiểm tra scope/selectors/peer logic nếu policy mở rộng hơn dự định; không suy rằng thêm một policy hẹp sẽ thu hẹp policy rộng còn đang áp dụng. Hoàn nguyên faults và kiểm tra các controls vốn chạy.

## Bằng chứng và điều kiện đạt

- [ ] Có trước/sau DNS egress và app traffic, explanation theo resolver thật, policy thực thi được.
- [ ] Có allow/deny matrix và đủ tiêu chí CP6; LoadBalancer mô phỏng phải giữ nhãn ngoại lệ, không biến thành reachability proof.
- [ ] Có rollback/dọn tài nguyên, [notes](../templates/LESSON_NOTE.md) và kết quả CP6 ở [PROGRESS.md](../PROGRESS.md).

**Tiếp theo:** [25a — Pod egress và SNAT](25a-pod-egress-snat.md), chỉ khi CP6 đã đạt.
