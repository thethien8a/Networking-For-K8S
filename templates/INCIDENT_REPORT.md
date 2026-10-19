# [Case ID] — [Triệu chứng, chưa ghi sẵn nguyên nhân]

- Ngày/giờ, người điều tra: [...]
- Bài học / môi trường / phiên bản: [...]
- Phạm vi lab và quyền được xác nhận: [...]

## 1. Triệu chứng và ảnh hưởng

[Nguồn → đích → protocol/port; phần nào lỗi, phần nào vẫn chạy; cách tái hiện]

## 2. Topology và baseline

[Pod/node/namespace/IP/DNS/listener liên quan; phân biệt quan sát với giả định]

## 3. Timeline điều tra

| Thời điểm | Giả thuyết / dự đoán | Phép đo và nơi chạy | Output thật / bằng chứng | Kết luận / bước tiếp |
|---|---|---|---|---|
| [time] | [hypothesis] | [command + context] | [evidence] | [decision] |

## 4. Root cause đã kiểm chứng

[Cơ chế gây lỗi; bằng chứng xác nhận; tại sao không phải giả thuyết đối lập. Nếu chưa đủ bằng chứng, ghi chưa xác định.]

## 5. Fix tối thiểu và rollback

[Thay đổi đúng một biến; cấu hình trước/sau; cách và điều kiện hoàn nguyên]

## 6. Post-fix và regression checks

| Luồng kiểm tra | Trước fix | Sau fix | Bằng chứng | Ý nghĩa |
|---|---|---|---|---|
| [luồng từng lỗi] | [result] | [result] | [output] | [reason] |
| [control vốn chạy] | [result] | [result] | [output] | [reason] |

## 7. Dọn dẹp và bàn giao

[Tài nguyên/rules/fault đã hoàn nguyên; phần còn giữ và owner; kiểm tra trạng thái cuối]

## 8. Bài học và điểm chưa rõ

[Tín hiệu phát hiện sớm, phép đo hiệu quả nhất, điều chưa được phép/không thể xác minh, việc cần phối hợp]
