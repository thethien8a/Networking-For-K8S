# LỘ TRÌNH NETWORKING FOR KUBERNETES

**Từ nền tảng mạng máy tính đến Kubernetes Networking chuyên sâu — 28 buổi, challenge-first**

> Phiên bản: 1.0 | Ngày biên soạn: 19/10/2026 | Ngôn ngữ: Tiếng Việt

## 1. Hồ sơ học viên và mục tiêu

### 1.1. Điểm xuất phát

- Vai trò: Data Engineer, muốn hiểu Networking để sử dụng Kubernetes hiệu quả hơn và tiến dần sang Data Platform Engineering.
- Trình độ mạng hiện tại (tự đánh giá): mới biết IP, port, localhost và DNS ở mức cơ bản.
- Phạm vi mong muốn: nền tảng Networking vững **và** Kubernetes Networking chuyên sâu.
- Thời lượng: **1–2 giờ/buổi**.
- Phương pháp: **giải thích nền tảng ngắn → dự đoán → challenge-first → tạo lỗi và troubleshoot → review**.
- Lab cá nhân: macOS Apple Silicon, OrbStack, kind; định hướng cluster lab 1 control-plane + 2 worker.
- Môi trường công ty: 4 VM Ubuntu 22.04 trên VMware (dự kiến 1 control-plane + 3 worker); **chỉ có quyền quản trị bên trong VM**, không có quyền cấu hình vSwitch/VLAN/vSphere.
- Chưa có sự cố mạng Kubernetes thực tế cần giải quyết; chương trình sẽ tạo lỗi có chủ đích trong môi trường lab riêng.

### 1.2. Mục tiêu cuối khóa

Sau khóa học, học viên có thể:

1. Giải thích vai trò của IP, CIDR/subnet, ARP, gateway, routing, TCP/UDP, DNS, NAT, firewall và MTU trong một luồng kết nối.
2. Quan sát và kiểm chứng bằng `ip`, `ss`, `dig`/`nslookup`, `curl`, `nc`, `tcpdump`, `traceroute`/`tracepath` (khi có).
3. Tạo mô hình Linux Networking bằng network namespace, veth, bridge và routing; hiểu mối liên hệ với container networking.
4. Phân tích Pod-to-Pod (cùng node/khác node), Pod-to-Service, DNS, external access, NetworkPolicy và lưu lượng ra ngoài cluster trong Kubernetes.
5. Debug lỗi kết nối có phương pháp: nêu giả thuyết, thu thập bằng chứng, cô lập tầng lỗi, sửa và xác minh.
6. Lập bản thiết kế và checklist network readiness cho Kubernetes trên **1 control-plane + 3 worker VM**; chỉ ra rõ những điều phải phối hợp với đội quản trị VMware/network.

**Không phải mục tiêu chính:** học toàn bộ CCNA, cấu hình router/switch vật lý, BGP/OSPF chuyên sâu hay quản trị VMware vSphere.

## 2. Quy tắc triển khai lộ trình

### 2.1. Cấu trúc một buổi học (gợi ý ~90–110 phút)

| Phần | Thời lượng | Hoạt động |
|---|---:|---|
| Nền tảng | 15 phút | Giải thích khái niệm, sơ đồ gói tin, ví dụ đời thực |
| Dự đoán | 10 phút | Nêu dự đoán trước khi chạy lệnh |
| Challenge | 45–55 phút | Tự làm bài, không xem lời giải ngay |
| Break & Fix | 15–20 phút | Tạo lỗi an toàn hoặc nhận một tình huống lỗi |
| Review | 10 phút | Giải thích cơ chế, đối chiếu dự đoán và bằng chứng |

Nếu một lab không thể hoàn tất trong 1–2 giờ, được phép **chia thành nhiều buổi**; ưu tiên hiểu đúng hơn hoàn thành cho đủ lịch.

### 2.2. Mẫu đầu ra bắt buộc cho mỗi buổi

Lưu lại một ghi chú Markdown với cấu trúc:

```markdown
# Buổi XX — Tên bài
## Mục tiêu
## Dự đoán trước lab
## Topology và địa chỉ IP
## Các bước thực hành / câu lệnh
## Bằng chứng (output, pcap, ảnh chụp nếu cần)
## Lỗi gặp phải và phân tích root cause
## Cách sửa và bằng chứng xác minh
## 3 điều hiểu được / 2 điều còn thắc mắc / 1 thử nghiệm tiếp theo
```

### 2.3. Nguyên tắc an toàn

