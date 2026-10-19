# 16a — CNI triage bằng trạng thái, events và logs

**Buổi gốc:** 16 · **Thời lượng:** 30–45 phút.  
**Trước bài:** [15b](15b-cross-node-evidence-va-fault.md).  
**Môi trường:** cluster lab; đọc Pod/node/events và logs thành phần CNI theo RBAC thực tế.

## Mục tiêu

- Phân biệt lỗi thiết lập Pod network với lỗi truyền traffic sau khi Pod đã có IP.
- Tổ chức bằng chứng control plane metadata và data plane theo thời gian.

## Mô hình cần hiểu

Events và component logs giúp biết bước setup/address allocation nào thất bại, nhưng “Pod Running” không chứng minh đường truyền ứng dụng hoạt động. Pod/node addresses, placement và allocation config phải được đọc cùng nhau. Không có một tên DaemonSet hoặc đường dẫn CNI log cố định đúng cho mọi cluster.

## Dự đoán trước khi chạy

1. Pod chưa có IP và Pod có IP nhưng cross-node timeout cần những nhóm chứng cứ khác nhau nào?
2. Log lỗi từ hôm qua có đủ để giải thích request lỗi hiện tại không?

## Challenge

1. Liệt kê thành phần CNI thật và cách lấy logs/events trong quyền hiện có; ghi version/config đã xác minh.
2. Với một case lab đã chuẩn bị, thu thập Pod status, IP, node placement, events và logs giới hạn theo thời gian lỗi.
3. Gắn mỗi bằng chứng vào tầng: scheduling/runtime setup, address allocation, Pod path, node path hoặc ứng dụng.
4. Viết cây quyết định ngắn: khi nào kiểm tra listener, khi nào kiểm tra allocation/setup, khi nào chuyển sang underlay/cross-node path.

## Kiểm chứng và đối chứng

Không restart DaemonSet để “làm mới log”. Nếu không đọc được một nguồn do RBAC, ghi thiếu quyền và phép đo thay thế; không coi sự vắng mặt của log được cấp quyền hạn chế là bằng chứng không có lỗi.

## Bằng chứng và điều kiện đạt

- [ ] Có tập events/logs/metadata gắn thời điểm và nguồn, không chỉ ảnh Pod status.
- [ ] Phân loại được setup failure và traffic failure, có giả thuyết kiểm chứng tiếp.
- [ ] Có cây triage dựa trên CNI thật và [notes](../templates/LESSON_NOTE.md), không thay thành phần cluster ngoài phạm vi.

**Tiếp theo:** [16b — Cross-node incident và CP4](16b-cross-node-incident-va-cp4.md).
