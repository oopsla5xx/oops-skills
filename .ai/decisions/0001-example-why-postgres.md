# ADR-0001: Dùng PostgreSQL làm primary database

**Status:** Accepted  
**Date:** 2025-01-15

---

## Context

Project cần lưu trữ dữ liệu có quan hệ phức tạp (user, order, product). Có hai lựa chọn chính: PostgreSQL và MongoDB. Team quen với SQL hơn NoSQL.

## Decision

Dùng PostgreSQL 15.

## Reasons

- Dữ liệu có schema cố định và quan hệ rõ ràng — relational model phù hợp hơn document model
- Team đã có kinh nghiệm với SQL, learning curve thấp
- JSONB support đủ linh hoạt cho các field bán cấu trúc nếu cần

## Tradeoffs

- Horizontal scaling khó hơn MongoDB — chấp nhận được vì traffic hiện tại không yêu cầu
- Schema migration cần cẩn thận hơn — đã có quy trình review migration trước khi deploy

## Consequences

- Mọi thay đổi schema phải qua migration file, không được sửa trực tiếp
- Query phức tạp dùng raw SQL thay vì ORM để tránh N+1

---

<!-- Format cho ADR mới:
     - Tên file: NNNN-slug-mô-tả.md (số tăng dần)
     - Status: Proposed | Accepted | Deprecated | Superseded by ADR-XXXX
     - Giữ ngắn: context + decision + reasons là đủ
     - Ghi rõ tradeoffs đã chấp nhận — đây là phần quan trọng nhất
-->
