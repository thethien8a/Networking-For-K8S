# 21b — NodePort, hostPort và giới hạn kind trên Mac

**Buổi gốc:** 21 · **Thời lượng:** 45–60 phút.  
**Trước bài:** [21a](21a-port-forward-va-client-location.md).  
**Môi trường:** cluster cá nhân có NodePort lab, biết runtime network/port mappings; không recreate cluster ngầm để thêm mapping.

## Mục tiêu

- So sánh expose bằng port-forward với NodePort; hiểu hostPort khác hostNetwork.
- Xác định IP/port khả dụng theo vị trí client, không mặc định Mac route tới mọi Node IP.

## Mô hình cần hiểu

NodePort expose Service qua port trên các node theo cấu hình triển khai; client vẫn cần đường tới node và traffic được phép. hostPort là khai báo mapping port của workload trên host/node, không đồng nghĩa toàn Pod dùng hostNetwork. Với kind, node là container trong runtime nên có thể cần mapping hoặc đường truy cập khác theo môi trường.

## Dự đoán trước khi chạy

1. NodePort hoạt động từ một client Linux có route tới node thì Mac có chắc cũng gọi được không?
2. Publish host port ở runtime có giống tạo NodePort trong Kubernetes không?

## Challenge

1. Expose cùng ứng dụng lab bằng NodePort, ghi Service port/targetPort/nodePort và node addresses thật.
2. Đọc runtime/kind configuration và port mappings đã có, không suy từ cấu hình mẫu trên mạng.
3. Điền matrix client Mac/Linux VM/Pod × port-forward/NodePort với IP/port thực dùng, expected/actual và nơi có thể bị chặn.
4. So sánh hostPort ở mức cấu hình/data path; chỉ chạy một workload hostPort nếu có nhu cầu và quyền, chọn port không xung đột rồi dọn ngay.

## Break & Fix

Nhận case “NodePort từ Mac không tới nhưng backend trong cluster vẫn chạy”. Kiểm tra route/runtime mapping/firewall/Service trước khi kết luận Kubernetes lỗi. Nếu topology không hỗ trợ Mac truy cập trực tiếp, ghi giới hạn và phương án lab phù hợp; mọi tạo cluster/mapping mới cần đồng ý riêng.

## Bằng chứng và điều kiện đạt

- [ ] Có hai cách expose được phân tích bằng matrix; chứng minh NodePort từ ít nhất một client được topology hỗ trợ và port-forward từ bài trước.
- [ ] Phân biệt NodePort, hostPort, hostNetwork và mapping runtime, không điền các vị trí chưa test là thành công.
- [ ] Có cleanup Service/mapping/workload riêng và [notes](../templates/LESSON_NOTE.md).

**Tiếp theo:** [22a — Ingress và Host/path routing](22a-ingress-controller-host-path.md).
