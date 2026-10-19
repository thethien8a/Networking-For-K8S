# Các cổng đánh giá

Giữ nguyên phạm vi CP1–CP6 và Final của giáo trình gốc. Kết quả lưu ở [PROGRESS.md](PROGRESS.md), bằng chứng lưu trong `notes/`. Mỗi tiêu chí dưới đây là bắt buộc, không lấy điểm mạnh bù cho phần chưa kiểm chứng.

LLM giao đề trước, chưa nêu nguyên nhân hoặc đáp án. Với lỗi ẩn, chuẩn bị fault trong lab riêng hoặc nhờ người khác tạo; học viên làm một biến thể chưa biết, rồi người dạy xác nhận root cause bằng cấu hình/quan sát. Không dùng bài đã chép lời giải làm bằng chứng duy nhất.

## CP1 — sau 04b

**Đề:** nhận một IP plan 4 host và routing table có route chồng lấn. Phân loại đích cùng subnet/khác subnet, dự đoán interface/next hop cho 5 đích, rồi xử lý một lỗi route trong lab.

- [ ] Tính đúng network/prefix và membership, không suy từ vài octet “giống nhau”.
- [ ] Giải thích longest-prefix match, next hop và MAC cần tìm là của đích hay gateway.
- [ ] Có đối chiếu dự đoán với `ip route get` và bằng chứng ARP/neighbor phù hợp.
- [ ] Nêu ít nhất 3 lý do hai IP hợp lệ vẫn không liên lạc; tìm đúng lỗi của case, sửa và kiểm tra lại.

## CP2 — sau 08b

**Đề:** phân loại các tình huống DNS failure, TCP failure, HTTP error và TLS validation failure; giải thích một luồng port mapping/NAT trong topology cụ thể.

- [ ] Mỗi kết luận có output/capture cụ thể; không kết luận chỉ từ một chuỗi lỗi của client.
- [ ] Phân biệt connect timeout với application timeout, refused/reset với drop theo bằng chứng.
- [ ] Phân biệt hostname dùng cho DNS, HTTP Host, TLS SNI và xác thực chứng chỉ.
- [ ] Theo dõi source/destination IP và port trước/sau SNAT hoặc DNAT, không gọi NAT là firewall.

## CP3 — sau 12b

**Đề:** dựng lại hai subnet nối qua router namespace; xử lý một lỗi routing và một lỗi firewall trong lab riêng.

- [ ] Có topology, addresses, routes hai chiều và forwarding đúng phạm vi.
- [ ] Có kết nối thành công thực tế và capture xác nhận đường đi; không thay bằng sơ đồ lý thuyết.
- [ ] Khoanh vùng đúng route chiều về/forwarding và chain/hook firewall liên quan.
- [ ] Phân biệt DROP và REJECT bằng thực nghiệm có đối chứng.
- [ ] Có lệnh rollback/dọn dẹp cụ thể, đã chạy và đo sau hoàn nguyên; không làm mất mạng host.

## CP4 — sau 16b

**Đề:** điều tra case cùng node chạy được, khác node lỗi; nguyên nhân không được công bố trước.

- [ ] Chứng minh placement và baseline same-node/cross-node, không chỉ suy từ tên Pod.
- [ ] Vẽ đường đi theo CNI/data plane thật, ghi phần nào chưa quan sát được.
- [ ] Dùng events/logs/route/capture thích hợp để xác định tầng lỗi và kiểm tra giả thuyết đối lập.
- [ ] Có root cause được kiểm chứng, post-fix checks và rollback phạm vi lab.
- [ ] Giải thích ít nhất hai giới hạn khi suy từ kind sang 4 VM VMware.

## CP5 — sau 20b

**Đề:** 3 mini-incident độc lập bao phủ selector mismatch, targetPort/listener mismatch và DNS resolution. Giao từng triệu chứng, không gắn nhãn nguyên nhân trước khi học viên điều tra.

- [ ] Có đủ 3 [incident reports](templates/INCIDENT_REPORT.md), không gộp thành một lần “restart hết”.
- [ ] Mỗi báo cáo có triệu chứng, dự đoán, bằng chứng, root cause, thay đổi tối thiểu và post-fix check.
- [ ] Phân biệt metadata Service/EndpointSlice với khả năng kết nối thật.
- [ ] DNS case lần được resolver → DNS Service/endpoints → tên cần tra, và có control qua IP phù hợp.
- [ ] Fix không làm hỏng luồng đang hoạt động; tài nguyên thí nghiệm được hoàn nguyên hoặc bàn giao rõ.

## CP6 — sau 24b

**Đề:** trình bày external access qua Ingress/LoadBalancer và chứng minh ma trận NetworkPolicy ingress/egress.

- [ ] Ingress có hai backend, Host/path tests và bài kiểm chứng TLS hostname/trust; phân biệt resource với controller.
- [ ] LoadBalancer có topology/client location và giải thích cấp IP khác với quảng bá/reachability. Nếu lab không hỗ trợ, chấp nhận phân tích mô phỏng kèm lý do và giới hạn, **không ghi như test thực tế đã pass**.
- [ ] Dùng CNI hỗ trợ policy, có baseline và traffic thực tế được phép/bị chặn sau policy.
- [ ] DNS egress hoạt động khi chỉ mở luồng cần thiết; không mở toàn bộ egress để che lỗi.
- [ ] Có kiểm tra cả frontend hợp lệ và client không hợp lệ, namespace/selector đúng ý định, và rollback.

## Final — sau 28b

**Đề:** nộp Network Readiness Report cho 1 control-plane + 3 worker VM và bảo vệ một case Spark networking đã thực hành.

- [ ] Có [report](templates/NETWORK_READINESS_REPORT.md) đủ topology, connectivity matrix, address plan và kiểm tra overlap.
- [ ] Mô tả Pod↔Pod, Pod↔Service, Pod→external, external→Ingress/LoadBalancer, DNS và dependency.
- [ ] Firewall/ports/CNI/Spark config dựa trên version và lựa chọn thực tế, có nguồn; không điền giả định thành kết quả đã xác minh.
- [ ] Phân chia guest OS với VMware/network team; đề xuất phép thử và escalation khả thi trong quyền hiện có.
- [ ] Có case Spark driver/executor cross-node từ bài 27, phân biệt được network với application/resource failure, có bằng chứng trước/sau.
- [ ] Người khác có thể dùng runbook để kiểm tra mà không cần đoán máy chạy lệnh, expected observation hoặc rollback.
- [ ] Những phần chưa được phép kiểm tra ở công ty được ghi là unknown/risk với người phụ trách và bước xác minh; không yêu cầu triển khai hoặc thay đổi hạ tầng công ty để qua khóa.

## Nếu chưa đạt

Ghi chính xác tiêu chí thiếu, bài cần quay lại và một phép thử để làm lại. Chuyển cổng sang `can-on` hoặc `bi-chan`, không xóa lịch sử lần trước. Lần đánh giá lại dùng biến thể khác ở phần chưa vững; không bắt làm lại mọi thứ nếu bằng chứng còn hợp lệ.
