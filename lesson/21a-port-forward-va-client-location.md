# 21a — Port-forward và vị trí thực của client

**Buổi gốc:** 21 · **Thời lượng:** 30–45 phút.  
**Trước bài:** [20b](20b-dns-incident-va-cp5.md) và CP5 đạt.  
**Môi trường:** ứng dụng/Service lab đang chạy, máy có kubectl được phép port-forward; mặc định giữ listener local.

## Mục tiêu

- Dùng port-forward để debug, biết port được mở trên máy nào.
- Không lấy thành công của tunnel debug làm bằng chứng NodePort hoặc Service data plane đã hoạt động.

## Mô hình cần hiểu

`kubectl port-forward` tạo đường chuyển tiếp phục vụ debug qua kubectl và API/kubelet tới Pod, không phải cơ chế expose bền vững cho ứng dụng. Ngay cả khi chọn Service làm đối tượng, phép thử không tương đương gửi traffic qua ClusterIP data plane. `localhost` là nơi kubectl đang listen, không tự xuất hiện trên mọi máy.

## Dự đoán trước khi chạy

1. Kubectl chạy trên Mac: client trên Linux VM gọi localhost cùng port có đi qua tunnel đó không?
2. Port-forward thành công có loại trừ selector/targetPort/path issues ở mọi cách gọi Service không?

## Challenge

1. Ghi context, namespace, target Pod/Service, local port và backend port; kiểm tra port local còn trống.
2. Mở port-forward chỉ cho phạm vi local được phép; xác nhận bind address và gọi ứng dụng từ máy chạy kubectl.
3. Lập matrix cho client Mac, Linux VM và Pod khác; chỉ test các nguồn thực có và được phép, ghi unavailable thay vì suy ra pass/fail.
4. So sánh với request trực tiếp qua Service từ Pod trong cluster, nêu các đoạn path không giống nhau.

## Break & Fix

Tạo case nhầm máy chạy client hoặc nhầm local/remote port. Quan sát listener và target thay vì expose `0.0.0.0` để chữa nhầm localhost. Dừng đúng tiến trình port-forward và xác nhận local listener đã hết khi kết thúc.

## Bằng chứng và điều kiện đạt

- [ ] Có request qua tunnel, mapping local/backend port và vị trí listener được xác minh.
- [ ] Có matrix vị trí client với nhãn phần chưa đo và so sánh Service path.
- [ ] Có output sau dừng tunnel và [notes](../templates/LESSON_NOTE.md).

**Tiếp theo:** [21b — NodePort, hostPort và kind mapping](21b-nodeport-hostport-kind-mapping.md).
