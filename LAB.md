# Sổ môi trường và quy tắc an toàn lab

## 1. Kế hoạch từ giáo trình, không phải hiện trạng đã xác minh

| Môi trường | Mục đích | Giới hạn |
|---|---|---|
| Windows 11 + WSL2 + kind | Client và Kubernetes lab cá nhân; hướng tới 1 control-plane + 2 worker | Node kind là container trong Docker/WSL2; kiểm tra khả năng port mapping/reachability từ Windows |
| Ubuntu Linux lab riêng (WSL2 Ubuntu) | `ip netns`, veth, bridge, route, forwarding, firewall | Cần Linux kernel, công cụ và quyền phù hợp; không dùng runtime VM chia sẻ làm lab phá lỗi |
| 4 Ubuntu 22.04 VM công ty trên VMware | Khảo sát có phép, thiết kế 1 control-plane + 3 worker, readiness report | Chỉ có quyền trong guest theo đề cương; không có quyền vSwitch/VLAN/vSphere; không fault injection |

## 2. Inventory thực tế

Điền khi kiểm tra, không tự suy từ kế hoạch. Không lưu kubeconfig, secret hoặc địa chỉ nội bộ nhạy cảm.

| Thuộc tính | Giá trị đã xác minh | Bằng chứng / ngày |
|---|---|---|
| Máy thực thi, OS/architecture, hostname | Windows 11, `LAPTOP-G35HS1Q6`; Docker client `windows/amd64`, Linux engine `linux/amd64` | `hostname`, `docker.exe version`; học viên xác nhận Windows + Docker Desktop / 2026-10-24 |
| Linux lab riêng và đường truy cập/console | Theo kiểm tra WSL2 ngày 2026-10-24, chỉ có distro `docker-desktop`, chưa có Ubuntu WSL riêng. Lab 02b đã dọn. Lab Docker 03a được chấp thuận và tạo mới: `nfk-03a-a` `.50/27`, `nfk-03a-b` `.35/27`, cùng network `nfk-03a` internal bridge `10.20.30.32/27`; truy cập qua `docker.exe --context desktop-linux exec <tên> ...`, không qua shell VM runtime | `wsl.exe --list --verbose` ngày 2026-10-24; cleanup trong [notes 02b](notes/02b.md); kiểm tra hostname/container/network và cấu hình thật trong [notes 03a](notes/03a.md) / 2026-10-25 12:01 |
| Runtime và phiên bản | Docker Desktop 4.88.1, client/Engine 29.7.2, API 1.55; context `desktop-linux`, endpoint cục bộ `npipe:////./pipe/dockerDesktopLinuxEngine`. Lần 11:51 không tìm thấy pipe; sau khi học viên mở Docker Desktop, đã đọc được server và kiểm kê thành công, không còn lỗi kết nối tại mốc kiểm tra lại | `context ls/inspect`, `--context desktop-linux version`, `image ls`, `ps -a`, `network ls` / 2026-10-25 11:55–12:01, [notes 03a](notes/03a.md) |
| kind / kubectl client / Kubernetes server version | `kubectl.exe` đã có; `kind` chưa cài | CLI check / 2026-10-24 |
| Tên cluster, current-context, namespace học | Chưa xác minh (chưa tạo cluster) | — |
| Nodes, vai trò, IP, Pod CIDR | Chưa xác minh | — |
| CNI, phiên bản, khả năng NetworkPolicy | Chưa xác minh | — |
| Service proxy/data plane thực dùng | Chưa xác minh | — |
| Service CIDR, cluster DNS suffix, DNS Service IP | Chưa xác minh | — |
| Ingress/LoadBalancer controller nếu có | Chưa xác minh | — |
| Công cụ Linux và quyền quan sát/capture | Windows có `python.exe`, `curl`, `kubectl.exe` theo kiểm tra trước. Image mới `nfk-03a-tools:local`: Ubuntu 24.04.4 LTS, iproute2 6.1.0, iputils 20240117, tcpdump 4.99.4, libpcap 1.10.4. A/B dùng quyền Docker mặc định, có `cap_net_raw`, không có `cap_net_admin`; `Privileged=false`, không mount/publish port. Đã capture ARP/ICMP thực tế trên `eth0` của A với `-p`, giới hạn 4 giây; 7 gói, 0 dropped by kernel, process đã dừng. Image nền giữ nguyên ID sau build | Kiểm tra OS/package/capability lúc 12:01; kiểm tra ownership/cấu hình và capture trong [notes 03a](notes/03a.md) / 2026-10-25 13:13–13:16 |
| Client nào truy cập được node/cluster, port mapping | Chưa xác minh | — |
| Spark version/deploy mode/image khi đến bài 27 | Chưa xác minh | — |