- **Chỉ tạo sự cố có chủ đích trên lab cá nhân**, không phá kết nối của VM công ty hay cluster chia sẻ.
- Trước khi sửa `ip route`, network interface, `iptables`/`nftables` hoặc firewall trên VM đang SSH: cần có quyền phê duyệt, lối truy cập console dự phòng và rollback plan. Nếu chưa có, **không thực hiện**.
- Khi cần quan sát mạng trên VM công ty: ưu tiên lệnh **read-only**; kết quả quan sát cần tuân thủ chính sách bảo mật và quyền truy cập.
- Các bài `NetworkPolicy` phải dùng CNI hỗ trợ policy; Kubernetes API nhận đối tượng policy **không đồng nghĩa** policy đang được thi hành.
- Kiểu data plane có thể thay đổi theo CNI, phiên bản Kubernetes và cấu hình; **không mặc định** mọi cluster đều dùng cùng iptables mode, VXLAN hay kube-proxy.
- Với kind trên macOS/OrbStack, cluster node là container; **không coi** đó là mô hình tương đương hoàn toàn với 4 VM thực trên VMware.

## 3. Môi trường và công cụ

### 3.1. Các môi trường được chọn

| Môi trường | Bài học phù hợp | Lưu ý |
|---|---|---|
| Ubuntu Linux VM/lab Linux có đủ quyền cần thiết | Linux networking: namespace, veth, bridge, route, firewall | Các lệnh `ip netns`, firewall cần quyền phù hợp; cần kiểm tra hỗ trợ kernel và quyền trước lab |
| kind multi-node trên OrbStack/macOS | Kubernetes Pod, Service, DNS, Ingress, troubleshooting | Kiểm tra kiểu CNI, khả năng host-to-cluster và port mapping của môi trường thực |
| 4 VM Ubuntu 22.04 trên VMware của công ty | Khảo sát cấu hình, IP plan, kiểm tra connectivity được phép, capstone report | Không có quyền thay đổi mạng ở lớp VMware; mọi thay đổi guest phải được phê duyệt |

### 3.2. Bộ công cụ tối thiểu

- **Trên Linux:** `iproute2` (lệnh `ip`, `ss`), `ping`, `curl`, `dig` hoặc `nslookup`, `tcpdump`, `traceroute`/`tracepath`, `nc`, `iptables`/`nft` (khi được phép).
- **Trên máy cá nhân:** `kubectl`, `kind`, OrbStack, trình soạn thảo Markdown.
- **Cho Kubernetes:** image debug mạng phù hợp, `kubectl get/describe/logs/exec/debug` (tùy RBAC và image).
- **Tùy chọn:** Wireshark để đọc file `.pcap` (không bắt buộc).

> Không yêu cầu cài toàn bộ công cụ ngay từ đầu. Mỗi buổi chỉ chuẩn bị những công cụ cần thiết sau khi kiểm tra môi trường.

## 4. Bản đồ lộ trình

| Giai đoạn | Buổi | Chủ đề | Năng lực trọng tâm |
|---|---|---|---|
| 1 | 1–4 | Computer Networking Fundamentals | IP/subnet, ARP, routing |
| 2 | 5–8 | Protocols & Traffic Analysis | TCP/UDP, DNS, NAT, HTTP/TLS |
| 3 | 9–12 | Linux Networking Internals | netns, veth, bridge, forwarding, firewall |
| 4 | 13–16 | Container & Pod Networking | pod network, same-node/cross-node, CNI |
| 5 | 17–20 | Kubernetes Service & DNS | ClusterIP, EndpointSlice, service proxy, CoreDNS |
| 6 | 21–24 | External Access & Security | NodePort, Ingress, LoadBalancer, NetworkPolicy |
| 7 | 25–28 | Production Troubleshooting & Capstone | egress, MTU, incident response, Spark networking, VM checklist |

**Tiến độ đề xuất:** 3 buổi/tuần → khoảng 9–10 tuần, chưa tính buổi học bù hoặc ôn checkpoint. Nếu học chậm hơn thì điều chỉnh thời gian, không cắt checkpoint.

---

## 5. Chi tiết 28 buổi

### GIAI ĐOẠN 1 — Computer Networking Fundamentals

#### Buổi 01 — Một HTTP request đi qua mạng như thế nào?

- **Kiến thức:** vai trò máy khách/server, NIC, frame/packet/segment, mô hình TCP/IP, OSI ở mức định hướng; phân biệt local process và network endpoint.
- **Challenge:** chạy một HTTP server đơn giản trong lab; từ client dùng `curl` truy cập server; vẽ hành trình từ ứng dụng đến NIC, qua mạng, tới ứng dụng đích.
- **Break & Fix:** server chỉ bind loopback nhưng client kết nối bằng IP network interface; tìm khác biệt giữa địa chỉ đích và listen address.
- **Bằng chứng đạt:** output `ss` + `curl`, sơ đồ đường đi, giải thích được `127.0.0.1` khác IP LAN như thế nào.

