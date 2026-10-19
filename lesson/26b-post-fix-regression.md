# 26b — Fix tối thiểu, rollback và regression checks

**Buổi gốc:** 26 · **Thời lượng:** 45–60 phút mỗi phiên, chia thêm nếu chưa đủ 3–4 case.  
**Trước bài:** [26a](26a-incident-triage.md).  
**Môi trường:** giữ đúng catalogue và tài nguyên lab của 26a; kiểm tra điểm bàn giao trước khi tiếp tục.

## Mục tiêu

- Hoàn tất 3–4 incidents với proof sau fix và kiểm tra luồng vốn chạy.
- Biết dừng/escalate khi phép sửa vượt quyền, thay vì thử config ngẫu nhiên.

## Mô hình cần hiểu

Fix một luồng nhưng mở rộng policy quá mức hoặc làm hỏng luồng khác chưa phải kết quả đúng. Regression check phải giữ điều kiện client/placement/protocol đủ tương đương baseline. Rollback là cách quay về trạng thái đã biết, không phải xóa sạch tài nguyên và mất luôn bằng chứng.

## Dự đoán trước khi chạy

1. Fix dự kiến có thể ảnh hưởng luồng nào ngoài luồng lỗi, và control nào bắt được tác dụng phụ đó?
2. Tín hiệu nào khiến bạn rollback thay vì tiếp tục thêm thay đổi?

## Challenge

1. Đọc các reports/điểm dừng 26a, tiếp tục triage các case chưa có root cause trước khi áp fix.
2. Với mỗi case, ghi diff/config tối thiểu, phạm vi, rollback cụ thể và expected post-fix observation.
3. Chạy lại phép thử từng lỗi, một control vốn hoạt động và một negative control khi yêu cầu bảo mật có liên quan.
4. Hoàn tất đủ 3–4 reports gồm timeline, triệu chứng, root cause, fix, rollback và post-fix checks. Rút ra một playbook ngắn từ bằng chứng, không biến thành công thức chữa mọi cluster.

## Break & Fix

Nếu test sau fix sai dự đoán, quay lại giả thuyết và phân tích trước khi đổi chiến thuật. Không tắt policy/firewall toàn cục để xanh matrix. Dọn faults/resources được sở hữu, xác minh trạng thái cuối và ghi limits/escalation nếu case cần quyền ngoài lab.

## Bằng chứng và điều kiện đạt

- [ ] Đủ 3–4 incidents độc lập, root cause có evidence và post-fix/control checks, không chỉ case thuận lợi.
- [ ] Mỗi case có rollback/dọn dẹp xác minh và explanation vì sao fix không mở rộng phạm vi ngoài ý định.
- [ ] Có playbook và links reports trong [notes](../templates/LESSON_NOTE.md); nếu chưa đủ case/bằng chứng thì tiếp tục bài này, không chuyển `dat` vì hết giờ.

**Tiếp theo:** [27a — Spark networking baseline](27a-spark-driver-executor-baseline.md).