## 3. Preflight trước từng bài

1. **Đúng máy:** client là Mac, Linux lab, Pod hay node? Ghi rõ nơi chạy từng lệnh; không trộn output từ nhiều network namespace.
2. **Đúng cluster:** xem `kubectl config current-context`, rồi kiểm tra node/context có khớp lab cá nhân không. Không công khai toàn bộ kubeconfig.
3. **Đủ công cụ:** chỉ kiểm tra/cài công cụ bài hiện tại cần. Các lệnh Linux như `ip`, `ss`, `ip netns` không phải lệnh macOS tương đương.
4. **Đủ quyền:** capture, firewall, network namespace có thể cần quyền cao. Không có quyền thì ghi blocker hoặc dùng phương án mô phỏng có nhãn.
5. **Đúng phạm vi:** ghi tên namespace, interface, route/rule, process hoặc manifest sắp thay đổi và chủ sở hữu của chúng.
6. **Có đối chứng:** đo luồng đang hoạt động trước khi tạo lỗi; chỉ thay một biến mỗi lần.
7. **Có đường lui:** ghi rollback cụ thể và cách chứng minh dọn xong trước khi bắt đầu phần thay đổi.

Bộ công cụ tham chiếu, chỉ chuẩn bị khi đến bài cần dùng:

| Nhu cầu | Công cụ |
|---|---|
| Interface/route/neighbor/socket trên Linux | `ip`, `ss` từ iproute2 |
| Kết nối và ứng dụng | `ping`, `curl`, `nc`; kiểm tra bản netcat vì flags khác nhau giữa OS/implementation |
| DNS | `dig` hoặc `nslookup`, thêm resolver lookup của OS khi cần so sánh |
| Packet/path | `tcpdump`; `traceroute`/`tracepath` khi có và được phép, Wireshark tùy chọn để đọc pcap |
| Firewall | `nft` hoặc `iptables` theo backend thực tế, chỉ trong lab được phép thay đổi |
| Kubernetes | `kubectl`, `kind`, runtime cá nhân; debug image tương thích architecture/RBAC, `exec`/`debug` tùy quyền |

## 4. Ranh giới thao tác

- **Read-only không đồng nghĩa được phép ở mọi nơi:** VM công ty vẫn cần đúng quyền/chính sách; packet capture và xem log có thể chứa dữ liệu nhạy cảm.
- **Thay đổi lab cá nhân:** được đề xuất trong bài, nhưng người dạy phải xác nhận phạm vi với học viên trước khi chạy. Dùng tài nguyên có tên/nhãn riêng cho bài; lưu manifest gốc hoặc cấu hình trước thay đổi.
- **Thay route/interface/firewall khi đang SSH:** chỉ làm trên lab riêng, có console dự phòng, quyền được chấp thuận và rollback đã chuẩn bị. Nếu thiếu một điều kiện, dừng; không sửa mạng để “thử xem”.
- **VM công ty/cluster chia sẻ:** không tạo lỗi, không đổi CNI, không chặn port, không flush firewall. Thay đổi guest cũng phải được phê duyệt riêng; việc học không cấp quyền đó.
- **Hạ tầng ngoài guest:** không nhận quyền admin Ubuntu là quyền thay vSwitch/VLAN/address pool. Đưa yêu cầu qua nhóm VMware/network.
- **Không dùng `iptables -F`**, xóa toàn bộ nft ruleset, xóa route mặc định hoặc xóa hàng loạt namespace/cluster làm cách reset bài.
- Cài CNI hỗ trợ policy, Ingress Controller hoặc MetalLB phải là quyết định riêng trên cluster lab phù hợp. Không thay CNI tại chỗ chỉ để vượt một bài học.

