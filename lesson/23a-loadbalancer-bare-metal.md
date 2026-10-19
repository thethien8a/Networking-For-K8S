# 23a — LoadBalancer bare-metal: cấp IP khác với quảng bá IP

**Buổi gốc:** 23 · **Thời lượng:** 30–45 phút.  
**Trước bài:** [22b](22b-ingress-tls-termination.md).  
**Môi trường:** khảo sát topology lab và tài liệu implementation phù hợp, chưa cấp external IP hoặc đổi mạng.

## Mục tiêu

- Hiểu vì sao `type: LoadBalancer` cần implementation/tích hợp phù hợp ngoài việc tạo API object.
- Phân biệt cấp địa chỉ, quảng bá/reachability và Service/backend forwarding.

## Mô hình cần hiểu

Một external IP được cấp chưa có nghĩa client có đường tới IP đó. Với ví dụ MetalLB L2, cần hiểu client/next hop, ARP cho IPv4 và node quảng bá địa chỉ trong topology hỗ trợ; không coi đó là cơ chế mặc định cho mọi LoadBalancer. Kubernetes Service backend path vẫn cần đúng sau khi traffic tới node.

## Dự đoán trước khi chạy

1. Service có external IP nhưng client ở ngoài L2/route hỗ trợ thì request dừng ở đâu?
2. Cấp được IP có loại trừ selector/listener sai ở backend không?

## Challenge

1. Vẽ client → LAN/runtime network → nodes → Service/backends cho lab hiện tại, đánh dấu L2 boundaries và routes đã xác minh.
2. Xác định có LoadBalancer implementation nào đang chạy không; nếu không có, giải thích trạng thái Service có thể gặp mà không vội cài thêm.
3. Lập checklist feasibility MetalLB L2: nguồn IP pool được phép, tránh trùng địa chỉ, mạng quảng bá, vị trí client và quyền quan sát.
4. Viết hai giả thuyết cho “IP được cấp, app không vào được”: một ở external reachability, một ở backend; đề xuất phép thử phân biệt.

## Kiểm chứng và đối chứng

Không chọn pool từ mạng doanh nghiệp chỉ vì “chưa ping thấy ai dùng”. Quyền trong Ubuntu guest không cấp quyền thay VMware/network. Không dùng kết quả kind để cam kết L2 announcement sẽ đi qua vSwitch/VLAN chưa khảo sát.

## Bằng chứng và điều kiện đạt

- [ ] Có topology/checklist với thông tin observed/planned/unknown rõ ràng.
- [ ] Giải thích allocation, advertisement/routing và backend path là các điều kiện khác nhau.
- [ ] Có kết luận sơ bộ lab khả thi/chưa đủ điều kiện và phép thử cần cho 23b trong [notes](../templates/LESSON_NOTE.md).

**Tiếp theo:** [23b — MetalLB feasibility lab](23b-metallb-feasibility-lab.md).
