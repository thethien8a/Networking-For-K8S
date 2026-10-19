# 20b — Mini-incident DNS và Checkpoint 5

**Buổi gốc:** 20 · **Thời lượng:** 45–60 phút.  
**Trước bài:** [20a](20a-service-mini-incidents.md).  
**Môi trường:** case DNS lab riêng, baseline Service/backend hoạt động và fault không ảnh hưởng DNS dùng chung.

## Mục tiêu

- Tự điều tra DNS failure không biết trước cấu hình bị đổi.
- Hoàn thành bộ ba reports và [CP5](../CHECKPOINTS.md#cp5--sau-20b).

## Mô hình cần hiểu

Một incident tốt kể lại cách bằng chứng thay đổi giả thuyết, không chỉ nêu cuối cùng trường nào sai. “Dùng IP thay tên thì được” có giá trị khi cùng service/port và Host/TLS được kiểm soát. Sau fix DNS phải kiểm tra cả resolution và request ứng dụng.

## Dự đoán trước khi chạy

1. Từ triệu chứng được giao, bạn chọn query nào làm control cho resolver và query nào kiểm tra tên mục tiêu?
2. Output nào sẽ khiến bạn quay lại kiểm tra Service/backend thay vì tiếp tục sửa DNS?

## Challenge

1. Nhận triệu chứng và phạm vi case DNS, chưa nhận nguyên nhân. Ghi request path dự đoán và hai giả thuyết cạnh tranh.
2. Thu resolver config, query output, DNS Service/endpoints/logs khi liên quan và direct IP control đúng phạm vi.
3. Áp sửa tối thiểu sau khi xác nhận nguyên nhân; kiểm tra query đúng/sai theo dự kiến và ứng dụng qua hostname.
4. Viết report thứ ba; review cả ba reports của buổi 20 theo CP5, chỉ bổ sung tiêu chí còn thiếu thay vì viết lại lịch sử điều tra.

## Break & Fix

Không đổi DNS toàn host/cluster hoặc thêm hosts entry để che root cause. Hoàn nguyên fault và kiểm tra luồng Service/DNS vốn chạy. Nếu case chỉ được phân tích trên output mẫu, ghi mô phỏng và để phần thực hành chưa đạt.

## Bằng chứng và điều kiện đạt

- [ ] Có report DNS với root cause, proof trước/sau và kiểm tra request qua hostname.
- [ ] Đủ ba incident reports độc lập, bao phủ đúng selector, targetPort/listener và DNS, đạt mọi tiêu chí CP5.
- [ ] Lưu links trong [notes](../templates/LESSON_NOTE.md), cleanup và cập nhật CP5 ở [PROGRESS.md](../PROGRESS.md).

**Tiếp theo:** [21a — Port-forward và vị trí client](21a-port-forward-va-client-location.md), chỉ khi CP5 đã đạt.
