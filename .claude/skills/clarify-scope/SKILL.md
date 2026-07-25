---
name: clarify-scope
description: Decide whether to ask before coding or proceed with stated assumptions. Small/clear tasks → do it. Large/ambiguous tasks → state assumptions and ask at most 1 critical question. Run at the start of any task.
---

# Clarify Scope

Trước khi bắt đầu task, áp dụng decision tree sau.

---

## Decision tree

### Task nhỏ, rõ ràng → làm luôn

Dấu hiệu:
- Biết chính xác file nào cần touch
- Goal không thể hiểu theo nhiều cách
- Scope không vượt ra ngoài 1 module

Hành động: **không hỏi, làm ngay.**

---

### Task lớn hoặc mơ hồ → nêu giả định + hỏi tối đa 1 câu

Dấu hiệu của task lớn/mơ hồ:
- Goal có thể hiểu theo nhiều cách
- Chạm > 1 module hoặc > 1 layer
- Không rõ tiêu chí "xong"
- Có thể ảnh hưởng đến behavior hiện tại

**Format phản hồi:**

```
Giả định:
- [điều tôi assume về scope/hướng giải quyết]
- [điều tôi assume về constraint]
- ...

→ Tiến hành theo hướng này. Sửa tôi nếu có gì sai.
```

Nếu có **1 điều** mà nếu sai sẽ đi sai hướng hoàn toàn — hỏi điều đó, không hỏi thêm gì khác.

---

## Câu hỏi "đáng hỏi" vs "không đáng hỏi"

**Đáng hỏi** (sai thì đi ngược hướng):
- "Đây là endpoint mới hay sửa endpoint cũ?"
- "Feature này dành cho tất cả user hay chỉ admin?"
- "Cần backward-compatible không hay được phép breaking change?"

**Không đáng hỏi** (sai chỉ tốn công sửa nhỏ):
- "Đặt tên biến thế nào?"
- "Error message hiển thị gì?"
- "Nên dùng `const` hay `let`?"

---

## Ví dụ

**Task:** "Fix bug login không redirect đúng"
→ Nhỏ, rõ → làm luôn, không hỏi.

**Task:** "Refactor hệ thống auth"
→ Lớn, mơ hồ. Phản hồi:
```
Giả định:
- Giữ nguyên API surface hiện tại (backward-compatible)
- Chỉ refactor internal logic, không thay đổi schema DB
- Scope: src/auth/ và các file gọi vào đó

→ Tiến hành theo hướng này. Sửa tôi nếu có gì sai.

Câu hỏi duy nhất: có session nào đang active trên production cần migrate không, hay được phép invalidate toàn bộ?
```
