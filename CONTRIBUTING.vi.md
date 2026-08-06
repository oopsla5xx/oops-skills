# Đóng góp cho oops-skills

Cảm ơn bạn đã quan tâm đóng góp. Tài liệu này giải thích cách bắt đầu và những gì chúng tôi mong đợi từ các đóng góp.

**[English](CONTRIBUTING.md)**

---

## Bắt đầu

1. **Fork** repository và clone fork của bạn.
2. Tạo **feature branch** từ `main`:
   ```bash
   git checkout -b feat/ten-tinh-nang-cua-ban
   ```
3. Thực hiện thay đổi, tuân theo các quy ước bên dưới.
4. Mở **Pull Request** vào `main` với mô tả rõ ràng.

---

## Những gì chúng tôi chào đón

- Sửa lỗi và chỉnh sửa trong các file workflow
- Script skill mới trong `.claude/skills/`
- Cải tiến template `.ai/context/`
- Cải thiện tài liệu và bản dịch
- Hỗ trợ adapter mới (Cursor, Copilot, v.v.)

## Những gì chúng tôi sẽ không merge

- Thay đổi vi phạm nguyên tắc single-source-of-truth
- Skill hardcode giá trị riêng của dự án thay vì dùng tham chiếu `.ai/`
- File workflow bỏ qua yêu cầu self-check Phase 3
- Dependency mới thêm vào mà không có ADR tương ứng trong `.ai/decisions/`

---

## Quy ước

Tuân theo `.ai/context/conventions.md` — đó là style guide chính thức cho dự án này. Các điểm chính:

- Giữ skill script ngắn gọn, tập trung vào một trách nhiệm duy nhất
- Dùng liên kết tương đối trong tài liệu `.ai/`; tránh URL tuyệt đối
- Chỉ viết comment khi *lý do* không hiển nhiên
- Không để lại comment TODO trong code đã submit

---

## Checklist Pull Request

Trước khi yêu cầu review, xác nhận:

```
[ ] Tests pass (nếu có)
[ ] Lint sạch
[ ] Tuân thủ conventions (đọc lại .ai/context/conventions.md)
[ ] Phạm vi gọn — không có thay đổi ngoài brief
[ ] Không có side effect ngoài ý muốn ở các file khác
[ ] Đã hoàn thành security checklist trong .ai/context/security-checklist.md
[ ] Tài liệu đã cập nhật nếu thay đổi ảnh hưởng đến chúng
```

---

## Báo cáo lỗi

Mở GitHub Issue với:

- **Điều bạn mong đợi** xảy ra
- **Điều thực sự xảy ra**
- Các bước tái hiện
- Môi trường của bạn (OS, shell, phiên bản Claude Code / Cursor nếu liên quan)

---

## Quy tắc ứng xử

Tôn trọng lẫn nhau. Giả định thiện chí. Tập trung phản hồi vào code, không phải con người.