## 5. Rollback và dọn dẹp

Mỗi lab có thay đổi phải ghi trong notes:

| Tài nguyên thay đổi | Trạng thái trước | Thay đổi có chủ đích | Cách hoàn nguyên chính xác | Phép đo sau hoàn nguyên |
|---|---|---|---|---|
| Chỉ điền tài nguyên bài này sở hữu | — | — | — | — |

- Với process HTTP/debug: lưu cách dừng đúng process và kiểm tra listener đã hết; không kill theo pattern rộng.
- Với netns/veth/bridge: ghi tên riêng của bài và tài nguyên nào nằm trong namespace nào; chỉ xóa phần bài tạo. Forwarding/firewall nên nằm trong router namespace của lab, không đổi global host để tiện làm.
- Với Kubernetes: lưu manifest và thay đổi của bài; chỉ xóa/khôi phục tài nguyên có ownership rõ. Không xóa namespace hoặc cluster nếu chưa xác nhận nó là disposable và không có tài nguyên người khác.
- Khi cần giữ lab cho bài sau, ghi rõ tài nguyên còn tồn tại, lý do giữ, người sở hữu và cách dọn cuối chuỗi; không coi đó là đã dọn.

## 6. Giới hạn cần ghi khi kết luận

- kind/OrbStack có lớp container/ảo hóa; NodePort, L2 announcements và external IP có thể không tới được từ mọi vị trí client.
- Service proxy, CNI, encapsulation, SNAT và MTU phụ thuộc triển khai thực tế. Không vẽ VXLAN, bridge hoặc DNAT thành sự thật chỉ vì đã thấy ở ví dụ.
- NetworkPolicy cần kiểm chứng traffic trước/sau; đối tượng được tạo thành công chưa chứng minh data plane thực thi.
- Nếu LoadBalancer L2 không chạy được trên topology hiện tại, dùng sơ đồ/trace mô phỏng theo bài 23b, ghi rõ chưa có kiểm chứng reachability thực tế. Không đưa địa chỉ từ pool doanh nghiệp vào lab.
- Config và ports Spark phải theo phiên bản/deploy mode thật. Chưa có Spark lab thì bài thực hành Spark vẫn bị chặn, không coi đọc log mẫu là đã chạy ứng dụng.

## 7. Tài liệu để đối chiếu theo phiên bản

Đây là điểm vào từ giáo trình, không phải cam kết mọi ví dụ trên trang áp dụng cho lab hiện tại. Khi dùng, lưu trang/phiên bản và ngày tra cứu trong notes.

- [Kubernetes networking](https://kubernetes.io/docs/concepts/services-networking/)
- [Service](https://kubernetes.io/docs/concepts/services-networking/service/) · [DNS](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/)
- [NetworkPolicy](https://kubernetes.io/docs/concepts/services-networking/network-policies/)
- [Debug Service](https://kubernetes.io/docs/tasks/debug/debug-application/debug-service/) · [Debug DNS](https://kubernetes.io/docs/tasks/administer-cluster/dns-debugging-resolution/)
- [kubectl debug](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_debug/)
- [kind quick start](https://kind.sigs.k8s.io/docs/user/quick-start/) · [kind configuration](https://kind.sigs.k8s.io/docs/user/configuration/)
- [ip-netns manual](https://man7.org/linux/man-pages/man8/ip-netns.8.html) · [ip-route manual](https://man7.org/linux/man-pages/man8/ip-route.8.html)
- [tcpdump manual](https://www.tcpdump.org/manpages/tcpdump.1.html)
- [Spark documentation](https://spark.apache.org/documentation.html) — chọn đúng release trước khi lấy cấu hình.
