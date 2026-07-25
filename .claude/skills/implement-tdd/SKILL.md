---
name: implement-tdd
description: Implement each step from the plan using TDD — write failing test first, then minimal code to pass, then refactor. Run for each unchecked step in the Plan section of .ai/tasks/<name>.md.
---

# Implement TDD

Implement từng bước trong `## Plan` của spec file theo chu trình TDD.

**Quy tắc cứng:** Không viết implementation trước khi có failing test. Không exception.

---

## Setup

Trước bước đầu tiên, chạy full test suite để confirm baseline xanh:

```bash
# Lấy lệnh từ .ai/commands.md
<test-command>
```

Nếu baseline đỏ → dừng, report với user, không implement trên nền đỏ.

---

## Chu trình cho mỗi bước trong Plan

Lấy bước tiếp theo chưa tick `- [ ]` từ `## Plan` trong spec file. Thực hiện:

### 🔴 Red — Viết test trước

1. Đọc "Done khi:" của bước để biết test cần assert gì
2. Viết test — chỉ test behavior của bước này, không nhiều hơn
3. Chạy test: **phải đỏ**

**Test đỏ đúng nghĩa:**
```
FAIL: expected X but got undefined   ← đúng, function chưa tồn tại
FAIL: expected true but got false    ← đúng, logic chưa implement
```

**Test đỏ sai nghĩa (phải fix trước khi tiếp):**
```
SyntaxError / TypeError              ← test bị lỗi cú pháp
Cannot find module                   ← import sai path
Expected 2 arguments but got 1      ← test tự viết sai
```

Nếu test đỏ vì lý do sai → sửa test, không viết implementation.

### 🟢 Green — Code tối giản để pass

4. Viết implementation nhỏ nhất có thể làm test xanh
5. Chạy test: **phải xanh**
6. Chạy full suite: **không được thêm đỏ mới**

**"Tối giản" có nghĩa là:**
- Không thêm logic cho case chưa có test
- Không thêm abstraction chưa cần thiết
- Không refactor code liên quan ngoài scope bước này

Nếu full suite bị đỏ mới → tìm regression, fix trước khi tiếp.

### 🔵 Refactor — Dọn khi đang xanh

7. Chỉ refactor nếu code vừa viết có vấn đề rõ ràng: tên xấu, duplicate logic, magic number
8. Sau mỗi thay đổi refactor → chạy test lại
9. Nếu test đỏ khi refactor → revert ngay, không để nợ

**Không refactor trong bước này:**
- Code của bước khác trong plan
- Code không liên quan đến bước đang làm
- "Cải thiện" mang tính phòng xa (YAGNI)

### ✅ Tick và commit

10. Đánh dấu bước xong trong spec file:
    ```
    - [x] 1. Create `src/auth/reset-token.ts` ...
    ```
11. Commit với message rõ:
    ```
    test: <bước vừa làm>
    feat: <bước vừa làm>
    ```
    Hoặc gom lại nếu nhỏ:
    ```
    feat: <bước vừa làm> (with tests)
    ```

Sau đó lấy bước tiếp theo và lặp lại.

---

## Các case đặc biệt

**Bước là migration DB:**
Test không theo Red/Green thông thường. Thay vào đó:
1. Viết migration
2. Chạy migration lên: `<migrate-command>`
3. Verify schema đúng (query hoặc inspect)
4. Chạy rollback: `<rollback-command>`
5. Chạy lại migration → confirm idempotent
Done khi: cả hai chiều đều chạy sạch.

**Bước là UI component:**
Nếu project có component test (Storybook, Testing Library) → dùng. Nếu không:
1. Viết test render đơn giản (snapshot hoặc "renders without crash")
2. Implement component
3. Nếu có thể — test interaction (click, input)
Không bỏ qua test hoàn toàn cho UI — test render tối thiểu vẫn có giá trị.

**Bước là wiring/integration:**
Test ở tầng integration, không unit. Mock external service (email, payment) nếu cần. Không mock internal code của chính mình.

**Bước có Open question `[BLOCKING]`:**
Dừng lại, hỏi user, không tự assume. Không implement bước có blocking question.

---

## Dừng và báo cáo khi

- Full suite đỏ sau khi implement và không biết nguyên nhân → report ngay, không implement tiếp
- Scope phình ra so với spec → dừng, cập nhật spec/plan, confirm với user
- Phát hiện ra plan có thứ tự sai (bước N cần code của bước N+2) → báo cáo, không tự reorder

---

## Sau khi tất cả các bước xong

1. Chạy full suite lần cuối
2. Chạy lint và typecheck (lấy lệnh từ `.ai/commands.md`)
3. Chuyển sang `/self-check` (Phase 3 của task-flow)
