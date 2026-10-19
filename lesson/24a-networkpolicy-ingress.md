# 24a — NetworkPolicy ingress: default-deny và allow-list

**Buổi gốc:** 24 · **Thời lượng:** 45–60 phút.  
**Trước bài:** [23b](23b-metallb-feasibility-lab.md).  
**Môi trường:** CNI **thực sự hỗ trợ policy**, namespace lab riêng, backend/frontend/client không hợp lệ có labels phân biệt.

## Mục tiêu

- Chỉ cho frontend hợp lệ vào backend qua TCP port được chọn.
- Chứng minh policy có hiệu lực bằng traffic trước/sau, không chỉ bằng API object.

## Mô hình cần hiểu

NetworkPolicy chọn Pods và kiểm soát các chiều ingress/egress được chỉ định. Các allow rules của policies áp dụng được cộng hợp, không có “deny rule ưu tiên cao hơn” để ghi đè một allow rộng khác. Nếu cả phía nguồn và đích bị cô lập ở chiều liên quan, cả hai phía phải cho phép flow.

## Dự đoán trước khi chạy

1. Policy tạo thành công nhưng CNI không enforce sẽ làm ma trận traffic khác mong đợi như thế nào?
2. Default-deny ingress có tự chặn mọi egress/DNS của Pod đó không?

## Challenge

1. Xác nhận CNI/version/support và policies sẵn có. Nếu chưa hỗ trợ, ghi blocker/kế hoạch cluster lab khác; không tự thay CNI hiện tại.
2. Đo baseline từ frontend và client không hợp lệ tới backend đúng/sai port; chỉ dùng port có listener/control rõ để phân biệt policy với app.
3. Áp default-deny ingress và allow-list frontend→backend cho đúng TCP port trong namespace lab; dự đoán từng ô trước khi test bằng kết nối mới, timeout hữu hạn.
4. Kiểm tra selectors và scope namespace; nếu dùng namespaceSelector/podSelector, phân biệt AND trong cùng peer với OR giữa các peers. Đối chiếu phép thử allowed/denied thực tế.

## Break & Fix

Nếu mọi client vẫn vào được, kiểm tra support, target selector, policy cộng hợp và connection reuse; không chỉ apply YAML lại. Nếu tất cả bị chặn, kiểm tra allow selector/port và egress phía nguồn. Không xóa policies không thuộc bài để làm kết quả khớp.

## Bằng chứng và điều kiện đạt

- [ ] Có ma trận baseline/default-deny/allow-list với ít nhất một allowed và một denied flow thực tế sau policy.
- [ ] Có labels/selectors/ports, giải thích isolation từng chiều và policy cộng hợp.
- [ ] Có manifest rollback, [notes](../templates/LESSON_NOTE.md) và tài nguyên giữ cho 24b; API accepted không thay cho traffic proof.

**Tiếp theo:** [24b — Egress DNS và CP6](24b-networkpolicy-dns-egress-va-cp6.md).
