# 15b — Bằng chứng và fault injection cho cross-node path

**Buổi gốc:** 15 · **Thời lượng:** 45–60 phút.  
**Trước bài:** [15a](15a-underlay-overlay-cross-node.md).  
**Môi trường:** cluster lab disposable/cô lập, quyền quan sát và một fault plan được đồng ý; không làm trên cluster chia sẻ.

## Mục tiêu

- Chọn điểm đo để xác định packet dừng ở tầng nào giữa hai Pod khác node.
- Tạo lỗi có phạm vi và rollback, không phá toàn bộ mạng cluster để làm bài.

## Mô hình cần hiểu

Một failure cross-node có thể nằm ở ứng dụng, Pod egress, node forwarding, underlay hoặc phía đích. Chứng cứ phải liên kết cùng flow và thời gian ở các điểm đo. Restart CNI có thể làm triệu chứng biến mất mà không chứng minh nguyên nhân, đồng thời ảnh hưởng các luồng không thuộc bài.

## Dự đoán trước khi chạy

1. Nếu packet rời Pod nhưng chưa tới destination node, phép đo tiếp theo nên đặt ở đâu?
2. Một fault trên test flow có thể để same-node/control traffic vẫn chạy không, và kiểm tra điều đó ra sao?

## Challenge

1. Lập bảng điểm đo gồm Pod source, node source, node network, node destination và Pod đích; ghi quyền/công cụ khả dụng.
2. Ghi baseline cross-node và control same-node, listener và routes liên quan.
3. Người dạy chọn fault giới hạn theo data plane thật, có bản cấu hình trước thay đổi và rollback cụ thể. Nếu không thể giới hạn tác động hoặc thiếu console, không injection; ghi blocker để dựng lab phù hợp.
4. Sau khi có fault an toàn, học viên chọn phép đo theo từng giả thuyết, xác định đoạn hỏng và sửa/hoàn nguyên đúng một biến.

## Break & Fix

Không tắt toàn node, flush firewall hoặc đổi CNI để tạo lỗi. Nếu không đủ quyền capture, dùng thêm logs/counters/endpoint evidence nhưng không giả tạo quan sát packet. Phải chạy lại control và luồng lỗi sau hoàn nguyên, không chỉ thấy Pod Running.

## Bằng chứng và điều kiện đạt

- [ ] Có fault plan, phạm vi, baseline và evidence khoanh vùng một lỗi cross-node thực tế.
- [ ] Có root cause theo triển khai thật, trước/sau và control tests.
- [ ] Có rollback được xác minh và [notes](../templates/LESSON_NOTE.md); lab không đủ an toàn thì ghi `bi-chan`, không đánh dấu đã thực hành.

**Tiếp theo:** [16a — Events, logs và CNI triage](16a-cni-events-logs-triage.md).
