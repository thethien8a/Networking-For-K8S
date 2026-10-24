# Theo dõi tiến độ Networking for Kubernetes

> Đây là nguồn trạng thái học duy nhất. Khởi tạo trống: việc tạo giáo trình không phải một phiên học và không chứng minh bài nào đã đạt. Quy tắc cập nhật nằm trong [AGENTS.md](AGENTS.md).

## Điểm nối tiếp cho phiên sau

- **Bài chính đã đạt:** 2/56, không tính bài 00.
- **Checkpoint đã đạt:** 0/7, gồm CP1–CP6 và Final.
- **Bài đang học:** [02a — IPv4 và CIDR](lesson/02a-ipv4-va-cidr.md).
- **Bước đang dừng:** Hoàn thành bài 01b; chuẩn bị vào bài 02a.
- **Bước tiếp theo:** [02a — IPv4 và CIDR](lesson/02a-ipv4-va-cidr.md), tính network ID, broadcast, số host khả dụng và prefix length.
- **Bằng chứng/notes gần nhất:** [notes/01b.md](notes/01b.md).
- **Blocker đã xác nhận:** Không có blocker cho 02a.
- **Thay đổi lab còn tồn tại / nợ rollback:** Không còn (đã dừng HTTP server).
- **Ngày học gần nhất / cập nhật từ phiên học (Asia/Ho_Chi_Minh):** 2026-10-24

Tổng số là bản tóm tắt từ các hàng `dat` bên dưới; cập nhật cùng lúc khi đổi trạng thái, không dùng số file notes hoặc checkbox trong bài làm phép đếm.

## Trạng thái dùng thống nhất

| Giá trị | Ý nghĩa |
|---|---|
| `chua-hoc` | Chưa bắt đầu học bài/cổng |
| `dang-hoc` | Đang làm hoặc còn thiếu bước/bằng chứng |
| `bi-chan` | Không thể tiếp tục do thiếu môi trường/quyền/điều kiện; cần ghi cách gỡ |
| `can-on` | Đã làm nhưng còn hiểu sai hoặc cần làm lại phần cụ thể |
| `dat` | Đủ tiêu chí, có bằng chứng và giải thích đúng; đã xử lý cleanup/bàn giao |

Ngày chỉ ghi ngày **thực học/đánh giá**, định dạng `YYYY-MM-DD`. Cột bằng chứng điền link notes kèm phần còn thiếu nếu chưa đạt, không tạo link tới file chưa tồn tại. Bài đã xem lý thuyết nhưng chưa làm lab không tự chuyển `dat`.

## Bài khởi động — ngoài bộ đếm 56

| ID | Bài | Trạng thái | Ngày gần nhất | Bằng chứng / điểm còn thiếu |
|---|---|---|---|---|
| 00 | [Khởi động và preflight](lesson/00-khoi-dong.md) | dat | 2026-10-24 | [notes/00.md](notes/00.md) |

## 56 bài chính

