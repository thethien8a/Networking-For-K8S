# Mục lục 56 bài học nhỏ

Mỗi **buổi gốc** được chia thành hai bài **a/b**, thường 30–60 phút/bài. Học theo thứ tự trong bảng; các lab dài được tiếp tục qua nhiều phiên. [Giáo trình gốc](../networking_for_kubernetes_28_buoi.md) giữ phạm vi và mục tiêu, [PROGRESS.md](../PROGRESS.md) giữ trạng thái học.

**Bắt đầu:** [00 — Khởi động và xác nhận môi trường](00-khoi-dong.md), 20–30 phút, không tính vào 56 bài chính.

Mỗi bài là một đơn vị học có mô hình nền tảng, dự đoán, challenge, đối chứng/Break & Fix và tiêu chí bằng chứng. Đây không phải bộ script copy-paste hoặc lời giải sẵn: LLM theo [AGENTS.md](../AGENTS.md) sẽ hướng dẫn từng bước sau khi kiểm tra [môi trường](../LAB.md). Checkbox trong bài là tiêu chí tham chiếu; ghi kết quả thật vào notes và bảng tracking, không sửa nội dung bài thành nhật ký.

## Giai đoạn 1 — Computer Networking Fundamentals

| Buổi gốc | Bài a | Bài b |
|---|---|---|
| 01 | [01a — HTTP request và endpoint](01a-http-request-va-endpoint.md) | [01b — Loopback và listen address](01b-loopback-va-listen-address.md) |
| 02 | [02a — IPv4, subnet mask, CIDR](02a-ipv4-va-cidr.md) | [02b — IP plan 4 VM và prefix](02b-ip-plan-va-prefix.md) |
| 03 | [03a — Ethernet, MAC, ARP](03a-ethernet-mac-arp.md) | [03b — Neighbor cache và gateway](03b-neighbor-cache-va-gateway.md) |
| 04 | [04a — Routing table](04a-routing-table.md) | [04b — Lỗi routing và CP1](04b-route-fault-va-cp1.md) |

