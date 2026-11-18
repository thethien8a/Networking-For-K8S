# Incident Report: Spark Executor Cross-Node Connection Refused to Driver

- Ngày/giờ (Asia/Ho_Chi_Minh): 2026-11-18 10:35
- Người điều tra: Học viên & Antigravity (LLM Gia sư)
- Bài học / môi trường / phiên bản: Bài 27b / k3d v5.8.3, K3s v1.33.6+k3s1, Spark 3.5.3 (Cluster Mode) / macOS arm64
- Phạm vi lab và quyền được xác nhận: Namespace `nfk-27a`, ServiceAccount `spark`, Role `spark-role`, NetworkPolicy `spark-driver-policy`

---

## 1. Triệu chứng và ảnh hưởng

- **Nguồn:** Spark Executor Pods (`spark-pi-8069b1a1ecace193-exec-1`..`exec-5`) chạy trên worker node `k3d-de-k3d-agent-1` (IP `10.42.1.86`..`10.42.1.90`).
- **Đích:** Spark Driver Pod (`spark-pi-incident-9621e8a1ecacd939-driver`) chạy trên worker node `k3d-de-k3d-agent-0` (IP `10.42.3.91`).
- **Protocol / Port:** TCP Port `7078` (Driver RPC listener) và Headless Service FQDN `spark-pi-incident-9621e8a1ecacd939-driver-svc.nfk-27a.svc`.
- **Ảnh hưởng:** 
  - Executor pods liên tục văng lỗi `java.net.ConnectException: Connection refused` và thoát với status `Error`.
  - Spark Driver Pod thử request lại 5 lần, sau đó đạt giới hạn `Max number of executor failures (3) reached`, báo lỗi `IllegalStateException: Spark context stopped while waiting for backend` và kết thúc với status `Error`.
  - Job Spark tính toán số Pi thất bại hoàn toàn.

---

## 2. Topology và baseline

```
+----------------------------------------------------------------------------------------------------------+
|                                         K3D CLUSTER (k3d-de-k3d)                                         |
|                                                                                                          |
|   [Node: agent-0 (192.168.155.3)]                        [Node: agent-1 (192.168.155.4)]                 |
|   PodCIDR: 10.42.3.0/24                                  PodCIDR: 10.42.1.0/24                           |
|                                                                                                          |
|   +---------------------------------------------+        +--------------------------------------------+  |
|   | Spark Driver Pod:                           |        | Spark Executor Pod:                        |  |
|   | IP: 10.42.3.91                              |        | IP: 10.42.1.86                             |  |
|   | - sparkDriver RPC: TCP 7078 (LISTEN)        |        | - Tự resolve FQDN -> 10.42.3.91            |  |
|   | - SparkUI: TCP 4040 (LISTEN)                |        | - Gửi TCP SYN tới 10.42.3.91:7078          |  |
|   | - NettyBlockTransfer: TCP 7079 (LISTEN)     |        +--------------------------------------------+  |
|   +---------------------------------------------+                           |                            |
|             ^                                                               |                            |
|             |                                                               |                            |
|             +-----[ BLOCKED by NetworkPolicy ]<==== Flannel VXLAN UDP 8472 =+                            |
|                   (Only port 4040 allowed)                                                               |
+----------------------------------------------------------------------------------------------------------+
```

---

## 3. Timeline điều tra

| Thời điểm | Giả thuyết / dự đoán | Phép đo và nơi chạy | Output thật / bằng chứng | Kết luận / bước tiếp |
|---|---|---|---|---|
| 10:28 | Job fail: xác nhận placement cross-node và status pod | `kubectl -n nfk-27a get pods -o wide` trên Mac | Driver `10.42.3.91` (agent-0, Error); 5 Exec `10.42.1.86-90` (agent-1, Error); Runner Completed | Driver và Executor thực sự chạy cross-node. Loại trừ giả thuyết same-node scheduler. |
| 10:29 | Executor không đăng ký được với Driver: phân biệt DNS vs TCP Refused vs OOM | `kubectl -n nfk-27a logs <exec-1> --tail=30` trên Mac | `Failed to connect to spark-pi-incident-...-driver-svc.nfk-27a.svc/10.42.3.91:7078: Connection refused` | FQDN đã resolve thành công ra `10.42.3.91` (DNS OK). TCP handshake bị từ chối bằng RST/REJECT. Không có OOM. |
| 10:30 | Driver chưa mở port 7078 hay bị firewall/policy chặn | `kubectl -n nfk-27a logs <driver> \| grep -E "sparkDriver\|7078"` | `INFO Utils: Successfully started service 'sparkDriver' on port 7078` | Driver process đã listen port 7078 thành công. Vấn đề nằm ở network filtering. |
| 10:31 | Có NetworkPolicy can thiệp vào Ingress của Driver | `kubectl -n nfk-27a get networkpolicy`, `describe` | Policy `spark-driver-policy` chọn `spark-role: driver`, Ingress chỉ cho phép port `4040/TCP` | **Root Cause xác nhận:** NetworkPolicy vô tình chặn port 7078 (RPC) và 7079 (BlockManager). |

