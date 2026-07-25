# oops-skills

Starter template cho AI workflow — hoạt động với Claude Code, Cursor, GitHub Copilot, và bất kỳ AI coding tool nào đọc được markdown.

## Cách dùng

Copy toàn bộ thư mục này vào root của project, rồi điền vào các file `<!-- TODO -->`.

```
cp -r oops-skills/. your-project/
```

Sau đó chạy `/setup-ai-context` để agent tự detect stack và điền 3 file template, hoặc điền tay:

1. `.ai/context/architecture.md` — mô tả hệ thống
2. `.ai/context/conventions.md` — quy tắc code
3. `.ai/commands.md` — lệnh build/test/deploy thật của project

---

## Workflow

### Setup (một lần)

```
/setup-ai-context   Detect stack, điền .ai/ templates tự động
```

### Mỗi khi bắt đầu làm việc

```
/sync-ai-context    Kiểm tra file .ai/ có bị stale không, patch nếu cần
```

### Task flow

```
Phase 1 — Brief
  /clarify-scope          Nhỏ/rõ → làm luôn. Lớn/mơ hồ → nêu giả định + hỏi 1 câu
  /write-spec             Chuyển context thảo luận → .ai/tasks/<name>.md
  /plan-tasks             Chia spec thành bước có thứ tự + "Done khi:" cho mỗi bước

Phase 2 — Implement
  /implement-tdd          Red → Green → Refactor, từng bước trong Plan

Phase 3 — Self-check
  /write-test-scenarios   Tạo UC/TC cho user tự test thủ công
  [checklist]             test xanh, lint, conventions, scope, side effects

Phase 4 — Ship
  /review-pr              Review diff theo spec: 5 chiều, verdict APPROVE/REQUEST CHANGES
  /create-pr-description  Điền .github/PULL_REQUEST_TEMPLATE.md từ spec + git history
```

**Exception path:** Task nhỏ, rõ ràng → `/clarify-scope` đánh giá "làm luôn" → bỏ qua write-spec, plan-tasks → code trực tiếp → self-check → ship.

---

## Cấu trúc

```
.ai/                          # Source of truth — mọi agent đọc ở đây
├── commands.md               # Abstract commands → lệnh shell cụ thể
├── status.md                 # Phối hợp multi-agent: ai đang làm gì
├── context/
│   ├── architecture.md       # Hệ thống tổng quan, luồng dữ liệu
│   ├── conventions.md        # Quy tắc code với ví dụ ❌/✅
│   └── domain-glossary.md    # Thuật ngữ nghiệp vụ
├── workflows/
│   ├── onboarding.md         # Agent mới vào project đọc gì, theo thứ tự nào
│   └── task-flow.md          # Brief → Implement → Self-check → Ship
├── tasks/
│   ├── TEMPLATE.md           # Template cho task brief
│   └── <name>.md             # Task brief đang active (xóa sau merge)
└── decisions/                # ADR — quyết định kỹ thuật quan trọng
    └── 0001-example-...md

.claude/skills/               # Skills — chỉ dùng được với Claude Code
├── setup-ai-context/         # First-time setup: detect stack, điền templates
├── sync-ai-context/          # Ongoing: kiểm tra .ai/ files có stale không
├── clarify-scope/            # Quyết định hỏi hay giả định trước khi code
├── write-spec/               # Chuyển discussion → .ai/tasks/<name>.md
├── plan-tasks/               # Chia spec thành bước implement có thứ tự
├── implement-tdd/            # Red → Green → Refactor per bước trong Plan
├── write-test-scenarios/     # Tạo UC/TC markdown cho manual testing
├── review-pr/                # Review diff theo spec trước khi ship
└── create-pr-description/    # Điền PR template từ spec + git history

CLAUDE.md                     # Claude Code adapter (dùng @ imports)
.cursorrules                  # Cursor adapter
.github/
├── copilot-instructions.md   # GitHub Copilot adapter
└── PULL_REQUEST_TEMPLATE.md  # PR template (dùng bởi create-pr-description)
```

---

## Nguyên tắc thiết kế

**1. `.ai/` là single source of truth.**
Mọi tool-specific config chỉ là adapter trỏ vào `.ai/`. Khi cập nhật convention hay workflow, chỉ cần sửa một nơi.

**2. Commands là abstract, không hardcode.**
Agent đọc `.ai/commands.md` để biết lệnh thật — không đoán `npm test` hay `pytest`. Cho phép đổi stack mà không sửa workflow.

**3. Workflow bằng plain markdown.**
`task-flow.md` và các skills là hướng dẫn từng bước — bất kỳ agent nào cũng follow được, không cần plugin hay DSL riêng.

**4. Spec là nguồn gốc của mọi thứ.**
Spec (`write-spec`) → Plan (`plan-tasks`) → Implementation (`implement-tdd`) → Test scenarios (`write-test-scenarios`) → Review (`review-pr`) → PR description (`create-pr-description`) đều trace về cùng một file `.ai/tasks/<name>.md`.

**5. Phối hợp qua file.**
`status.md` là shared state cho multi-agent: agent check trước khi bắt đầu, update khi xong. Không cần server hay tool đặc biệt.

---

## Thêm tool mới

Tạo adapter file cho tool đó, trỏ vào `.ai/`:

```markdown
# [Tool Name] Config

## Before writing code
Read `.ai/context/conventions.md`

## Commands
See `.ai/commands.md`

## Task workflow
Follow `.ai/workflows/task-flow.md`
```
