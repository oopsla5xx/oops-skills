---
name: review-pr
description: Review PR diff against the spec before shipping — check spec compliance, conventions, scope, security basics, and test coverage. Outputs a structured verdict (APPROVE / REQUEST CHANGES / COMMENT). Run after self-check, before Phase 4 ship.
---

# Review PR

Review diff của PR dựa trên spec đã viết. Không phải generic "code review" — mà là kiểm tra code có đúng với những gì đã cam kết trong spec không.

---

## Run

### Bước 1 — Đọc context

```bash
# Xem toàn bộ diff đang chuẩn bị ship
git diff main...HEAD    # hoặc git diff <base-branch>...HEAD
```

Cùng lúc đọc:
- `.ai/tasks/<tên-task>.md` — spec (Goal, Scope, Out of scope, Constraints, Plan)
- `.ai/context/conventions.md` — quy tắc code của project

### Bước 2 — Review theo 5 chiều

Đánh giá từng chiều, ghi findings theo format `[BLOCKING]` hoặc `[MINOR]`:

---

**A. Spec compliance**

- [ ] Code đạt được Goal trong spec chưa?
- [ ] Có thay đổi nào nằm ngoài `## Scope` không?
- [ ] Có thay đổi nào vi phạm `## Out of scope` không?
- [ ] Các `## Constraints` được tôn trọng không?
- [ ] Tất cả bước trong `## Plan` đã được implement chưa?

`[BLOCKING]`: bất kỳ item nào trong Out of scope bị vi phạm, hoặc Goal không đạt.
`[MINOR]`: thiếu 1 step nhỏ trong Plan nhưng không ảnh hưởng Goal.

---

**B. Conventions**

So sánh từng thay đổi với `.ai/context/conventions.md`:

- [ ] Error handling đúng pattern chưa? (không nuốt lỗi âm thầm)
- [ ] Naming đúng convention không?
- [ ] Không có hardcoded value nào không?
- [ ] Không có code nào copy-paste gây duplicate logic?

`[BLOCKING]`: vi phạm convention cứng (ví dụ: nuốt lỗi trong production path).
`[MINOR]`: naming hơi khác style, có thể fix sau.

---

**C. Scope creep**

```bash
# Kiểm tra file nào bị touch ngoài scope đã định
git diff --name-only main...HEAD
```

So sánh danh sách file thay đổi với `## Scope` trong spec.

- [ ] Có file nào bị sửa không được đề cập trong Scope không?
- [ ] Nếu có — thay đổi đó có justified không? (bugfix phát sinh, refactor bắt buộc)

`[BLOCKING]`: thay đổi không liên quan làm tăng risk không cần thiết.
`[MINOR]`: cleanup nhỏ trong file liên quan.

---

**D. Test coverage**

- [ ] Mỗi item trong `## Test plan` của spec có test tương ứng không?
- [ ] Test cover happy path?
- [ ] Test cover ít nhất 1 edge case?
- [ ] Không có test nào bị xóa mà không có lý do rõ ràng?

```bash
# Xem test files thay đổi
git diff --name-only main...HEAD | grep -E "test|spec"
```

`[BLOCKING]`: happy path hoàn toàn không có test.
`[MINOR]`: thiếu một số edge case nhỏ.

---

**E. Security basics** (stack-agnostic)

- [ ] Input từ user/external được validate trước khi dùng không?
- [ ] Không có secret/credential nào bị hardcode không?
- [ ] Không có SQL string concatenation nào không? (dùng parameterized query)
- [ ] Không có output nào expose stack trace hoặc internal error detail ra user không?
- [ ] Nếu có file upload — có validate type/size không?

`[BLOCKING]`: bất kỳ issue security nào trong list trên.

---

### Bước 3 — Tổng hợp verdict

**Format output:**

```markdown
## PR Review: <tên-task>

**Verdict:** ✅ APPROVE | 🔄 REQUEST CHANGES | 💬 COMMENT ONLY

---

### Blocking issues (phải fix trước khi merge)
<!-- Chỉ có nếu verdict là REQUEST CHANGES -->
- [BLOCKING] <chiều> — <mô tả cụ thể> | <file:line nếu có>

### Minor issues (có thể fix trong PR tiếp)
- [MINOR] <chiều> — <mô tả>

### Notes
<!-- Quan sát không phải issue, hoặc ghi nhận điểm tốt -->
-

---
Spec: `.ai/tasks/<tên-task>.md`
Diff: <số files thay đổi> files, +<dòng thêm>/-<dòng xóa>
```

**Nguyên tắc verdict:**
- `✅ APPROVE` — không có blocking issue
- `🔄 REQUEST CHANGES` — có ≥1 blocking issue, phải fix trước khi merge
- `💬 COMMENT ONLY` — không có blocking nhưng có điều đáng ghi nhận

### Bước 4 — Nếu có blocking issue

Không tự sửa. Report rõ ràng, để agent/người implement quyết định:
- Sửa trong PR hiện tại → re-run review sau khi fix
- Tạo follow-up task cho minor issue → note vào `.ai/tasks/` hoặc tạo issue

---

## Những gì KHÔNG review ở đây

- Style preference không có trong conventions.md → không flag
- Architectural opinion nằm ngoài scope task → không flag (đó là việc của ADR)
- Performance chưa là vấn đề được đo → không flag
- Test coverage % tuyệt đối → chỉ xem xét test plan items, không đòi 100%
