# 23b — Kiểm chứng MetalLB và external IP trong topology phù hợp

**Buổi gốc:** 23 · **Thời lượng:** 45–60 phút.  
**Trước bài:** [23a](23a-loadbalancer-bare-metal.md).  
**Môi trường:** lab cô lập có pool được phép và topology phù hợp; nếu không đáp ứng, dùng nhánh mô phỏng được gắn nhãn.

## Mục tiêu

- Kiểm tra external IP từ một vị trí client cụ thể, không chỉ nhìn status Service.
- Báo cáo tính khả thi và giới hạn khi chuyển từ kind/OrbStack sang VMware.

## Mô hình cần hiểu

Reachability là quan hệ từ nguồn cụ thể tới đích cụ thể. Một Pod/runtime client vào external IP được chưa chứng minh Mac hoặc client LAN cũng vào được. Khi lỗi, cần tách ARP/L2/routing ở bên ngoài với Service selection/forwarding/listener bên trong.

## Dự đoán trước khi chạy

1. Những client nào trong topology 23a được kỳ vọng tới external IP, và qua next hop nào?
2. Nếu backend Pod IP hoạt động nhưng không thấy ARP reply ở link phù hợp, nên điều tra tầng nào tiếp?

## Challenge

1. Chọn nhánh sau preflight: **thực nghiệm** nếu topology, quyền và pool đều phù hợp; **mô phỏng** nếu không, ghi lý do cụ thể.
2. Nhánh thực nghiệm: sau khi được đồng ý, cấu hình MetalLB theo version thật trong lab riêng, tạo Service thử, quan sát allocation/advertisement và test từ các client được hỗ trợ.
3. Nhánh mô phỏng: đọc topology/trace do người dạy cung cấp có ghi nguồn hoặc gắn nhãn dữ liệu tổng hợp; điền đường đi, điểm ARP/route cần kiểm tra và giới hạn chưa xác minh. Không bịa output như đã chạy.
4. Phân tích case “external IP có nhưng client không vào được”, có phép thử cho external path và backend path; viết feasibility report ngắn.

## Break & Fix

Không quảng bá IP lên LAN công ty hoặc sửa VMware để hoàn thành bài. Thay đổi controller/address pool chỉ ở lab đã đồng ý; ghi rollback và khôi phục tài nguyên bài sở hữu. Nếu chỉ một vị trí client test được, ghi đúng phạm vi đó.

## Bằng chứng và điều kiện đạt

- [ ] Có report thực nghiệm với client/IP/port/evidence, **hoặc** report mô phỏng có topology/trace và lý do môi trường không phù hợp theo ngoại lệ giáo trình gốc.
- [ ] Phân biệt được cấp IP, ARP/L2/route reachability và backend failure, nêu giới hạn chuyển sang 4 VM.
- [ ] Gắn nhãn mức kiểm chứng rõ, có cleanup cho nhánh thực nghiệm và [notes](../templates/LESSON_NOTE.md); không ghi mô phỏng là traffic thật đã pass.

**Tiếp theo:** [24a — NetworkPolicy ingress](24a-networkpolicy-ingress.md).