| ID | Bài | Trạng thái | Ngày gần nhất | Bằng chứng / điểm còn thiếu |
|---|---|---|---|---|
| 01a | [HTTP request và endpoint](lesson/01a-http-request-va-endpoint.md) | dat | 2026-10-24 | [notes/01a.md](notes/01a.md) |
| 01b | [Loopback và listen address](lesson/01b-loopback-va-listen-address.md) | dat | 2026-10-24 | [notes/01b.md](notes/01b.md) |
| 02a | [IPv4 và CIDR](lesson/02a-ipv4-va-cidr.md) | chua-hoc | — | — |
| 02b | [IP plan 4 VM và prefix](lesson/02b-ip-plan-va-prefix.md) | chua-hoc | — | — |
| 03a | [Ethernet, MAC, ARP](lesson/03a-ethernet-mac-arp.md) | chua-hoc | — | — |
| 03b | [Neighbor cache và gateway](lesson/03b-neighbor-cache-va-gateway.md) | chua-hoc | — | — |
| 04a | [Routing table](lesson/04a-routing-table.md) | chua-hoc | — | — |
| 04b | [Lỗi routing và CP1](lesson/04b-route-fault-va-cp1.md) | chua-hoc | — | — |
| 05a | [Port, socket, TCP/UDP](lesson/05a-port-socket-tcp-udp.md) | chua-hoc | — | — |
| 05b | [TCP handshake](lesson/05b-tcp-handshake.md) | chua-hoc | — | — |
| 06a | [Refused, timeout, reset](lesson/06a-refused-timeout-reset.md) | chua-hoc | — | — |
| 06b | [Timeout, retry và điều tra](lesson/06b-timeout-retry-va-dieu-tra.md) | chua-hoc | — | — |
| 07a | [Resolver và DNS records](lesson/07a-resolver-va-dns-records.md) | chua-hoc | — | — |
| 07b | [IP được, hostname lỗi](lesson/07b-ip-duoc-hostname-loi.md) | chua-hoc | — | — |
| 08a | [HTTP Host và TLS](lesson/08a-http-host-va-tls.md) | chua-hoc | — | — |
| 08b | [NAT, port mapping và CP2](lesson/08b-nat-port-mapping-va-cp2.md) | chua-hoc | — | — |
| 09a | [Linux network inventory](lesson/09a-linux-network-inventory.md) | chua-hoc | — | — |
| 09b | [Sockets và connectivity matrix](lesson/09b-sockets-va-connectivity-matrix.md) | chua-hoc | — | — |
| 10a | [Network namespace và veth](lesson/10a-network-namespace-va-veth.md) | chua-hoc | — | — |
| 10b | [Linux bridge](lesson/10b-linux-bridge.md) | chua-hoc | — | — |
| 11a | [Router namespace hai subnet](lesson/11a-router-namespace-hai-subnet.md) | chua-hoc | — | — |
| 11b | [Return path và forwarding](lesson/11b-return-path-va-forwarding.md) | chua-hoc | — | — |
| 12a | [Firewall và conntrack](lesson/12a-firewall-va-conntrack.md) | chua-hoc | — | — |
| 12b | [DROP/REJECT và CP3](lesson/12b-drop-reject-va-cp3.md) | chua-hoc | — | — |
| 13a | [Pod shared network namespace](lesson/13a-pod-shared-network-namespace.md) | chua-hoc | — | — |
| 13b | [Bind address và hostNetwork](lesson/13b-bind-address-va-hostnetwork.md) | chua-hoc | — | — |
| 14a | [CNI và same-node placement](lesson/14a-cni-va-same-node-placement.md) | chua-hoc | — | — |
| 14b | [Same-node data path](lesson/14b-same-node-data-path.md) | chua-hoc | — | — |
| 15a | [Underlay, overlay, cross-node](lesson/15a-underlay-overlay-cross-node.md) | chua-hoc | — | — |
| 15b | [Cross-node evidence và fault](lesson/15b-cross-node-evidence-va-fault.md) | chua-hoc | — | — |
| 16a | [CNI events/logs triage](lesson/16a-cni-events-logs-triage.md) | chua-hoc | — | — |
| 16b | [Cross-node incident và CP4](lesson/16b-cross-node-incident-va-cp4.md) | chua-hoc | — | — |
| 17a | [Service, selector, EndpointSlice](lesson/17a-service-selector-endpointslice.md) | chua-hoc | — | — |
| 17b | [Selector mismatch](lesson/17b-selector-mismatch.md) | chua-hoc | — | — |
| 18a | [Service proxy/data plane](lesson/18a-service-proxy-data-plane.md) | chua-hoc | — | — |
| 18b | [port, targetPort, listener](lesson/18b-port-targetport-listener.md) | chua-hoc | — | — |
| 19a | [CoreDNS, namespace, FQDN](lesson/19a-coredns-service-names.md) | chua-hoc | — | — |
| 19b | [DNS resolution path](lesson/19b-dns-resolution-path.md) | chua-hoc | — | — |
| 20a | [Hai Service mini-incidents](lesson/20a-service-mini-incidents.md) | chua-hoc | — | — |
| 20b | [DNS incident và CP5](lesson/20b-dns-incident-va-cp5.md) | chua-hoc | — | — |
| 21a | [Port-forward và client location](lesson/21a-port-forward-va-client-location.md) | chua-hoc | — | — |
| 21b | [NodePort, hostPort, kind mapping](lesson/21b-nodeport-hostport-kind-mapping.md) | chua-hoc | — | — |
| 22a | [Ingress Controller, Host/path](lesson/22a-ingress-controller-host-path.md) | chua-hoc | — | — |
| 22b | [TLS termination](lesson/22b-ingress-tls-termination.md) | chua-hoc | — | — |
| 23a | [LoadBalancer bare-metal](lesson/23a-loadbalancer-bare-metal.md) | chua-hoc | — | — |
| 23b | [MetalLB feasibility lab](lesson/23b-metallb-feasibility-lab.md) | chua-hoc | — | — |
| 24a | [NetworkPolicy ingress](lesson/24a-networkpolicy-ingress.md) | chua-hoc | — | — |
| 24b | [DNS egress allowance và CP6](lesson/24b-networkpolicy-dns-egress-va-cp6.md) | chua-hoc | — | — |
| 25a | [Pod egress và SNAT](lesson/25a-pod-egress-snat.md) | chua-hoc | — | — |
| 25b | [MTU và PMTU](lesson/25b-mtu-pmtu.md) | chua-hoc | — | — |
| 26a | [Incident triage](lesson/26a-incident-triage.md) | chua-hoc | — | — |
| 26b | [Fix, rollback, regression checks](lesson/26b-post-fix-regression.md) | chua-hoc | — | — |
| 27a | [Spark driver/executor baseline](lesson/27a-spark-driver-executor-baseline.md) | chua-hoc | — | — |
| 27b | [Spark network incident](lesson/27b-spark-network-incident.md) | chua-hoc | — | — |
| 28a | [Network Readiness Report](lesson/28a-network-readiness-report.md) | chua-hoc | — | — |
| 28b | [Bảo vệ capstone và Final](lesson/28b-capstone-defense-final.md) | chua-hoc | — | — |