#### Buổi 02 — IPv4, subnet mask và CIDR

- **Kiến thức:** host/network bits, subnet mask, CIDR, địa chỉ cùng subnet/khác subnet, private IPv4; tránh học mẹo nhẩm thay cho hiểu binary.
- **Challenge:** thiết kế IP plan minh họa cho 4 VM, xác định network address, usable addresses và subnet membership; kiểm tra bằng bài tính subnet và địa chỉ VM lab.
- **Break & Fix:** tạo tình huống (trên lab tách biệt) hai máy gán prefix không đồng nhất; quan sát route selection hoặc ARP khác dự đoán.
- **Bằng chứng đạt:** bảng IP/CIDR/gateway, giải thích được việc hai IP "trông giống nhau" chưa chắc cùng subnet.

#### Buổi 03 — Ethernet, MAC, ARP và broadcast domain

- **Kiến thức:** MAC address, L2 vs L3, ARP lookup, neighbor cache, broadcast, switch ở mức khái niệm.
- **Challenge:** dùng `ip neigh` và `tcpdump` để quan sát quá trình tìm MAC của gateway hoặc host cùng subnet (nếu môi trường cho phép).
- **Break & Fix:** làm mới neighbor cache **chỉ trong lab** rồi so sánh packet capture trước/sau; thảo luận tình huống ARP bị sai.
- **Bằng chứng đạt:** nhận diện được ARP request/reply, giải thích tại sao router được dùng khi đích ở subnet khác.

#### Buổi 04 — Gateway và routing table

- **Kiến thức:** connected route, default route, longest-prefix match, next hop, route decision.
- **Challenge:** dự đoán interface/next-hop của 5 địa chỉ khác nhau từ `ip route`; dùng `ip route get <IP>` để kiểm chứng.
- **Break & Fix:** trong lab, tạo route ưu tiên sai hoặc thiếu route; tìm nguyên nhân trước khi sửa.
- **Bằng chứng đạt / Checkpoint 1:** phân biệt thành công IP addressing, ARP, routing; giải thích ít nhất 3 nguyên nhân khiến hai host có IP hợp lệ nhưng không giao tiếp được.

### GIAI ĐOẠN 2 — Protocols & Traffic Analysis

#### Buổi 05 — TCP, UDP, port và socket

- **Kiến thức:** TCP/UDP, source/destination port, listen socket, 4-tuple, TCP three-way handshake, connection state.
- **Challenge:** mở TCP listener, kết nối từ client; dùng `ss` và `tcpdump` nhận diện SYN → SYN-ACK → ACK.
- **Break & Fix:** so sánh port đóng với port mở; giải thích RST và phân biệt với UDP không có handshake TCP.
- **Bằng chứng đạt:** packet capture được chú thích, sơ đồ handshake và mối liên hệ với socket.

#### Buổi 06 — Refused, timeout, reset và retry

- **Kiến thức:** `ECONNREFUSED`, connection timeout, reset, connect timeout vs application timeout, cơ chế retry cơ bản.
- **Challenge:** tạo ba tình huống lỗi trong lab: đích không có listener, traffic bị DROP, server đóng kết nối; quan sát lỗi ở client và network.
- **Break & Fix:** không biết trước loại lỗi; xác định từ `curl -v`, `ss`, packet capture và trạng thái server.
- **Bằng chứng đạt:** bảng triệu chứng → giả thuyết → lệnh kiểm chứng → kết luận; tránh kết luận chỉ từ một thông báo lỗi.

#### Buổi 07 — DNS từ resolver đến IP

- **Kiến thức:** DNS record A/AAAA/CNAME, resolver, nameserver, `/etc/hosts`, `/etc/resolv.conf`, caching ở mức cơ bản.
- **Challenge:** truy cập cùng endpoint qua IP và hostname, quan sát `dig`/`nslookup`, phân biệt resolution và TCP connect.
- **Break & Fix:** cố tình cung cấp hostname không có bản ghi hoặc cấu hình resolver trong lab; tìm nguyên nhân khi IP hoạt động nhưng tên không hoạt động.
- **Bằng chứng đạt:** sơ đồ truy vấn DNS, giải thích được `/etc/hosts` và DNS khác nhau ở đâu.

#### Buổi 08 — HTTP/HTTPS, TLS và NAT

