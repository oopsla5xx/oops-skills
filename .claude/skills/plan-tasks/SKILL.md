---
name: plan-tasks
description: Break a spec file into ordered, concrete implementation steps with done criteria. Appends a Plan section to the existing .ai/tasks/<name>.md. Run after write-spec, before coding.
---

# Plan Tasks

Đọc spec đã viết, chia nhỏ thành các bước implement có thứ tự rõ ràng. Output là một `## Plan` section được append vào cuối file spec — không tạo file mới.

---

## Run

### Bước 1 — Đọc đầu vào

```
Đọc: .ai/tasks/<tên-task>.md          ← spec cần plan
Đọc: .ai/context/architecture.md      ← hiểu cấu trúc hiện tại
```

Trả lời các câu hỏi này trước khi viết plan:
- Có code nào hiện tại cần đọc/sửa không? Nếu có, đọc ngay.
- Scope mới hoàn toàn (greenfield) hay sửa code cũ (brownfield)?
- Có dependency nào cần tạo trước không (DB schema, interface, type)?

### Bước 2 — Tìm "atoms"

Một **atom** là đơn vị nhỏ nhất có thể verify độc lập.

Cách tìm: đọc Scope trong spec → với mỗi file/module, tự hỏi "phần nhỏ nhất có thể làm và test độc lập là gì?"

Ví dụ decompose:
```
Scope: "thêm password reset"
→ Tách thành:
   - token generation logic (pure function, testable)
   - DB schema (migration)
   - API endpoint request (HTTP layer)
   - email send (side effect, mockable)
   - API endpoint confirm (HTTP layer)
   - wiring (integration)
```

### Bước 3 — Sắp xếp theo thứ tự dependency

Thứ tự chuẩn (từ dưới lên):

```
1. Types / interfaces / contracts  ← không phụ thuộc vào ai
2. Pure business logic              ← phụ thuộc vào types
3. Data persistence (migrations, models)
4. Side effects (email, queue, cache)
5. API / interface layer            ← gọi vào logic + persistence
6. Wiring / integration             ← nối các layer lại
7. Tests bao phủ toàn flow          ← xác nhận end-to-end
```

Quy tắc: bước N không được gọi code của bước N+1.

### Bước 4 — Viết plan

Append vào cuối file spec:

```markdown
## Plan

<!-- Thực hiện theo thứ tự. Mỗi bước chỉ bắt đầu khi bước trước done. -->

- [ ] 1. <động từ> <file/function cụ thể> — <mô tả ngắn>
         Done khi: <tiêu chí verify cụ thể>

- [ ] 2. ...
```

**Yêu cầu cho mỗi bước:**
- Bắt đầu bằng động từ hành động: `Create`, `Add`, `Update`, `Remove`, `Wire`, `Migrate`, `Test`
- Ghi tên file/function cụ thể nếu biết
- `Done khi:` phải verify được ngay (chạy lệnh, xem output, đọc code) — không được là "cảm giác đúng"

**Ví dụ bước tốt:**
```
- [ ] 1. Create `src/auth/reset-token.ts` — hàm generateToken() và validateToken()
         Done khi: unit test pass cho cả hai hàm, kể cả case token hết hạn
```

**Ví dụ bước tệ:**
```
- [ ] 1. Implement password reset logic   ← quá rộng, không biết "done" là gì
- [ ] 2. Make sure it works              ← không verify được
```

### Bước 5 — Kiểm tra chất lượng plan

Trước khi lưu:

```
[ ] Mỗi bước có "Done khi:" cụ thể?
[ ] Bước đầu không phụ thuộc vào bước nào chưa có?
[ ] Không có bước nào span quá 1 buổi code (~2-3h)?
[ ] Bước lớn nhất có thể tách nhỏ hơn không?
[ ] Nếu bước 3 fail → bước 4 vẫn có thể bắt đầu không? (nếu không, ghi rõ dependency)
```

Nếu có bước nào fail check → tách hoặc rewrite bước đó trước khi lưu.

---

## Sau khi plan xong

Confirm với user: hiển thị plan vừa viết, hỏi "Bắt đầu từ bước 1?" hoặc "Có bước nào cần điều chỉnh không?"

Không tự động bắt đầu code — plan chưa được user approve thì chưa implement.

---

## Gotchas

**Brownfield: đọc code hiện tại trước khi plan.** Plan dựa trên giả định về code cũ sẽ sai. Dùng grep/read để xác nhận tên hàm, interface, và pattern hiện tại trước khi ghi vào plan.

**Tránh "mega step" cuối.** "Integration + testing + polish" gom vào 1 bước cuối là dấu hiệu plan chưa đủ chi tiết. Tách ra.

**Đừng plan quá xa.** Nếu spec có Open questions chưa giải quyết → plan chỉ đến trước điểm cần quyết định đó, ghi chú rõ "⚠️ cần confirm X trước khi tiếp."
