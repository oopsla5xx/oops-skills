---
name: sync-ai-context
description: Refresh stale .ai/ context files before starting a task — detect which of commands.md, architecture.md, conventions.md are outdated and patch only those sections. Run at the start of each task session.
---

# Sync AI Context

Kiểm tra và cập nhật các file `.ai/` bị lỗi thời trước khi bắt đầu task. Chỉ patch phần bị stale — không rewrite toàn bộ file.

Dùng khi: bắt đầu task mới, sau khi pull từ remote, hoặc khi nghi ngờ context không còn chính xác.

---

## Run

### Bước 1 — Phát hiện file stale

```bash
bash .claude/skills/sync-ai-context/check-staleness.sh
```

Đọc toàn bộ output. Script có 3 loại kết quả:
- `OK` — file này còn mới, bỏ qua
- `STALE <source> is newer than <target>` — cần cập nhật phần liên quan
- `MISSING` — file chưa tồn tại, chạy `/setup-ai-context` trước

### Bước 2 — Xử lý từng STALE signal

**Nếu `package.json` newer than `commands.md`:**
```bash
node -e "const d=require('./package.json'); Object.entries(d.scripts||{}).forEach(([k,v])=>console.log(k+': '+v))"
```
So sánh output với `.ai/commands.md`. Cập nhật chỉ những section có lệnh khác — không xóa section nào nếu không chắc đã bị xóa khỏi project.

**Nếu `Makefile` newer than `commands.md`:**
```bash
grep -E "^[a-zA-Z_-]+:" Makefile | sed 's/:.*//'
```
Thêm hoặc sửa target tương ứng trong `commands.md`.

**Nếu `README.md` newer than `architecture.md`:**
```bash
head -60 README.md
```
Đọc phần thay đổi. Cập nhật đúng section bị ảnh hưởng (thường là "Project là gì" hoặc "Stack").

**Nếu có new directory không có trong `architecture.md`:**
Đọc nội dung thư mục đó (`ls <dir>`). Thêm 1-2 dòng mô tả vào section "Cấu trúc module" — không mô tả nếu không rõ mục đích.

**Nếu lint config (`.eslintrc*`, `biome.json`, `ruff.toml`...) newer than `conventions.md`:**
```bash
cat <config-file>
```
Cập nhật đúng rule liên quan trong `conventions.md`. Không xóa rule cũ trừ khi config mới rõ ràng mâu thuẫn.

### Bước 3 — Cập nhật timestamp ngầm

Sau khi sửa xong file nào đó: touch file đó để reset baseline của lần check tiếp theo.

```bash
touch .ai/commands.md          # chỉ file nào vừa cập nhật
touch .ai/context/architecture.md
touch .ai/context/conventions.md
```

### Bước 4 — Báo cáo

In ra:
- File nào đã cập nhật và sửa section gì
- File nào `OK` (không cần động vào)
- Có STALE signal nào agent không đủ thông tin để tự xử lý — cần hỏi user

---

## Gotchas

**Script exit code 1 không phải lỗi:** Exit 1 chỉ có nghĩa là có file stale. Exit 0 = tất cả fresh. Đừng treat exit 1 là failure.

**`find -newer` dùng filesystem mtime:** Nếu project vừa được `git clone`, mọi file đều có mtime = thời điểm clone — script sẽ báo nhiều STALE giả. Trong trường hợp này, dùng section "RECENT GIT CHANGES" để phán đoán thay vì file timestamps.

**Không cập nhật khi không chắc:** Nếu STALE signal là `package.json` mới hơn nhưng chỉ có field `version` thay đổi → bỏ qua, `commands.md` không cần sửa. Ưu tiên không sai hơn không cập nhật.

**Monorepo:** Script phát hiện thư mục mới ở depth 1-2. Nếu project là monorepo với nhiều `apps/`, chỉ quan tâm thư mục bên trong sub-project đang làm việc.