- **Kiến thức:** Host header, URL, HTTP response, TLS handshake ở mức nền tảng; NAT, SNAT, DNAT; port mapping.
- **Challenge:** dùng `curl -v`/`--resolve` (khi phù hợp) phân biệt DNS name, HTTP Host và TCP destination; nghiên cứu NAT hiện có trong lab và quan sát thay đổi địa chỉ/port nếu môi trường hỗ trợ.
- **Break & Fix:** tạo tình huống hostname đúng nhưng chứng chỉ/Host routing sai (có thể dùng bài mô phỏng hoặc lab TLS nội bộ).
- **Bằng chứng đạt / Checkpoint 2:** phân biệt resolution failure, TCP failure, HTTP error và TLS validation failure.

### GIAI ĐOẠN 3 — Linux Networking Internals

#### Buổi 09 — Đọc trạng thái mạng một Linux host

- **Kiến thức:** interface, loopback, virtual NIC, addresses, routes, neighbor table, sockets.
- **Challenge:** lập báo cáo network inventory của Ubuntu lab với `ip -br addr`, `ip route`, `ip neigh`, `ss -lntup` và một connectivity matrix.
- **Break & Fix:** server listen sai địa chỉ hoặc sai port; xác định nguyên nhân mà không thay cấu hình mạng host.
- **Bằng chứng đạt:** bản vẽ interface/IP/route/server ports và output thực tế.

#### Buổi 10 — Network namespaces, veth và bridge

- **Kiến thức:** network namespace, veth pair, Linux bridge, sự khác nhau giữa process isolation và network isolation.
- **Challenge:** trên Ubuntu lab có quyền phù hợp, tạo hai namespace; kết nối qua veth vào bridge; cấp IP và kiểm tra ping/HTTP.
- **Break & Fix:** thiếu lệnh `ip link set ... up` hoặc gắn veth sai namespace; suy luận vì sao kết nối thất bại.
- **Bằng chứng đạt:** sơ đồ namespace–veth–bridge, output của `ip netns`, `ip link` và packet capture (nếu thực hiện được).

#### Buổi 11 — Routing giữa hai subnet

- **Kiến thức:** IP forwarding, router namespace, static route, return path, routing bất đối xứng ở mức nhập môn.
- **Challenge:** tạo hai subnet độc lập trong network namespace, nối qua router namespace; cấu hình để dịch vụ giữa hai subnet truy cập được nhau.
- **Break & Fix:** cố tình thiếu route chiều về hoặc tắt forwarding trong router namespace; tìm bằng `ip route get` và `tcpdump`.
- **Bằng chứng đạt:** sơ đồ hai chiều request/response và route tables của cả ba namespace.

#### Buổi 12 — Firewall và connection tracking

- **Kiến thức:** packet filtering, INPUT/FORWARD/OUTPUT, DROP vs REJECT, nftables/iptables, conntrack ở mức giải thích.
- **Challenge:** trên lab tách biệt, dùng quy tắc firewall chặn một cổng; so sánh lỗi và packet capture; khôi phục truy cập.
- **Break & Fix:** nhận một rule gây lỗi, xác định chính xác chain/hook hoặc quy tắc liên quan.
- **Bằng chứng đạt / Checkpoint 3:** tự dựng và giải thích mạng hai subnet qua router Linux, debug được một lỗi routing và một lỗi firewall; **có lệnh dọn dẹp và rollback**.

### GIAI ĐOẠN 4 — Container & Kubernetes Pod Networking

#### Buổi 13 — Từ Linux namespace sang container và Pod

- **Kiến thức:** container network namespace, loopback trong container, port bind, Pod shared network namespace, `hostNetwork`.
- **Challenge:** thử hai container trong cùng Pod truy cập nhau qua localhost; so sánh với hai Pod khác nhau.
- **Break & Fix:** một container listen trên `127.0.0.1` thay vì địa chỉ phù hợp với hướng kết nối; phân tích giới hạn truy cập.
- **Bằng chứng đạt:** mô tả đúng ranh giới network namespace giữa host, Pod và container.

#### Buổi 14 — Pod-to-Pod trong cùng node

- **Kiến thức:** Pod IP, Pod CIDR, CNI là giao diện cấu hình networking cho runtime, đường truyền của Pod trong cùng node.
- **Challenge:** tạo 2 Pod được schedule cùng worker bằng node placement được phép; chứng minh connectivity qua Pod IP; quan sát thông tin giao diện/route và CNI của lab.
- **Break & Fix:** server listen sai port/interface; phân biệt với lỗi CNI.
- **Bằng chứng đạt:** sơ đồ actual data path theo **CNI thực đang cài**, không giả định trước Linux bridge/veth nếu chưa quan sát.

