---
name: setup-ai-context
description: Bootstrap .ai/ context files — auto-detect stack and fill commands.md, architecture.md, conventions.md instead of filling TODO placeholders manually. Run when setting up oops-skills template in a new project.
---

# Setup AI Context

Tự động điền 3 file template trong `.ai/` bằng cách probe project thay vì điền tay.

**Không viết placeholder, không đoán.** Chỉ điền những gì tìm thấy được trong project.

---

## Run

### Bước 1 — Chạy detection script

```bash
bash .claude/skills/setup-ai-context/detect.sh
```

Đọc toàn bộ output trước khi làm bước tiếp theo.

### Bước 2 — Điền `.ai/commands.md`

Dựa vào section `SCRIPTS` trong output:

- Tìm lệnh `test`: ưu tiên script tên `test`, `test:ci`, hoặc lệnh gọi test framework trực tiếp
- Tìm lệnh `build`: script tên `build`, `compile`, hoặc tương đương
- Tìm lệnh `lint`: script tên `lint`, `check`, hoặc gọi eslint/ruff/clippy trực tiếp
- Tìm lệnh `typecheck`: script tên `typecheck`, `type-check`, `tsc`
- Tìm lệnh `dev`: script tên `dev`, `start`, `serve`
- Tìm lệnh `deploy`: script tên `deploy`, hoặc Makefile target liên quan

Với mỗi lệnh: nếu tìm thấy → ghi lệnh thật vào file. Nếu không có → xóa section đó (đừng để placeholder).

### Bước 3 — Điền `.ai/context/architecture.md`

Dùng section `README EXCERPT` và `TOP-LEVEL STRUCTURE`:

**Project là gì**: lấy từ đầu README (thường là dòng đầu tiên sau title).

**Stack**: từ section `STACK`, `FRAMEWORKS`, `DATABASE SIGNALS`.

**Cấu trúc module**: từ `TOP-LEVEL STRUCTURE` — mô tả 3-5 thư mục quan trọng nhất, bỏ qua `node_modules`, `dist`, `.git`, v.v.

**Luồng dữ liệu**: nếu README có mô tả flow → ghi lại. Nếu không → để mục này với comment `<!-- TODO: mô tả luồng request/event chính -->` và báo cho user.

**Ranh giới giữa module** và **External dependencies**: nếu README không đề cập → để TODO và báo user.

### Bước 4 — Điền `.ai/context/conventions.md`

Dùng section `LINTING / FORMATTING CONFIG`:

- Có `biome.json` → ghi: "Formatter và linter: Biome. Chạy `biome check --apply`"
- Có `.eslintrc*` + `.prettierrc*` → ghi: "Lint: ESLint. Format: Prettier."
- Có `ruff.toml` → ghi: "Lint + format: Ruff."
- Có `.rubocop.yml` → ghi: "Lint: RuboCop."
- Có `.editorconfig` → đọc file đó, extract indent_style và indent_size

Tạo một rule skeleton cho `Error Handling` dựa trên stack (xem Gotchas bên dưới).
Các section `Naming`, `Data Access`, `Testing` → để trống với comment `<!-- TODO -->` và báo user cần điền.

### Bước 5 — Báo cáo kết quả

In ra:
- Những gì đã điền được (với lệnh/giá trị cụ thể)
- Những TODO còn lại mà agent không thể tự điền (luồng dữ liệu, business logic, naming conventions)

---

## Gotchas

**`node` không available:** Script dùng `node -e` để parse JSON. Nếu project là non-Node và không có node → script sẽ skip các section đó. Không sao, output vẫn có ích từ các section khác.

**Monorepo:** `detect.sh` chạy từ root sẽ thấy quá nhiều. Nếu `TOP-LEVEL STRUCTURE` có `apps/`, `packages/`, `services/` → hỏi user muốn setup `.ai/` cho sub-project nào, rồi chạy lại từ thư mục đó.

**README nghèo thông tin:** Nhiều project có README chỉ vài dòng. Khi đó `architecture.md` sẽ có nhiều TODO — đây là expected behavior, không phải lỗi.

**Error Handling rule theo stack:**
- Node.js/TS → ví dụ với `try/catch` và typed errors
- Python → ví dụ với `except Exception as e` và logging
- Rust → ví dụ với `Result<T, E>` và `?` operator
- Go → ví dụ với `if err != nil`