---

## 4. Root cause đã kiểm chứng

- **Cơ chế gây lỗi:**
  - Kube-router (NetworkPolicy controller trong K3s) thực thi chính sách `spark-driver-policy` chọn label `spark-role: driver`.
  - Ingress spec chỉ khai báo port `4040/TCP`. Theo cơ chế mặc định của Kubernetes NetworkPolicy, một khi Pod đã được chọn bởi Ingress policy, mọi Ingress traffic tới các port khác (ngoài 4040) đều bị **Default Deny**.
  - Kube-router cấu hình iptables gửi phản hồi `REJECT icmp-port-unreachable` (hoặc TCP RST) khi các Pods ngoài (Executor ở `agent-1`) gửi TCP SYN tới `10.42.3.91:7078`.
  - Java Netty client trong JVM của Executor nhận được RST, ném ngoại lệ `java.net.ConnectException: Connection refused`.
- **Bằng chứng loại trừ:**
  - *DNS:* Tên miền Headless Service đã phân giải ra đúng `10.42.3.91` ngay trong log của Netty.
  - *Driver Process:* Log Driver khẳng định `Started service 'sparkDriver' on port 7078`.
  - *OOM / Runtime:* Pod Executor không bị exit code 137 hay `OOMKilled`, mà kết thúc do uncaught `SparkException`.

---

## 5. Fix tối thiểu và rollback

- **Nguyên tắc:** Least Privilege cho Spark Driver. Cần mở cả port `7078` (Driver RPC) và `7079` (BlockManager data transfer), đồng thời duy trì port `4040` (Web UI).
- **Patch NetworkPolicy:**
  ```yaml
  apiVersion: networking.k8s.io/v1
  kind: NetworkPolicy
  metadata:
    name: spark-driver-policy
    namespace: nfk-27a
  spec:
    podSelector:
      matchLabels:
        spark-role: driver
    policyTypes:
    - Ingress
    ingress:
    - ports:
      - protocol: TCP
        port: 4040   # Spark Web UI
      - protocol: TCP
        port: 7078   # Driver RPC (Executor Registration)
      - protocol: TCP
        port: 7079   # Driver BlockManager (Data & Broadcast)
  ```
- **Rollback:** Xóa hoặc rollback NetworkPolicy về phiên bản chỉ có port 4040 nếu cần tái hiện lỗi.

---

## 6. Post-fix và regression checks

| Luồng kiểm tra | Trước fix | Sau fix | Bằng chứng | Ý nghĩa |
|---|---|---|---|---|
| Executor đăng ký Driver (RPC 7078) | Fail `Connection refused` | Thành công | Log Driver: `Registered executor NettyRpcEndpointRef ... (10.42.1.92:45084) with ID 1` | Port 7078 đã thông suốt qua NetworkPolicy |
| BlockManager trao đổi khối dữ liệu (7079) | Không kích hoạt được | Thành công | Log Driver: `Added broadcast_0_piece0 in memory on spark-pi-fixed-...-driver-svc...:7079` và trên `10.42.1.92:40301` | Luồng truyền khối dữ liệu phân tán hai chiều hoạt động bình thường |
| Kết quả tính toán của Job Spark | Fail với `IllegalStateException` | Hoàn thành | `Pi is roughly 3.1416989428339788` | Job kết thúc thành công với status `Completed` (Exit code 0) |

---

## 7. Dọn dẹp và bàn giao

- Toàn bộ namespace `nfk-27a` (gồm pods, NetworkPolicy, RBAC, ServiceAccount) được xóa sạch sẽ sau khi hoàn thành bài học.
- Trạng thái cluster trở về sạch sẽ, không còn tàn dư cấu hình mạng tạm.

---

## 8. Bài học và Playbook đúc kết

1. **Chuỗi chẩn đoán kết nối Spark on K8s:**
   `Kubernetes Pod/Events` -> `DNS Headless Service` -> `RPC Port 7078 (Driver)` -> `BlockManager Port 7079 (Driver & Executor)`.
2. **Gotcha về NetworkPolicy với Spark:**
   Rất nhiều SecOps chỉ mở Web UI (4040) khi thiết lập NetworkPolicy cho Spark Driver vì không hiểu kiến trúc nội tại của Spark. Bắt buộc phải whitelist cả `7078` và `7079` cho nội bộ cluster/namespace.
3. **Phân biệt OOM vs Network Refused:**
   Kiểm tra `exitCode: 137` và `Reason: OOMKilled` trước khi nhảy vào bắt gói CNI để tránh chẩn đoán nhầm.