#### Buổi 15 — Pod-to-Pod khác node

- **Kiến thức:** underlay/overlay, encapsulation và VXLAN như một ví dụ, route between nodes, MTU implications.
- **Challenge:** đặt Pod A trên Worker 1 và Pod B trên Worker 2; kiểm tra kết nối; thu thập route/IP/packet evidence phù hợp với loại CNI.
- **Break & Fix:** mô phỏng failure cross-node trong lab riêng và kiểm tra từng tầng; nếu thay đổi cấu hình network có thể phá cluster, ưu tiên fault injection có rollback rõ ràng.
- **Bằng chứng đạt:** giải thích gói tin từ source Pod đến destination Pod và xác định cơ chế đúng của CNI đang sử dụng.

#### Buổi 16 — CNI troubleshooting

- **Kiến thức:** CNI deployment components, node address, pod address allocation, symptoms của cross-node failure, đọc logs và events.
- **Challenge:** điều tra một tình huống Pod cùng node hoạt động nhưng Pod khác node không hoạt động, bằng chứng minh bạch do bài lab dựng sẵn.
- **Break & Fix:** sử dụng bảng kiểm tra layer-by-layer: Pod → node → node underlay → destination Pod.
- **Bằng chứng đạt / Checkpoint 4:** báo cáo root cause, lệnh kiểm chứng, cách sửa và hạn chế của mô hình kind so với VM thật.

### GIAI ĐOẠN 5 — Kubernetes Service & DNS Internals

#### Buổi 17 — Service, selector và EndpointSlice

- **Kiến thức:** ClusterIP, selectors, EndpointSlice, sự khác biệt Pod IP và Service IP.
- **Challenge:** triển khai Service trỏ đến nhiều backend Pods; thay đổi nhãn Pod trong lab và quan sát EndpointSlice/traffic.
- **Break & Fix:** Service không có backend vì selector mismatch; xác nhận bằng `kubectl get endpointslice` và label queries.
- **Bằng chứng đạt:** mô tả quan hệ `Service → EndpointSlice → backend Pod` và cơ chế kiểm tra.

#### Buổi 18 — Service proxy và packet steering

- **Kiến thức:** kube-proxy, iptables/nftables mode ở mức khái niệm, DNAT; data plane eBPF có thể thay thế kube-proxy tùy CNI.
- **Challenge:** gửi request vào ClusterIP và xác định backend thực nhận request, quan sát network rules/data plane theo cấu hình cluster.
- **Break & Fix:** phân biệt sai `port`, `targetPort`, listener backend hoặc NetworkPolicy.
- **Bằng chứng đạt:** sơ đồ request vào Service và cách routing backend **theo triển khai thật**; không mặc định mọi cluster có kube-proxy.

#### Buổi 19 — CoreDNS và Service discovery

- **Kiến thức:** CoreDNS, Service DNS names, namespace, FQDN, `resolv.conf`, DNS search suffix.
- **Challenge:** từ namespace A resolve Service ở namespace B bằng short name/FQDN; quan sát khác biệt và kiểm tra DNS Service/EndpointSlice.
- **Break & Fix:** tên Service sai hoặc CoreDNS có lỗi trong lab; chẩn đoán resolution path.
- **Bằng chứng đạt:** nêu rõ tên nào resolve trong cùng namespace, tên nào cần namespace/FQDN.

#### Buổi 20 — Tổ hợp sự cố Service + DNS

- **Kiến thức:** playbook phân biệt DNS, EndpointSlice, service proxy, NetworkPolicy và application listener.
- **Challenge:** nhận 3 lab lỗi độc lập: selector sai, targetPort không khớp, DNS resolution lỗi; điều tra **không biết trước** lỗi nào thuộc nhóm nào.
- **Break & Fix:** sửa đúng chỗ, không “restart tất cả” để chữa lỗi.
- **Bằng chứng đạt / Checkpoint 5:** 3 mini-incident reports, mỗi báo cáo có dấu hiệu ban đầu, giả thuyết, chứng cứ và xác minh.

### GIAI ĐOẠN 6 — External Access & Network Security

#### Buổi 21 — Từ máy ngoài vào ứng dụng trong cluster

