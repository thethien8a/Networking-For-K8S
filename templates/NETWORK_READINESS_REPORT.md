# Network Readiness Report — 1 control-plane + 3 worker VM

> Mẫu báo cáo, chưa có khảo sát thực tế. Không điền IP/ports/CNI bằng suy đoán. Dùng `observed`, `planned`, `unknown`, `simulated` để phân biệt mức bằng chứng. Dữ liệu công ty phải được xử lý theo chính sách bảo mật.

- Ngày khảo sát / tác giả / phạm vi được phép: [...]
- Distro/Kubernetes/CNI/service proxy/phiên bản: [đã chọn hay chưa]
- Kết luận readiness: [sẵn sàng / có điều kiện / chưa sẵn sàng, kèm lý do]

## 1. Topology và inventory

| VM/vai trò | NIC/IP/prefix | Gateway | DNS/NTP | Trạng thái thông tin | Bằng chứng |
|---|---|---|---|---|---|
| control-plane | [...] | [...] | [...] | [...] | [...] |
| worker-1 | [...] | [...] | [...] | [...] | [...] |
| worker-2 | [...] | [...] | [...] | [...] | [...] |
| worker-3 | [...] | [...] | [...] | [...] | [...] |

[Sơ đồ guest OS và ranh giới VMware/network; phần dưới quyền quản trị của nhóm khác]

## 2. Connectivity matrix

| Nguồn/vị trí client | Đích | Protocol/port | Mục đích / nguồn yêu cầu | Phép thử được phép | Kết quả và bằng chứng | Người có quyền xử lý |
|---|---|---|---|---|---|---|
| [...] | [...] | [theo version thật] | [URL/config] | [...] | [observed/unknown] | [...] |

Bao phủ node↔node, các dependency cần thiết, Pod same/cross-node, Service, DNS, egress và external access. Phép thử mô phỏng không thay cho kết quả trên mạng công ty.

## 3. Kubernetes address plan và overlap

| Dải địa chỉ | CIDR / phạm vi | Planned hay observed | Kiểm tra overlap với LAN/VPN/mạng doanh nghiệp | Bằng chứng / owner xác nhận |
|---|---|---|---|---|
| Node network | [...] | [...] | [...] | [...] |
| Pod network | [...] | [...] | [...] | [...] |
| Service network | [...] | [...] | [...] | [...] |
| External IP pool nếu dùng | [...] | [...] | [...] | [...] |

## 4. Data paths hai chiều

[Mỗi luồng ghi source/destination, next hop, namespace boundary, routing/NAT/encapsulation thực tế, return path và cách quan sát]

- Pod↔Pod cùng node: [...]
- Pod↔Pod khác node: [...]
- Pod↔Service: [...]
- Pod→external và chiều trả về: [...]
- External→Ingress/LoadBalancer→Service→Pod: [...]
- MTU/PMTU và rủi ro đường truyền: [...]

## 5. DNS, thời gian và dependency

[Node resolver, Pod resolver, Service FQDN/cluster suffix, external DNS, upstream dependencies, NTP/time sync và bằng chứng hoặc kế hoạch kiểm tra]

## 6. Firewall, CNI và external access

[Lựa chọn distro/CNI/proxy/controller; yêu cầu kết nối theo tài liệu đúng phiên bản; NetworkPolicy support và traffic tests; điều kiện ARP/L2 hoặc routing nếu có LoadBalancer; không đề nghị mở mọi port]

## 7. Ranh giới trách nhiệm và yêu cầu phối hợp

| Vấn đề | Bằng chứng trong guest | Điều chưa quan sát được | Nhóm có quyền | Yêu cầu cụ thể / điều kiện chấp nhận |
|---|---|---|---|---|
| [...] | [...] | [...] | [guest/VMware/network] | [...] |

## 8. Runbook quan sát và rollback

| Tình huống | Lệnh read-only và nơi chạy | Quan sát mong đợi | Điều kiện escalate | Nếu được phép sửa: phạm vi/rollback |
|---|---|---|---|---|
| [...] | [...] | [...] | [...] | [...] |

## 9. Case Spark driver/executor

[Link incident bài 27; version/deploy mode, driver discovery/listeners, executor placement, bằng chứng network vs application/resource errors, fix và regression check]

## 10. Rủi ro, unknowns và kết luận

| Rủi ro / điều chưa kiểm chứng | Ảnh hưởng | Vì sao chưa kiểm chứng | Phép thử / phê duyệt cần có | Owner / bước tiếp |
|---|---|---|---|---|
| [...] | [...] | [...] | [...] | [...] |

[Những điều đã sẵn sàng, điều kiện còn thiếu và đề xuất tiếp theo. Báo cáo không phải phê duyệt triển khai hoặc quyền thay đổi mạng công ty.]