## Checkpoint gates

Không tự đánh dấu cổng đạt chỉ vì bài cuối giai đoạn đã `dat`; đối chiếu đủ [tiêu chí](CHECKPOINTS.md). Sau mỗi cổng chưa đạt, quay lại phần thiếu trước khi chuyển giai đoạn.

| Cổng | Sau bài | Tiêu chí | Trạng thái | Ngày đánh giá | Bằng chứng / phần cần làm lại |
|---|---|---|---|---|---|
| CP1 | 04b | [IP, ARP, routing](CHECKPOINTS.md#cp1--sau-04b) | chua-hoc | — | — |
| CP2 | 08b | [DNS/TCP/HTTP/TLS, NAT](CHECKPOINTS.md#cp2--sau-08b) | chua-hoc | — | — |
| CP3 | 12b | [Linux router/firewall, capture, rollback](CHECKPOINTS.md#cp3--sau-12b) | chua-hoc | — | — |
| CP4 | 16b | [Cross-node theo CNI thật](CHECKPOINTS.md#cp4--sau-16b) | chua-hoc | — | — |
| CP5 | 20b | [Ba Service/DNS incidents](CHECKPOINTS.md#cp5--sau-20b) | chua-hoc | — | — |
| CP6 | 24b | [Ingress/LB/policy](CHECKPOINTS.md#cp6--sau-24b) | chua-hoc | — | — |
| Final | 28b | [Report 4 VM + case Spark](CHECKPOINTS.md#final--sau-28b) | chua-hoc | — | — |

## Hàng đợi ôn tập

Chưa có nội dung được đánh giá cần ôn. Khi có, ghi một câu hỏi/phép thử cụ thể; ngày ôn là gợi ý được học viên đồng ý, không phải lịch tự động.

| Bài/năng lực | Điểm cần ôn | Phép thử lại / bằng chứng cần có | Ngày ôn dự kiến | Kết quả / link notes |
|---|---|---|---|---|
| — | — | — | — | — |

## Blocker và việc cần chuẩn bị

Chưa có blocker được xác nhận. Chỉ thêm sau preflight hoặc khi học thực tế, kèm môi trường cụ thể, điều kiện gỡ và bước hành động tiếp theo; không ghi thiếu quyền công ty thành lý do bắt buộc xin thêm quyền để học.

| Bài | Blocker thực tế | Điều kiện gỡ | Bước tiếp / người xử lý |
|---|---|---|---|
| — | — | — | — |

## Lịch sử phiên học

| Ngày/giờ | Bài / thời lượng | Kết quả và bằng chứng | Gợi ý cao nhất / xem lời giải | Bước tiếp theo |
|---|---|---|---|---|
| 2026-10-24 13:05 | 00 / 15 phút | Đạt bài 00: đánh giá đầu vào, xác nhận WSL2 lab, inventory công cụ [notes/00.md](notes/00.md) | Tự làm / không gợi ý | Bắt đầu bài 01a |
| 2026-10-24 13:20 | 01a / 25 phút | Đạt bài 01a: endpoint (IP:port), ephemeral port, demultiplexing, đối chứng connection refused [notes/01a.md](notes/01a.md) | Giải thích demultiplexing | Bắt đầu bài 01b |
| 2026-10-24 15:15 | 01b / 25 phút | Đạt bài 01b: loopback vs IP interface vs wildcard 0.0.0.0, giải quyết case container k8s bind localhost [notes/01b.md](notes/01b.md) | Đối chứng bind loopback vs wildcard | Bắt đầu bài 02a |