- **Kiến thức:** `kubectl port-forward`, NodePort, hostPort; khác nhau giữa chuyển tiếp phục vụ debug và cơ chế expose bền vững.
- **Challenge:** expose cùng ứng dụng theo hai cách; xác định IP/port client dùng; phân biệt client từ host Mac, Linux VM và Pod khác.
- **Break & Fix:** tưởng rằng NodePort của kind luôn truy cập được trực tiếp qua bất kỳ Node IP nào từ macOS; kiểm tra port mapping và giới hạn môi trường.
- **Bằng chứng đạt:** connectivity matrix theo vị trí client và phương pháp expose.

#### Buổi 22 — Ingress Controller và TLS termination

- **Kiến thức:** Ingress resource vs Ingress Controller, Host/path routing, HTTP vs HTTPS, TLS termination, reverse proxy.
- **Challenge:** triển khai 2 web apps qua 1 Ingress Controller; dùng hai hostname/path kiểm chứng routing.
- **Break & Fix:** sai host rule, service backend hoặc TLS hostname; kiểm tra HTTP headers, controller logs và Service.
- **Bằng chứng đạt:** request flow client → controller → Service → Pod và nguyên nhân 404/502/connection failure trong case đã tạo.

#### Buổi 23 — LoadBalancer trên bare-metal: khái niệm và lab

- **Kiến thức:** Kubernetes `type: LoadBalancer`; tại sao on-prem/bare-metal cần thành phần cấp và quảng bá địa chỉ bên ngoài; giới thiệu MetalLB và L2/ARP ở mức cơ chế.
- **Challenge:** nếu môi trường kind/OrbStack hỗ trợ topology thích hợp, cấu hình thử MetalLB trong **lab cô lập**, kiểm tra external IP và khả năng truy cập từ client được hỗ trợ; nếu không, dùng trace và topology mô phỏng.
- **Break & Fix:** trường hợp external IP được cấp nhưng máy ngoài không truy cập được; phân biệt ARP/L2 reachability với Service backend.
- **Bằng chứng đạt:** báo cáo tính khả thi của giải pháp LoadBalancer trên lab và hạn chế khi chuyển sang VMware 4 VM.

#### Buổi 24 — NetworkPolicy thực thi được

- **Kiến thức:** ingress/egress policy, default-deny, allow-list, selectors, DNS allowance, giới hạn và điều kiện CNI hỗ trợ policy.
- **Challenge:** dùng CNI có hỗ trợ policy, chỉ cho phép frontend → backend theo TCP port được chỉ định; thử traffic được phép và traffic bị chặn.
- **Break & Fix:** policy quá chặt làm Pod không resolve DNS; phân tích luồng DNS egress cần cho ứng dụng.
- **Bằng chứng đạt / Checkpoint 6:** ma trận allow/deny trước và sau policy, chứng cứ rằng policy **thực sự** có hiệu lực.

### GIAI ĐOẠN 7 — Production Troubleshooting & Capstone

#### Buổi 25 — Pod egress, SNAT và MTU

- **Kiến thức:** Pod → external destination, egress NAT tùy CNI, return path, MTU, fragmentation/PMTU ở mức troubleshooting.
- **Challenge:** so sánh source address nhìn thấy từ hệ thống ngoài lab của traffic phát từ Pod và từ node; đo/kiểm tra behavior MTU bằng công cụ phù hợp.
- **Break & Fix:** tạo tình huống Pod truy cập nội bộ được nhưng không truy cập được external endpoint; lần lượt loại trừ DNS, TCP, route, SNAT/policy.
- **Bằng chứng đạt:** bảng kiểm egress có phân biệt node-level và Pod-level reachability.

#### Buổi 26 — Incident response: một cluster, nhiều lỗi

- **Kiến thức:** quy trình debug theo giả thuyết, thu thập tối thiểu bằng chứng, kiểm tra control plane metadata vs data plane, tránh thay nhiều biến cùng lúc.
- **Challenge:** xử lý 3–4 sự cố trong lab tách biệt (ví dụ: DNS, selector, cross-node path, policy). Người học không được biết trước đáp án.
- **Break & Fix:** xác nhận fix không làm hỏng luồng traffic vốn đang chạy.
- **Bằng chứng đạt:** timeline, triệu chứng, root cause, thay đổi áp dụng, bước rollback và post-fix checks.

#### Buổi 27 — Spark driver/executor networking trên Kubernetes

- **Kiến thức:** driver/executor communication, service discovery, ports, Pod placement, cross-node connectivity; phân biệt lỗi networking với Spark application/resource errors.
- **Challenge:** dựng ứng dụng Spark lab phù hợp với cluster sẵn có; tình huống executor trên worker khác không kết nối được driver; thu thập Pod/node/IP/DNS/listener/traffic evidence.
- **Break & Fix:** ví dụ service/hostname driver sai hoặc port bị chặn **trong lab**; sửa dựa trên kiểm chứng thay vì thử config ngẫu nhiên.
- **Bằng chứng đạt:** sơ đồ Spark data/control connections và báo cáo lỗi. Các config Spark chính xác cần được tra theo **phiên bản Spark thực dùng** trước lab.

