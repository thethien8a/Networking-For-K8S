# 08b — SNAT, DNAT, port mapping và Checkpoint 2

**Buổi gốc:** 08 · **Thời lượng:** 45–60 phút.  
**Trước bài:** [08a](08a-http-host-va-tls.md).  
**Môi trường:** port mapping/NAT sẵn có trong lab được phép quan sát; không thêm NAT rule lên host đang dùng.

## Mục tiêu

- Theo dõi IP/port trước và sau chuyển địa chỉ, phân biệt SNAT với DNAT.
- Hoàn thành [CP2](../CHECKPOINTS.md#cp2--sau-08b).

## Mô hình cần hiểu

SNAT thay nguồn, DNAT thay đích theo quy tắc tương ứng; đường về cần được xử lý phù hợp để cuộc trao đổi hoạt động. Port mapping là hành vi expose/chuyển tiếp có thể được triển khai bằng NAT hoặc proxy tùy môi trường. Không tự coi một process proxy là bằng chứng kernel đang DNAT, và NAT không đồng nghĩa với firewall.

## Dự đoán trước khi chạy

1. Client gọi một mapped port thì backend có nhất thiết nhìn thấy đúng source IP ban đầu không?
2. Cùng một flow nhìn trước/sau điểm dịch có thể khác những trường nào?

## Challenge

1. Vẽ topology của một port mapping lab đã xác minh: vị trí client, host/node, backend và điểm có thể quan sát.
2. Điền bảng source IP/port, destination IP/port ở mỗi phía, ghi rõ dự đoán trước khi xem output/log/capture.
3. Nếu môi trường cho phép, quan sát hai phía để xác định dịch địa chỉ thật. Nếu không, ghi giới hạn và phân tích một trace có ghi nguồn; không khẳng định đã capture NAT thực tế.
4. Làm CP2 bằng các case đã thực nghiệm: DNS, TCP, HTTP và TLS; mỗi kết luận cần bằng chứng và một giả thuyết đối lập được loại trừ.

## Break & Fix

So sánh mapped port sai với backend không listen và HTTP Host sai, chọn phép đo phân biệt thay vì sửa tất cả. Không thay rules do runtime quản lý để ép khớp sơ đồ lý thuyết. Dọn mapping/tài nguyên bài tạo, không đụng mapping đang phục vụ việc khác.

## Bằng chứng và điều kiện đạt

- [ ] Có bảng tuple và topology giải thích SNAT/DNAT/port mapping; phần chưa quan sát được gắn nhãn rõ.
- [ ] Có kết quả CP2 đủ bằng chứng từng lớp, không dùng `-k` để coi TLS đã đúng.
- [ ] Lưu [notes](../templates/LESSON_NOTE.md), ghi checkpoint vào [PROGRESS.md](../PROGRESS.md) và hoàn nguyên phần lab thay đổi.

**Tiếp theo:** [09a — Linux network inventory](09a-linux-network-inventory.md), chỉ khi CP2 đã đạt.
