# Agent Onboarding Protocol

Khi bắt đầu làm việc trên project này lần đầu (hoặc sau khoảng thời gian dài), đọc theo thứ tự sau.

## Bước 1 — Hiểu project (bắt buộc)

1. Đọc `.ai/context/architecture.md` — hệ thống làm gì, các module chính, luồng dữ liệu
2. Đọc `.ai/context/conventions.md` — quy tắc code, ví dụ đúng/sai
3. Đọc `.ai/commands.md` — lệnh build, test, deploy của project này

## Bước 2 — Sync context (nếu dùng Claude Code)

Chạy `/sync-ai-context` để kiểm tra file nào bị stale trước khi đọc. Nếu tool không available, bỏ qua bước này.

## Bước 3 — Kiểm tra trạng thái hiện tại

4. Đọc `.ai/status.md` — có task nào đang in-progress không? Có gì bị blocked không?
5. Nếu có task brief liên quan trong `.ai/tasks/` — đọc brief đó

## Bước 4 — Sẵn sàng làm việc

Sau khi đọc xong các file trên:
- Nếu được giao task: follow `.ai/workflows/task-flow.md`
- Nếu chưa rõ task: hỏi lại, không tự assume

## Khi nào cần onboard lại

- Sau khi merge thay đổi lớn vào `main`
- Khi thấy mình đang làm trái với conventions
- Khi không chắc một quyết định kỹ thuật có phù hợp với architecture không