**Cổng:** [CP1](../CHECKPOINTS.md#cp1--sau-04b) — subnet, ARP, next hop và route evidence.

## Giai đoạn 2 — Protocols & Traffic Analysis

| Buổi gốc | Bài a | Bài b |
|---|---|---|
| 05 | [05a — Port, socket, TCP/UDP](05a-port-socket-tcp-udp.md) | [05b — TCP handshake và states](05b-tcp-handshake.md) |
| 06 | [06a — Refused, timeout, reset](06a-refused-timeout-reset.md) | [06b — Connect/app timeout và retry](06b-timeout-retry-va-dieu-tra.md) |
| 07 | [07a — Resolver và DNS records](07a-resolver-va-dns-records.md) | [07b — IP được, hostname lỗi](07b-ip-duoc-hostname-loi.md) |
| 08 | [08a — HTTP Host, TLS, certificate](08a-http-host-va-tls.md) | [08b — NAT, port mapping và CP2](08b-nat-port-mapping-va-cp2.md) |

**Cổng:** [CP2](../CHECKPOINTS.md#cp2--sau-08b) — phân biệt DNS/TCP/HTTP/TLS và giải thích NAT.

## Giai đoạn 3 — Linux Networking Internals

| Buổi gốc | Bài a | Bài b |
|---|---|---|
| 09 | [09a — Linux network inventory](09a-linux-network-inventory.md) | [09b — Sockets và connectivity matrix](09b-sockets-va-connectivity-matrix.md) |
| 10 | [10a — Network namespace và veth](10a-network-namespace-va-veth.md) | [10b — Linux bridge](10b-linux-bridge.md) |
| 11 | [11a — Hai subnet qua router namespace](11a-router-namespace-hai-subnet.md) | [11b — Return path và forwarding](11b-return-path-va-forwarding.md) |
| 12 | [12a — Firewall hooks và conntrack](12a-firewall-va-conntrack.md) | [12b — DROP/REJECT, rollback và CP3](12b-drop-reject-va-cp3.md) |

**Cổng:** [CP3](../CHECKPOINTS.md#cp3--sau-12b) — topology hai subnet thật, capture, lỗi routing/firewall và rollback.

## Giai đoạn 4 — Container & Pod Networking

| Buổi gốc | Bài a | Bài b |
|---|---|---|
| 13 | [13a — Pod shared network namespace](13a-pod-shared-network-namespace.md) | [13b — Bind address và hostNetwork](13b-bind-address-va-hostnetwork.md) |
| 14 | [14a — CNI, Pod IP, same-node placement](14a-cni-va-same-node-placement.md) | [14b — Same-node data path](14b-same-node-data-path.md) |
| 15 | [15a — Underlay, overlay, cross-node](15a-underlay-overlay-cross-node.md) | [15b — Cross-node evidence và fault](15b-cross-node-evidence-va-fault.md) |
| 16 | [16a — CNI events/logs triage](16a-cni-events-logs-triage.md) | [16b — Cross-node incident và CP4](16b-cross-node-incident-va-cp4.md) |

**Cổng:** [CP4](../CHECKPOINTS.md#cp4--sau-16b) — root cause cross-node theo CNI thật, không giả định VXLAN/bridge.

## Giai đoạn 5 — Kubernetes Service & DNS

| Buổi gốc | Bài a | Bài b |
|---|---|---|
| 17 | [17a — Service, selector, EndpointSlice](17a-service-selector-endpointslice.md) | [17b — Selector mismatch](17b-selector-mismatch.md) |
| 18 | [18a — Service proxy/data plane](18a-service-proxy-data-plane.md) | [18b — port, targetPort, listener](18b-port-targetport-listener.md) |
| 19 | [19a — CoreDNS, namespace, FQDN](19a-coredns-service-names.md) | [19b — DNS resolution path](19b-dns-resolution-path.md) |
| 20 | [20a — Hai Service mini-incidents](20a-service-mini-incidents.md) | [20b — DNS incident và CP5](20b-dns-incident-va-cp5.md) |

**Cổng:** [CP5](../CHECKPOINTS.md#cp5--sau-20b) — ba reports độc lập, root cause và post-fix/control checks.

## Giai đoạn 6 — External Access & Network Security

| Buổi gốc | Bài a | Bài b |
|---|---|---|
| 21 | [21a — Port-forward và client location](21a-port-forward-va-client-location.md) | [21b — NodePort, hostPort, kind mapping](21b-nodeport-hostport-kind-mapping.md) |
| 22 | [22a — Ingress Controller, Host/path](22a-ingress-controller-host-path.md) | [22b — TLS termination](22b-ingress-tls-termination.md) |
| 23 | [23a — LoadBalancer bare-metal](23a-loadbalancer-bare-metal.md) | [23b — MetalLB feasibility lab](23b-metallb-feasibility-lab.md) |
| 24 | [24a — NetworkPolicy ingress](24a-networkpolicy-ingress.md) | [24b — DNS egress allowance và CP6](24b-networkpolicy-dns-egress-va-cp6.md) |

**Cổng:** [CP6](../CHECKPOINTS.md#cp6--sau-24b) — Ingress/TLS, giới hạn LoadBalancer và policy có traffic proof. Riêng LoadBalancer có nhánh mô phỏng nếu topology không phù hợp, phải ghi rõ.

## Giai đoạn 7 — Troubleshooting, Spark & Capstone

| Buổi gốc | Bài a | Bài b |
|---|---|---|
| 25 | [25a — Pod egress và SNAT](25a-pod-egress-snat.md) | [25b — MTU và PMTU](25b-mtu-pmtu.md) |
| 26 | [26a — Incident triage](26a-incident-triage.md) | [26b — Fix, rollback, regression checks](26b-post-fix-regression.md) |
| 27 | [27a — Spark driver/executor baseline](27a-spark-driver-executor-baseline.md) | [27b — Spark network incident](27b-spark-network-incident.md) |
| 28 | [28a — Network Readiness Report](28a-network-readiness-report.md) | [28b — Bảo vệ capstone và Final](28b-capstone-defense-final.md) |

**Cổng:** [Final](../CHECKPOINTS.md#final--sau-28b) — report cho 4 VM có thể dùng lại, kèm case Spark thực hành và ranh giới quyền rõ ràng.