#### Buổi 28 — Capstone: Network Readiness cho Kubernetes 1 control-plane + 3 worker VM

- **Kiến thức:** kết nối node-to-node, IP plan, gateway, route, DNS, NTP, firewall port requirements theo distro/Kubernetes/CNI thực dùng, Pod CIDR vs Service CIDR, external access và observability.
- **Challenge:** lập phương án mạng cho 4 VM Ubuntu 22.04 trên VMware, **trong giới hạn quyền bên trong VM**. Chỉ quan sát hệ thống công ty bằng các lệnh được phép; kiểm chứng cấu hình bằng lab mô phỏng khi cần.
- **Break & Fix:** hội đồng review giả định đưa ra tình huống worker không kết nối worker khác hoặc NodePort không truy cập được; đề xuất các phép thử để xác định ranh giới trách nhiệm guest OS vs VMware/network team.
- **Bằng chứng đạt / Capstone:** nộp **Network Readiness Report** và bảo vệ trước các câu hỏi về đường đi gói tin, DNS, Service, firewall, CNI và rollback.

---

## 6. Các checkpoint và điều kiện đạt

| Checkpoint | Sau buổi | Bài kiểm tra thực hành | Điều kiện đạt |
|---|---:|---|---|
| CP1 | 4 | Phân loại same subnet/other subnet, đọc route | Nêu được next hop và lý do, kiểm chứng bằng lệnh |
| CP2 | 8 | Phân biệt DNS/TCP/HTTP/TLS failures | Có ít nhất một bằng chứng cụ thể cho mỗi kết luận |
| CP3 | 12 | Hai namespace/subnet kết nối qua router Linux | Có cấu hình, capture và rollback thực tế |
| CP4 | 16 | Pod cross-node failure | Vẽ đường đi theo CNI hiện tại và tìm đúng tầng lỗi |
| CP5 | 20 | 3 lỗi Service/DNS | 3 incident reports có root cause và post-fix check |
| CP6 | 24 | Ingress/LoadBalancer/NetworkPolicy | Hiểu giới hạn môi trường, chứng minh traffic được phép/bị chặn |
| Final | 28 | Network Readiness Report + case Spark | Report có thể được người khác sử dụng để thực hiện kiểm tra |

**Quy tắc chuyển giai đoạn:** Nếu chưa giải thích được nguyên nhân bằng bằng chứng thì chưa xem là đạt, dù lệnh cuối cùng đã chạy thành công.

## 7. Sản phẩm cuối khóa: Network Readiness Report

Báo cáo cần có tối thiểu:

1. **Topology:** 4 VM, địa chỉ IP, NIC, subnet, gateway; xác nhận phần nào là giả định/phần nào được quan sát.
2. **Connectivity matrix:** nguồn → đích → giao thức/port → lý do → kết quả kiểm tra → ai có quyền xử lý.
3. **Kubernetes address plan:** node IPs, Pod CIDR, Service CIDR và kiểm tra nguy cơ overlap với mạng doanh nghiệp.
4. **Data paths:** Pod↔Pod (same/cross-node), Pod↔Service, Pod→external, external→Ingress/LoadBalancer.
5. **DNS:** resolution trên node, trong Pod, Service FQDN, external DNS, dependencies.
6. **Firewall và CNI:** những điểm cần kiểm tra dựa vào Kubernetes distribution, CNI, service proxy và phiên bản *được chọn thực tế*.
7. **Các giới hạn của quyền quản trị:** vấn đề nào sửa được từ guest Ubuntu và vấn đề nào phải yêu cầu nhóm VMware/network hỗ trợ.
8. **Runbook:** lệnh read-only, ngưỡng khoanh vùng sự cố, checklist rollback, và bằng chứng cần lưu.
9. **Rủi ro chưa kiểm chứng:** không điền số port, CIDR, behavior CNI hoặc chức năng VMware bằng suy đoán.

## 8. Lịch học gợi ý

