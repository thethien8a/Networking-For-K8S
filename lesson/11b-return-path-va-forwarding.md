# 11b — Return path, forwarding và lỗi một chiều

**Buổi gốc:** 11 · **Thời lượng:** 30–45 phút.  
**Trước bài:** [11a](11a-router-namespace-hai-subnet.md).  
**Môi trường:** topology A–R–B riêng đã có baseline, không đụng routes hoặc sysctl ngoài namespace lab.

## Mục tiêu

- Phân biệt thiếu route chiều về với forwarding bị tắt.
- Hiểu routing bất đối xứng ở mức nhập môn mà không coi mọi đường khác nhau là lỗi.

## Mô hình cần hiểu

Traffic đi được một chiều chưa đủ cho TCP/HTTP. Route được chọn độc lập ở từng hop và từng chiều; đường đi/đường về khác nhau có thể hợp lệ, nhưng có thể tương tác với filtering/connection state. Cần capture để biết packet đã tới đâu, không suy từ một phía im lặng.

## Dự đoán trước khi chạy

1. Tắt forwarding trong R khác gì với thiếu route về ở B về vị trí thấy SYN/SYN-ACK?
2. Ping tới R vẫn được có loại trừ lỗi forwarding qua R không?

## Challenge

1. Lưu baseline routes, forwarding và capture của luồng A→B.
2. Người dạy tạo lần lượt hai lỗi có hoàn nguyên giữa các lượt: route chiều về và forwarding trong R; chưa chỉ rõ lỗi ở lượt nào.
3. Với mỗi lượt, dùng route lookup tại đúng namespace và capture hai phía router để xác định chỗ packet dừng.
4. Sửa một biến, chạy lại luồng lỗi và control A→R; hoàn nguyên fault trước lượt tiếp theo.

## Break & Fix

Không thêm default route rộng hoặc NAT để làm request “tự chạy” nếu root cause là một route cụ thể. Không kết luận thiếu route về chỉ vì SYN retransmit; kiểm tra packet ở phía server và quyết định route trả về của nó.

## Bằng chứng và điều kiện đạt

- [ ] Có hai case với routes/capture và kết luận khác nhau được kiểm chứng.
- [ ] Vẽ request/response, chỉ ra tầng chọn route và nơi cần forwarding.
- [ ] Có post-fix/control tests, hoàn nguyên và [notes](../templates/LESSON_NOTE.md).

**Tiếp theo:** [12a — Firewall và conntrack](12a-firewall-va-conntrack.md).
