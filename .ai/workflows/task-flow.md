# Task Flow

Mọi task đều đi qua 4 phase này. Không skip phase, không merge khi chưa xong self-check.

---

## Phase 1 — Brief

**Trước khi viết một dòng code:**

1. Chạy `/clarify-scope` — quyết định hỏi hay giả định và tiến hành
2. Chạy `/write-spec` — chuyển context thảo luận thành file `.ai/tasks/<tên-task>.md`
3. Chạy `/plan-tasks` — chia spec thành bước implement có thứ tự, append `## Plan` vào spec file

Nếu task nhỏ, rõ ràng (clarify-scope đánh giá "làm luôn") → bỏ qua write-spec và plan-tasks, tiến thẳng sang Phase 2.

---

## Phase 2 — Implement

Chạy `/implement-tdd` — lặp chu trình Red → Green → Refactor cho từng bước trong `## Plan`.

Nếu task nhỏ (không có spec/plan): đọc `.ai/context/conventions.md`, confirm baseline test xanh, rồi code trực tiếp.

**Không bao giờ:**
- Viết implementation trước khi có failing test (nếu đang dùng TDD)
- Refactor code ngoài scope bước đang làm
- Sửa test để pass thay vì sửa code
- Thêm dependency mới mà không ghi vào `.ai/decisions/`

---

## Phase 3 — Self-check

1. Chạy `/write-test-scenarios` — tạo file UC/TC tại `.ai/tasks/<tên-task>-manual-tests.md` để user test thủ công

2. **Tự kiểm tra trước khi tuyên bố "done":**

```
[ ] test: toàn bộ test suite xanh
[ ] lint: không có lỗi mới
[ ] typecheck: không có lỗi mới (nếu project có)
[ ] conventions: re-read thay đổi, không có vi phạm
[ ] scope: không có code ngoài phạm vi brief
[ ] side effects: không có thay đổi ngoài ý muốn ở file khác
[ ] docs: nếu thay đổi ảnh hưởng docs → đã cập nhật
[ ] manual-tests: file UC/TC đã tạo và cover toàn bộ Test plan
```

Chỉ khi tất cả check xanh mới chuyển sang Phase 4.

---

## Phase 4 — Ship

1. Chạy `/review-pr` — verdict phải là ✅ APPROVE trước khi tiếp tục
2. Chạy `/create-pr-description` — điền template từ `.github/PULL_REQUEST_TEMPLATE.md`
3. Nếu có quyết định kỹ thuật đáng ghi lại → tạo file trong `.ai/decisions/`
4. Cập nhật `.ai/status.md`:
   - Xóa task khỏi "In Progress"
   - Thêm vào "Recently Completed" với notes ngắn
5. Xóa file `.ai/tasks/<tên-task>.md` và `<tên-task>-manual-tests.md`
