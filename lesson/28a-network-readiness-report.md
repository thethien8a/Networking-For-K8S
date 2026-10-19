# 28a — Thiết kế Network Readiness cho 4 VM

**Buổi gốc:** 28 · **Thời lượng:** 45–60 phút cho bản thảo, có thể tiếp tục nhiều phiên để đủ thông tin.  
**Trước bài:** [27b](27b-spark-network-incident.md).  
**Môi trường:** tài liệu/bằng chứng lab; VM công ty chỉ quan sát read-only trong phạm vi được phê duyệt, không yêu cầu triển khai cluster thật.

## Mục tiêu

- Tổng hợp phương án mạng cho 1 control-plane + 3 worker Ubuntu VM trên VMware.
- Biến kiến thức packet path thành checklist khả thi trong quyền quản trị guest.

## Mô hình cần hiểu

Readiness report là bản mô tả điều kiện cần và bằng chứng đạt/chưa đạt, không phải giấy xác nhận mọi cấu hình “sẽ chạy”. Node, Pod, Service và external address spaces cần kế hoạch không overlap đã được xác minh hoặc có owner xác minh. Quyền trong guest dừng trước lớp vSwitch/VLAN/vSphere.

## Dự đoán trước khi khảo sát

1. Điều kiện nào kiểm tra được từ Ubuntu guest, điều kiện nào cần nhóm VMware/network xác nhận?
2. Cùng subnet trên giấy có đủ chứng minh worker↔worker qua hạ tầng thật không?

## Challenge

1. Tạo bản thảo theo [NETWORK_READINESS_REPORT](../templates/NETWORK_READINESS_REPORT.md). Điền topology 4 VM và gắn observed/planned/unknown/simulated cho từng thông tin.
2. Hoàn thành IP plan, gateway/routes, DNS/NTP và connectivity matrix với nguồn/đích/protocol/port/mục đích/owner. Ports phải lấy theo distro/Kubernetes/CNI/Spark versions được chọn, không sao chép danh sách mở rộng vô điều kiện.
3. Vẽ các data paths same/cross-node, Service, egress và external access; nối với bài lab đã kiểm chứng, nêu giới hạn khi chuyển topology.
4. Viết runbook read-only, kiểm tra overlap LAN/VPN/Pod/Service, ranh giới trách nhiệm và danh sách unknowns cần phối hợp. Chỉ đo hệ thống công ty khi đã được phép; chưa được phép thì giữ unknown và đề xuất phép thử.

## Break & Fix

Nhận câu hỏi “worker không tới worker” hoặc “NodePort ngoài mạng không tới”. Đề xuất phép thử phân biệt guest route/firewall với VMware/network path, kèm quyền cần có và điều kiện escalate. Không tự sửa network công ty để hoàn thiện báo cáo.

## Bằng chứng và điều kiện đạt

- [ ] Có bản thảo đủ các phần theo mẫu, traceability tới notes/lab/docs và facts/assumptions tách rõ.
- [ ] Có matrix/address-overlap checks hoặc unknowns với owner/phép thử cụ thể, không điền giả IP/port thành facts.
- [ ] Có runbook theo đúng nơi chạy lệnh/quyền, case Spark được liên kết và [notes](../templates/LESSON_NOTE.md) cho phần cần bảo vệ ở 28b.

**Tiếp theo:** [28b — Bảo vệ capstone và Final](28b-capstone-defense-final.md).
