# oops-skills

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE) [![Status](https://img.shields.io/badge/status-production-brightgreen.svg)]()

A polished starter kit for AI-assisted development workflows — designed to work with Claude Code, Cursor, GitHub Copilot, and any markdown-aware coding agent.

It gives every tool the same source of truth, the same workflow, and the same project context.

---

## Mục lục

- [Tổng quan](#tổng-quan)
- [Tại sao tồn tại](#tại-sao-tồn-tại)
- [Bắt đầu nhanh](#bắt-đầu-nhanh)
- [Quy trình hàng ngày](#quy-trình-hàng-ngày)
- [Cấu trúc thư mục](#cấu-trúc-thư-mục)
- [Nguyên tắc thiết kế](#nguyên-tắc-thiết-kế)
- [Legacy (Hỗ trợ thế hệ cũ)](#legacy-hỗ-trợ-thế-hệ-cũ)
- [Tùy chỉnh & đóng góp](#tùy-chỉnh--đóng-góp)

---

## Tổng quan

`oops-skills` là một starter kit được đóng gói để chuẩn hóa cách các công cụ AI tương tác với repository của bạn. Thay vì rải hướng dẫn và lệnh ra nhiều tập tin, kit này gom mọi thứ quan trọng vào thư mục `.ai/` để các adapter (Claude, Cursor, Copilot, ...) đều có thể tham chiếu cùng một nguồn chân thật.

---

## Tại sao tồn tại

Phần lớn workflow AI thất bại vì:

- Hướng dẫn nằm rải rác ở nhiều nơi
- Các lệnh được đoán mò thay vì định nghĩa rõ
- Quy trình khác nhau giữa các công cụ
- Ngữ cảnh nhanh chóng trở nên lỗi thời

`oops-skills` khắc phục bằng cách: đặt kiến thức dự án trong `.ai/`, và để mọi adapter trỏ về đó.

---

## Bắt đầu nhanh

Sao chép vào dự án của bạn:

```bash
cp -r oops-skills/. your-project/
```

Sau đó chạy:

```text
/setup-ai-context
```

Lệnh này tự động phát hiện stack và điền các tập tin cốt lõi trong `.ai/`.

Nếu muốn cấu hình bằng tay, chỉnh những file sau:

1. `.ai/context/architecture.md` — tổng quan hệ thống
2. `.ai/context/conventions.md` — quy tắc mã hoá với ví dụ ❌/✅
3. `.ai/commands.md` — các lệnh build/test/lint/deploy thực tế

---

## Quy trình hàng ngày

1) Thiết lập một lần

```text
/setup-ai-context
```

2) Đồng bộ trước khi bắt đầu làm việc

```text
/sync-ai-context
```

3) Luồng tác vụ (xem chi tiết trong `.ai/workflows/task-flow.md`)

Phase 1 — Brief
  - `/clarify-scope`          Nhỏ/rõ → tiếp tục. Lớn/mơ hồ → hỏi 1 câu + nêu giả định
  - `/write-spec`             Biên dịch ngữ cảnh thành `.ai/tasks/<name>.md`
  - `/plan-tasks`             Chia spec thành các bước có tiêu chí "Done when:"

Phase 2 — Implement
  - `/implement-tdd`          Red → Green → Refactor cho từng bước

Phase 3 — Self-check
  - `/write-test-scenarios`   Viết kịch bản chấp nhận và test case
  - Checklist: tests xanh, lint sạch, conventions tuân thủ, security được kiểm tra

Phase 4 — Ship
  - `/review-pr`              Review diff so với spec
  - `/create-pr-description`  Tự động sinh mô tả PR từ spec
  - `/ship`                  Push branch, mở PR, cập nhật status, dọn dẹp task file

---

## Cấu trúc thư mục (tóm tắt)

```
.ai/                          # Single source of truth for all agents
├── commands.md               # Abstract commands → project shell commands
├── status.md                 # Shared coordination state
├── context/
│   ├── architecture.md
│   ├── conventions.md
│   └── domain-glossary.md
├── workflows/
│   ├── onboarding.md
│   └── task-flow.md
├── tasks/
│   ├── TEMPLATE.md
│   └── <name>.md
└── decisions/                # ADRs

.claude/skills/               # Claude Code adapter scripts
.github/
└── copilot-instructions.md   # Copilot adapter
```

---

## Nguyên tắc thiết kế

1. `.ai/` là nguồn chân thật: cập nhật một nơi, mọi adapter hưởng lợi.
2. Các lệnh phải rõ ràng và có thể thực thi: đừng đoán `npm test` nếu dự án dùng `make test`.
3. Workflow đọc được và không phụ thuộc plugin bí ẩn.
4. Spec dẫn dắt mọi thứ: Spec → Plan → Implement → Test → Review → PR → Ship.
5. Trạng thái phối hợp lưu trong file (`status.md`) để nhiều agent cùng làm việc an toàn.

---

## Legacy (Hỗ trợ thế hệ cũ)

Để giúp các dự án đã tồn tại hoặc adapter cũ không phải thay đổi ngay, thêm phần hỗ trợ "legacy" như sau:

- Thêm `LEGACY.md` (nếu cần) mô tả cách mapping các lệnh cũ sang `.ai/commands.md` mới.
- Cung cấp script `legacy-migrate.sh` (tùy chọn) để detect và báo các lệnh/CI đã cũ.
- Trong README hiển thị liên kết rõ ràng tới `LEGACY.md` và nêu "Nếu bạn dùng adapter hoặc CI cũ, xem hướng dẫn Legacy để chuyển đổi nhẹ nhàng."

Gợi ý nội dung `LEGACY.md`:

- Bảng mapping: `old-command` → `.ai/commands.md` tương ứng
- Các bước thủ công tối thiểu để giữ các job CI cũ chạy song song
- Lời khuyên rollback nếu migration xảy ra lỗi

---

## Tùy chỉnh & đóng góp

- Muốn tuỳ chỉnh workflow? Thay `.ai/workflows/*` và cập nhật adapter tương ứng trong thư mục adapter.
- Muốn đóng góp? Fork → Branch → PR. Vui lòng tuân thủ `.ai/context/conventions.md` trước khi mở PR.

---

Nếu muốn, tôi có thể:
- Thêm badges (CI, coverage) với URL thực tế
- Tạo `LEGACY.md` và script mẫu `legacy-migrate.sh`
- Viết phiên bản README tiếng Anh song song

Cho tôi biết bạn muốn tôi thêm gì — tôi sẽ cập nhật tiếp.