| Tuần | Buổi | Trọng tâm |
|---|---|---|
| 1 | 1–3 | Network basics, subnet, ARP |
| 2 | 4–6 | Routing, TCP/UDP, lỗi kết nối |
| 3 | 7–9 | DNS/NAT, Linux inventory |
| 4 | 10–12 | Network namespace, bridge, routing, firewall |
| 5 | 13–15 | Container/Pod, same-node/cross-node |
| 6 | 16–18 | CNI troubleshooting, Service proxy |
| 7 | 19–21 | CoreDNS, Service faults, external access |
| 8 | 22–24 | Ingress, LoadBalancer, NetworkPolicy |
| 9 | 25–27 | Egress/MTU, incidents, Spark networking |
| 10 | 28 + review | Capstone và củng cố phần còn yếu |

## 9. Cách dùng tài liệu với một LLM gia sư

Dán prompt sau cùng tài liệu này vào phiên học mới:

```text
Bạn là gia sư thực hành Networking for Kubernetes của tôi.

Bối cảnh:
- Tôi là Data Engineer; biết Kubernetes cơ bản nhưng Networking chỉ mới ở mức IP, port, localhost, DNS.
- Môi trường cá nhân: macOS Apple Silicon + OrbStack + kind multi-node, và có thể dùng Ubuntu lab.
- Môi trường công ty: 4 Ubuntu 22.04 VMs trên VMware; chỉ có quyền bên trong VM. Không tự ý làm thay đổi có rủi ro ở môi trường công ty.
- Tôi muốn học 28 buổi theo tài liệu đính kèm, mỗi buổi 1–2 giờ.
- Phương pháp: giải thích lý thuyết nền tảng ngắn -> bắt tôi dự đoán -> challenge-first -> troubleshoot -> review.

Nguyên tắc dạy:
1. Chỉ dạy đúng buổi đang học, không làm hộ toàn bộ challenge.
2. Trước lab, hỏi để kiểm tra kiến thức và bảo đảm môi trường có quyền cần thiết.
3. Nêu yêu cầu, topology, tiêu chí thành công và giới hạn an toàn trước khi cho lệnh.
4. Để tôi tự đề xuất cách làm. Khi tôi bí, gợi ý tăng dần (hint 1/2/3), chưa đưa đáp án hoàn chỉnh nếu tôi chưa yêu cầu.
5. Luôn yêu cầu tôi giải thích packet path, route, DNS, TCP và nguồn bằng chứng phù hợp.
6. Khi có lỗi, cùng tôi xây dựng giả thuyết và chọn lệnh quan sát trước khi sửa.
7. Không bịa API/config/port/CNI behavior; kiểm tra tài liệu theo phiên bản công cụ thực tế.
8. Kết thúc buổi bằng bài kiểm tra ngắn, feedback điểm mạnh/yếu, và checklist để lưu Markdown notes.
9. Chỉ sang buổi kế tiếp khi tôi đạt tiêu chí checkpoint.

Hãy bắt đầu từ Buổi 01, bằng một bài đánh giá đầu vào ngắn và một lab an toàn.
```

## 10. Nguồn tham khảo chính thống

- Kubernetes — Networking: https://kubernetes.io/docs/concepts/services-networking/
- Kubernetes — Services: https://kubernetes.io/docs/concepts/services-networking/service/
- Kubernetes — DNS for Services and Pods: https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/
- Kubernetes — NetworkPolicy: https://kubernetes.io/docs/concepts/services-networking/network-policies/
- Kubernetes — Debug Services: https://kubernetes.io/docs/tasks/debug/debug-application/debug-service/
- Kubernetes — Debugging DNS Resolution: https://kubernetes.io/docs/tasks/administer-cluster/dns-debugging-resolution/
- Kubernetes — kubectl debug: https://kubernetes.io/docs/reference/kubectl/generated/kubectl_debug/
- kind — Quick Start / Multinode: https://kind.sigs.k8s.io/docs/user/quick-start/
- kind — Configuration: https://kind.sigs.k8s.io/docs/user/configuration/
- Linux `ip-netns` manual: https://man7.org/linux/man-pages/man8/ip-netns.8.html
- Linux `ip-route` manual: https://man7.org/linux/man-pages/man8/ip-route.8.html
- Linux `tcpdump` manual: https://www.tcpdump.org/manpages/tcpdump.1.html

> **Lưu ý:** Các tài liệu chính thức có thể thay đổi theo phiên bản. Khi bắt đầu từng lab, ưu tiên kiểm tra version của Kubernetes, kind, CNI và công cụ Linux, sau đó đối chiếu tài liệu tương ứng trước khi áp dụng cấu hình.

---

**Khuyến nghị:** Hãy học chắc các buổi 01–12 trước khi đi sâu vào CNI. Năng lực quan trọng nhất không phải là ghi nhớ lệnh mà là **dự đoán và chứng minh đường đi của gói tin** bằng những bằng chứng thu thập được.
