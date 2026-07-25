---
name: write-spec
description: Convert clarify-scope discussion into a structured spec file at .ai/tasks/<name>.md. Run after clarify-scope resolves assumptions and scope. Captures decisions before they're lost in conversation history.
---

# Write Spec

Chuyển context đã thảo luận (assumptions, câu hỏi đã giải quyết, scope đã xác định) thành file spec có cấu trúc tại `.ai/tasks/<tên-task>.md`.

Chạy ngay sau khi `clarify-scope` đã xong — đừng để context trôi mất trong lịch sử chat.

---

## Run

### Bước 1 — Đặt tên task

Tên phải: ngắn, dùng kebab-case, mô tả hành động cụ thể.

```
# Tốt
add-password-reset
refactor-auth-middleware
fix-order-status-race-condition

# Không tốt
feature          ← quá chung
fix-bug          ← không biết bug gì
update-stuff     ← vô nghĩa
```

### Bước 2 — Điền từng section từ context cuộc thảo luận

Mở `.ai/tasks/TEMPLATE.md` làm tham chiếu, tạo file mới `.ai/tasks/<tên-task>.md`.

**Goal** — lấy từ mục tiêu đã thảo luận. Phải đáp ứng cả hai:
- Specific: biết chính xác phải làm gì
- Measurable: biết khi nào thì "xong"

Ví dụ tệ: "Cải thiện hệ thống auth"
Ví dụ tốt: "User có thể reset password qua email, nhận link trong 60s, link hết hạn sau 1h"

**Scope** — liệt kê file/module cụ thể từ assumptions đã confirm:
```
- src/auth/password-reset.ts (tạo mới)
- src/api/routes/auth.ts (thêm endpoint)
- src/email/templates/ (thêm template)
```

**Out of scope** — lấy từ những gì đã nói KHÔNG làm, hoặc tự suy ra từ scope boundary. Đây là phần quan trọng nhất để tránh scope creep sau này.

**Test plan** — tối thiểu 3 case:
- Happy path (flow chính hoạt động đúng)
- Edge case (input biên, concurrent request...)
- Không làm hỏng gì đang hoạt động (regression)

**Constraints** — từ câu hỏi critical đã được trả lời trong clarify-scope. Nếu user đã nói "backward-compatible" hay "không được invalidate session cũ" → ghi vào đây.

**Open questions** — nếu vẫn còn câu hỏi chưa giải quyết sau clarify-scope, ghi vào đây với tag `[BLOCKING]` nếu cần trả lời trước khi code.

### Bước 3 — Kiểm tra chất lượng spec

Trước khi lưu, tự hỏi:

```
[ ] Goal: đọc xong biết ngay "xong" trông như thế nào không?
[ ] Out of scope: nếu ai đó muốn thêm X vào scope, có thể từ chối bằng file này không?
[ ] Test plan: có ít nhất 1 case "không làm hỏng cái đang chạy" không?
[ ] Constraints: câu hỏi critical từ clarify-scope đã được capture chưa?
```

Nếu bất kỳ checkbox nào là NO → sửa trước khi lưu.

### Bước 4 — Cập nhật status

Sau khi tạo spec:
1. Cập nhật `.ai/status.md` — thêm task vào "In Progress" với ngày bắt đầu
2. Confirm với user: "Spec đã tạo tại `.ai/tasks/<tên-task>.md`. Tiến hành implement?"

---

## Khi nào KHÔNG chạy skill này

- Task nhỏ, rõ ràng (theo đánh giá của clarify-scope) → không cần spec, làm luôn
- Spec đã tồn tại trong `.ai/tasks/` → cập nhật file đó thay vì tạo mới
