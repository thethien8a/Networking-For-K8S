# Theo dõi tiến độ Networking for Kubernetes

> Đây là nguồn trạng thái học duy nhất. Khởi tạo trống: việc tạo giáo trình không phải một phiên học và không chứng minh bài nào đã đạt. Quy tắc cập nhật nằm trong [AGENTS.md](AGENTS.md).

## Điểm nối tiếp cho phiên sau

- **Bài chính đã đạt:** 8/56, không tính bài 00.
- **Checkpoint đã đạt:** 1/7, gồm CP1–CP6 và Final.
- **Bài đang học:** [05a — Port, socket, TCP/UDP](lesson/05a-port-socket-tcp-udp.md) (`chua-hoc`).
- **Bước đang dừng:** Hoàn thành xuất sắc bài 04b và Cổng đánh giá CP1 (`dat`): học viên tự chẩn đoán và khắc phục sự cố Route Shadowing trong lab, dùng `ip route get` (L3) và `ip neigh` (L2 FAILED) để bắt bệnh, tính chuẩn subnet /26, nắm chắc L3 IP end-to-end vs L2 MAC hop-by-hop, nêu đúng 3 lý do mất liên lạc giữa các IP hợp lệ. LLM đã tự động dọn sạch lab.
- **Bước tiếp theo:** Bắt đầu bài [05a — Port, socket, TCP/UDP](lesson/05a-port-socket-tcp-udp.md).
- **Bằng chứng/notes gần nhất:** [notes/04b.md](notes/04b.md); bài trước: [notes/04a.md](notes/04a.md).
- **Blocker đã xác nhận:** Không có blocker.
- **Thay đổi lab còn tồn tại / nợ rollback:** Không còn nợ rollback. LLM đã dọn dẹp sạch toàn bộ: xóa container `nfk-04b-troubleshoot`, xóa network `nfk-04b` và đã xác minh sạch sẽ qua CLI.
- **Ngày học gần nhất / cập nhật từ phiên học (Asia/Ho_Chi_Minh):** 2026-10-25

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
| 02a | [IPv4 và CIDR](lesson/02a-ipv4-va-cidr.md) | dat | 2026-10-24 | [notes/02a.md](notes/02a.md) |
| 02b | [IP plan 4 VM và prefix](lesson/02b-ip-plan-va-prefix.md) | dat | 2026-10-25 | [notes/02b.md](notes/02b.md); đủ IP plan, giải thích và route/ARP thực tế; review đã sửa đúng, hoàn nguyên và cleanup xong; bỏ 3/2/1 theo yêu cầu kết thúc |
| 03a | [Ethernet, MAC, ARP](lesson/03a-ethernet-mac-arp.md) | dat | 2026-10-25 | [notes/03a.md](notes/03a.md); học viên tự thao tác: xác nhận context, đọc IP/MAC/cache, tự capture/ping thực tế, đối chứng cache tái dùng và NUD probe, review đúng switch L2 và gateway next hop |
| 03b | [Neighbor cache và gateway](lesson/03b-neighbor-cache-va-gateway.md) | dat | 2026-10-25 | [notes/03b.md](notes/03b.md); đối chứng IP đích cuối vs MAC gateway, tự xóa 1 entry lab không flush cả bảng, tự capture tcpdump thấy ARP broadcast trước ICMP, phân tích FAILED L2; LLM đã cleanup toàn bộ |
| 04a | [Routing table](lesson/04a-routing-table.md) | dat | 2026-10-25 | [notes/04a.md](notes/04a.md); học viên tự đọc connected/default route, dự đoán và đo khớp 5/5 đích qua ip route get (lo cho chính mình, direct on-link, via gateway), giải đúng 5/5 LPM prefix chồng lấn; LLM đã dọn dẹp lab |
| 04b | [Lỗi routing và CP1](lesson/04b-route-fault-va-cp1.md) | dat | 2026-10-25 | [notes/04b.md](notes/04b.md); học viên tự dùng ip route get và ip neigh phát hiện route shadowing và ARP FAILED, xóa route lỗi tối thiểu và đối chứng phục hồi; hoàn thành 4/4 tiêu chí CP1 (subnet /26, L2 MAC vs L3 IP, 3 nguyên nhân mất kết nối); lab đã dọn dẹp sạch |
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
| CP1 | 04b | [IP, ARP, routing](CHECKPOINTS.md#cp1--sau-04b) | dat | 2026-10-25 | Đạt toàn bộ tiêu chí CP1: tính đúng subnet /26, phân loại 4 host; giải thích đúng L3 IP end-to-end vs L2 MAC hop-by-hop; bắt bệnh thực tế trong lab với ip route get và ip neigh FAILED; nêu đúng 3 lý do mất kết nối giữa IP hợp lệ [notes/04b.md](notes/04b.md) |
| CP2 | 08b | [DNS/TCP/HTTP/TLS, NAT](CHECKPOINTS.md#cp2--sau-08b) | chua-hoc | — | — |
| CP3 | 12b | [Linux router/firewall, capture, rollback](CHECKPOINTS.md#cp3--sau-12b) | chua-hoc | — | — |
| CP4 | 16b | [Cross-node theo CNI thật](CHECKPOINTS.md#cp4--sau-16b) | chua-hoc | — | — |
| CP5 | 20b | [Ba Service/DNS incidents](CHECKPOINTS.md#cp5--sau-20b) | chua-hoc | — | — |
| CP6 | 24b | [Ingress/LB/policy](CHECKPOINTS.md#cp6--sau-24b) | chua-hoc | — | — |
| Final | 28b | [Report 4 VM + case Spark](CHECKPOINTS.md#final--sau-28b) | chua-hoc | — | — |

## Hàng đợi ôn tập

Điểm chọn gateway trong review 02b đã được làm lại đúng sau phản hồi; giữ kết quả bên dưới, chưa hẹn ngày ôn. Ngày ôn là gợi ý được học viên đồng ý, không phải lịch tự động.

| Bài/năng lực | Điểm cần ôn | Phép thử lại / bằng chứng cần có | Ngày ôn dự kiến | Kết quả / link notes |
|---|---|---|---|---|
| 02b — gateway trực tiếp | C `.70/27`: tính đúng `.64–.95` nhưng chấp nhận gateway `.62` ngoài subnet trong mô hình không có route đặc biệt | Đề xuất một IP gateway phù hợp, khác IP C, và giải thích bằng dải subnet | — | Đã tự chọn đúng `.77` sau phản hồi, lý do cùng subnet; 2026-10-25 00:33 [notes/02b.md](notes/02b.md) |

## Blocker và việc cần chuẩn bị

Không có blocker còn tồn tại. Lỗi engine lúc 11:51 đã được gỡ bằng việc học viên mở Docker Desktop và kiểm tra lại; giữ bằng chứng thất bại/thành công trong [notes 03a](notes/03a.md).

| Bài | Blocker thực tế | Điều kiện gỡ | Bước tiếp / người xử lý |
|---|---|---|---|
| — | — | — | — |

## Lịch sử phiên học

| Ngày/giờ | Bài / thời lượng | Kết quả và bằng chứng | Gợi ý cao nhất / xem lời giải | Bước tiếp theo |
|---|---|---|---|---|
| 2026-10-24 13:05 | 00 / 15 phút | Đạt bài 00: đánh giá đầu vào, xác nhận WSL2 lab, inventory công cụ [notes/00.md](notes/00.md) | Tự làm / không gợi ý | Bắt đầu bài 01a |
| 2026-10-24 13:20 | 01a / 25 phút | Đạt bài 01a: endpoint (IP:port), ephemeral port, demultiplexing, đối chứng connection refused [notes/01a.md](notes/01a.md) | Giải thích demultiplexing | Bắt đầu bài 01b |
| 2026-10-24 15:15 | 01b / 25 phút | Đạt bài 01b: loopback vs IP interface vs wildcard 0.0.0.0, giải quyết case container k8s bind localhost [notes/01b.md](notes/01b.md) | Đối chứng bind loopback vs wildcard | Bắt đầu bài 02a |
| 2026-10-24 17:20 | 02a / 30 phút | Đạt bài 02a: CIDR /27, network/broadcast/usable range, subnet mask, RFC 1918 [notes/02a.md](notes/02a.md) | Giải thích mô hình con đường / con ngõ | Bắt đầu bài 02b |
| 2026-10-24 23:39 | 02b / đang học, chưa chốt thời lượng | Cập nhật mốc 21:29: có IP plan, dự đoán baseline đúng và được route/ARP xác nhận; ping A ↔ B mỗi chiều 2/2 khi cùng `/27`; chưa đổi B [notes/02b.md](notes/02b.md) | Hướng dẫn tính `/29`, xét riêng từng chiều, sơ đồ giao bước đầu cho gateway; dự đoán baseline không cần gợi ý thêm | Dự đoán rồi đo case B `/29`; giữ lab có ownership và lệnh cleanup |
| 2026-10-24 23:53 | 02b / tiếp tục, chưa chốt thời lượng | Học viên tự nhắc lại đúng B `/29` ARP `.38`, subnet `.32–.39`; đã đổi riêng B và đo route/ARP hai chiều đúng dự đoán, ping mỗi chiều 0/2; A/gateway không đổi [notes/02b.md](notes/02b.md) | Không cần gợi ý thêm cho dự đoán này; LLM hỗ trợ lệnh đo, chưa sửa | Học viên chọn nơi bắt gói để phân biệt nhận/chuyển tiếp trước khi sửa; giữ B `/29`, rollback đã ghi |
| 2026-10-25 00:04 | 02b / tiếp tục | Học viên nói “nào cũng được” về nơi bắt gói; LLM giải thích và chọn gateway, chưa phát traffic/capture mới hoặc sửa [notes/02b.md](notes/02b.md) | Giải thích vì sao chọn điểm đo; chưa xác nhận học viên tự chọn được | Chờ dự đoán chiều gói tại gateway trước phép thử; sau đó preflight và đo |
| 2026-10-25 00:12 | 02b / tiếp tục | Dự đoán “chỉ đi vào” khớp capture gateway: 2 request B → A vào, 0 ra; ping chính gateway 2/2; global/interface forwarding 0, cấu hình cả ba container không đổi [notes/02b.md](notes/02b.md) | LLM chọn điểm đo và giải thích nhận/chuyển tiếp; học viên dự đoán trước traffic | Học viên đề xuất prefix B để trở về IP plan đồng nhất, rồi hoàn nguyên/đo lại; chưa sửa hoặc cleanup |
| 2026-10-25 00:20 | 02b / tiếp tục | Học viên đề xuất `/27`, giải thích dải `.32–.63`; đã hoàn nguyên B, route/ARP trực tiếp, ping A ↔ B mỗi chiều 2/2, đối chứng B → gateway vẫn 2/2; A/gateway và forwarding không đổi [notes/02b.md](notes/02b.md) | Không cần gợi ý thêm cho đề xuất prefix; LLM thực hiện lệnh và đo kiểm | Review case C `.70/27` với gateway đề xuất `.62`, tổng kết 3/2/1 rồi dọn lab; chưa đánh dấu đạt |
| 2026-10-25 00:27 | 02b / review case giấy | C `.70/27`: học viên tính đúng `.64–.95` nhưng chấp nhận gateway trực tiếp `.62` ngoài subnet; chưa đạt phần review này, không thay đổi lab [notes/02b.md](notes/02b.md) | Đối chiếu IP gateway với dải subnet học viên đã tính; chưa đưa IP gateway thay thế | Học viên tự chọn IP gateway phù hợp và giải thích; sau review còn tổng kết 3/2/1 và cleanup |
| 2026-10-25 00:33 | 02b / review case giấy, làm lại | Học viên tự đề xuất gateway `192.168.10.77` vì thuộc `.64–.95`, đúng với C `.70/27`; đã sửa được phần chọn IP sau phản hồi, không đổi lab [notes/02b.md](notes/02b.md) | Phản hồi về gateway ngoài subnet; không cho sẵn IP thay thế | Tổng kết 3/2/1, trước hết 3 điều học được; còn cleanup trước khi chốt bài |
| 2026-10-25 01:04 | 02b / kết thúc | Học viên yêu cầu kết thúc vì phiên kéo dài, bỏ 3/2/1; chốt `dat` theo IP plan, giải thích/route/ARP và review đã có; đã xác minh dọn đúng container/network/image của bài, giữ nguyên image nền [notes/02b.md](notes/02b.md) | Hướng dẫn tính `/29` và sơ đồ next hop như các lượt trước; không hỏi thêm | Lần học tiếp: 03a, chưa bắt đầu; không còn nợ rollback/cleanup |
| 2026-10-25 11:51 | 03a / nối lại và preflight, chưa chốt thời lượng | Tự chọn đúng MAC B trong câu ôn, xác nhận Windows + Docker; host/context đúng nhưng không truy cập được Linux engine; chưa setup/traffic/capture [notes/03a.md](notes/03a.md) | Nhắc lại cùng subnet nên gửi trực tiếp; chưa đưa lời giải challenge | Học viên mở/kiểm tra Docker Desktop, kiểm tra lại server rồi xin chấp thuận setup lab; không có nợ rollback mới |
| 2026-10-25 12:01 | 03a / tiếp tục preflight và setup | Học viên mở Docker Desktop, kiểm tra server thành công và chọn tạo lab riêng; đã build image/tạo network/hai container, xác minh IP/MAC/route/cache trống; chưa ping/capture [notes/03a.md](notes/03a.md) | Không gợi ý thêm cho câu ôn; LLM làm setup, chưa làm challenge | Nền tảng ARP ngắn và dự đoán cache trước phép thử; giữ lab có nhãn ownership, cleanup chưa chạy |
| 2026-10-25 13:00 | 03a / dự đoán trước thực nghiệm | Học viên giải thích không bắt buộc ARP mới ở lần ping thứ hai nếu cache còn dùng được vì đã có mapping MAC B; đúng trên lý thuyết, chưa traffic/capture [notes/03a.md](notes/03a.md) | Đã giải thích nền tảng ARP/cache; không gợi ý thêm cho câu trả lời này | Học viên chọn interface bắt ARP trên A và giải thích; kiểm tra lại lab/cache trước đo, tiếp tục giữ tài nguyên bài |
| 2026-10-25 13:16 | 03a / capture và đối chứng cache | Học viên chọn `eth0` có lý do; đã preflight/capture 4 giây, hai ping độc lập mỗi lần 1/1, A học MAC B REACHABLE, không ARP mới lượt hai; 7 gói, 0 dropped, capture đã dừng, cấu hình không đổi. Có 2 request đầu cùng nội dung chưa rõ nguyên nhân [notes/03a.md](notes/03a.md) | LLM cung cấp/thực thi lệnh theo interface học viên chọn; chưa giải thích hộ Ethernet header | Học viên đọc MAC đích ARP request; tiếp tục L2/L3/phạm vi broadcast, review và cleanup, giữ lab có ownership |
| 2026-10-25 13:40 | 03a / điều chỉnh cách thực hành theo phản hồi | Học viên yêu cầu được hướng dẫn tự làm, không để LLM chạy hết lab. Phân loại output trước là demo do LLM thực hiện, giữ dự đoán/chọn interface đúng [notes/03a.md](notes/03a.md) | LLM nhận đã làm thay; dừng tự chạy lệnh lab, lưu sở thích đã xác nhận trong hồ sơ | Học viên tự xác nhận hostname/context rồi thực hành từng bước; LLM chờ output |
| 2026-10-25 15:00 | 03a / hoàn thành thực hành tự chủ (45 phút) | Đạt bài 03a: học viên tự chạy preflight, đọc L2/L3 A và B, tự bật tcpdump và ping đối chứng neighbor cache, NUD probe; giải thích switch L2 và gateway next hop [notes/03a.md](notes/03a.md) | Gợi ý NUD probe và chuyển tiếp L2 switch | Bắt đầu bài 03b |
| 2026-10-25 15:35 | 03b / 25 phút | Đạt bài 03b: học viên tự dự đoán và đo kiểm IP đích cuối vs MAC gateway, xóa 1 neighbor entry lab không flush bảng, tự capture ARP request/reply trước ICMP, phân tích lỗi L2 FAILED [notes/03b.md](notes/03b.md) | Hint 1 (học viên tự trả lời đúng và thực hiện độc lập) | Bắt đầu bài 04a |
| 2026-10-25 16:50 | 04a / 30 phút | Đạt bài 04a: đọc routing table, dự đoán & đối chứng khớp 5/5 đích với ip route get, giải đúng 5/5 LPM prefix chồng lấn Kubernetes, phân biệt có route vs reachability [notes/04a.md](notes/04a.md) | Tự làm / không cần gợi ý | Bắt đầu bài 04b |
| 2026-10-25 19:25 | 04b + CP1 / 40 phút | Đạt bài 04b và hoàn thành Cổng đánh giá CP1: học viên tự chạy chẩn đoán, phát hiện lỗi route shadowing và L2 ARP FAILED, sửa tối thiểu và đối chứng; vượt qua toàn bộ câu hỏi CP1 [notes/04b.md](notes/04b.md) | Gợi ý mô hình bưu cục / thư bưu điện cho L2 MAC vs L3 IP | Bắt đầu bài 05a |
