---
name: create-pr-description
description: Generate a pull request description by filling in the .github/PULL_REQUEST_TEMPLATE.md from spec, git diff, and commit history. Outputs ready-to-use text or creates PR via gh CLI. Run as part of Phase 4 ship.
---

# Create PR Description

Điền PR template từ `.github/PULL_REQUEST_TEMPLATE.md` dựa trên spec và git history. Output sẵn sàng paste vào GitHub hoặc dùng với `gh pr create`.

---

## Run

### Bước 1 — Thu thập context

Chạy tất cả song song:

```bash
# Template
cat .github/PULL_REQUEST_TEMPLATE.md 2>/dev/null \
  || cat .github/pull_request_template.md 2>/dev/null \
  || echo "(no template found)"

# Git summary
git log main...HEAD --oneline
git diff --stat main...HEAD
```

Đọc spec: `.ai/tasks/<tên-task>.md` (Goal, Scope, Test plan, Constraints)

### Bước 2 — Map từng section của template

Với mỗi section trong template, lấy nội dung từ nguồn tương ứng:

| Section | Lấy từ |
|---|---|
| **What** | Goal trong spec — 1-2 câu súc tích |
| **Why** | Lý do từ cuộc thảo luận clarify-scope, hoặc Constraints trong spec |
| **Changes** | `git diff --stat` + Scope trong spec |
| **Test plan** | Test plan items trong spec (convert sang checklist) |
| **Manual test guide** | Link đến `<tên-task>-manual-tests.md` nếu có |
| **Checklist** | Điền lệnh test thật từ `.ai/commands.md` |
| **Notes for reviewer** | Constraints, trade-off, hoặc gotcha quan trọng từ spec |

### Bước 3 — Viết description

Quy tắc:

**What:** Mô tả WHAT, không phải HOW. Đọc xong biết ngay feature/fix là gì.
```
# Tốt
Add password reset via email — users can request a reset link that expires in 1 hour.

# Tệ
Implement the reset token logic and add the API endpoints for password reset flow.
```

**Why:** Lý do business hoặc technical. Không giải thích lại WHAT.
```
# Tốt
Required by auth security audit — tokens previously had no expiry.

# Tệ
Because we need to allow users to reset their password.
```

**Changes:** Dùng `git diff --stat` để lấy danh sách file, thêm 1 dòng context cho file quan trọng.
```
- `src/auth/reset-token.ts` (new) — token generation and validation
- `src/api/routes/auth.ts` — 2 new endpoints: request + confirm
- `src/email/templates/` — reset-password email template
```

**Notes for reviewer:** Chỉ ghi những gì reviewer KHÔNG thể biết từ code:
- Trade-off đã cân nhắc
- Quyết định bỏ qua một approach nào đó và lý do
- Known limitation hoặc follow-up cần làm

### Bước 4 — Output

**Nếu có `gh` CLI:**
```bash
gh pr create \
  --title "<type>: <tên ngắn gọn>" \
  --body "$(cat <<'EOF'
<nội dung description>
EOF
)"
```

**Nếu không có `gh`:**
In ra description đầy đủ, sẵn sàng để paste vào GitHub.

**PR title format:**
```
feat: <what>       ← feature mới
fix: <what>        ← bug fix
refactor: <what>   ← không thay đổi behavior
chore: <what>      ← tooling, deps, config
```

---

## Nếu project không có PR template

Dùng fallback structure:

```markdown
## What
## Why
## Changes
## Test plan
## Notes for reviewer
```

Sau đó gợi ý tạo `.github/PULL_REQUEST_TEMPLATE.md` cho project (template có trong oops-skills).

---

## Gotchas

**Không copy-paste commit message làm description.** Commit message mô tả từng bước; PR description mô tả toàn bộ thay đổi như một đơn vị.

**"What" và "Why" hay bị nhầm.** What = kết quả/output. Why = lý do tồn tại. Nếu câu bắt đầu bằng "Because" hoặc "In order to" → đó là Why, không phải What.

**Checklist phải có lệnh thật.** `- [ ] Tests xanh (npm test)` có ích hơn `- [ ] Tests pass`.
