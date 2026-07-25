---
name: write-test-scenarios
description: Generate manual test scenarios (UC/TC) from the spec file after implementation — use cases and step-by-step test cases for human testers to verify the feature by hand. Output saved to .ai/tasks/<name>-manual-tests.md.
---

# Write Test Scenarios

Tạo tài liệu test thủ công (UC + TC) dựa trên spec đã viết, để người dùng tự kiểm tra feature sau khi implement xong.

**Output:** file `.ai/tasks/<tên-task>-manual-tests.md`

---

## Run

### Bước 1 — Đọc đầu vào

```
Đọc: .ai/tasks/<tên-task>.md    ← lấy Goal + Test plan + Constraints
Đọc: .ai/context/domain-glossary.md  ← dùng đúng thuật ngữ nghiệp vụ
```

Liệt kê tất cả item trong `## Test plan` — đây là danh sách UC cần viết.

### Bước 2 — Xác định Use Cases

Nhóm test plan items thành các UC theo flow người dùng. Một UC = một hành trình người dùng có đầu có cuối.

Ví dụ:
```
Test plan items:
- happy path: reset password thành công
- edge case: token hết hạn
- edge case: token dùng lại
- không làm hỏng: login vẫn hoạt động bình thường

→ UC-01: Reset password thành công
→ UC-02: Xử lý token không hợp lệ
→ UC-03: Flow login không bị ảnh hưởng (regression)
```

### Bước 3 — Viết file test

Tạo `.ai/tasks/<tên-task>-manual-tests.md` theo format sau:

```markdown
# Manual Tests: <tên-task>

**Feature:** <mô tả ngắn từ Goal của spec>
**Tester:** ___________
**Ngày test:** ___________
**Môi trường:** ___________   (staging / local / production)

---

## UC-01: <tên use case>

> <1-2 câu mô tả use case — ai làm gì, mục đích là gì>

**Preconditions:**
- <điều kiện trước khi bắt đầu test>
- ...

---

### TC-01-01: <tên test case cụ thể>

| | |
|---|---|
| **Priority** | High / Medium / Low |
| **Type** | Happy path / Edge case / Regression / Error handling |

**Steps:**
1. <bước cụ thể — đủ chi tiết để người không biết code làm được>
2. ...
3. ...

**Expected result:**
- <điều gì phải xảy ra — cụ thể, quan sát được>
- ...

**Actual result:** ___________

**Pass / Fail:** ⬜ Pass  ⬜ Fail

**Notes:** ___________

---

### TC-01-02: <test case tiếp theo trong cùng UC>
...

---

## UC-02: ...
```

### Bước 4 — Quy tắc viết TC tốt

**Steps phải đủ cụ thể để người không biết code làm được:**
```
# Tốt
1. Mở trình duyệt, truy cập http://localhost:3000/forgot-password
2. Nhập email "test@example.com" vào ô "Email"
3. Nhấn nút "Gửi link đặt lại mật khẩu"
4. Kiểm tra hộp thư của test@example.com

# Tệ
1. Gọi API reset password
2. Kiểm tra kết quả
```

**Expected result phải quan sát được:**
```
# Tốt
- Trang hiển thị thông báo "Đã gửi email. Vui lòng kiểm tra hộp thư."
- Email đến trong vòng 60 giây với subject "Đặt lại mật khẩu"
- Link trong email có dạng https://.../reset?token=...

# Tệ
- Hệ thống xử lý đúng
- Không có lỗi
```

**Preconditions phải rõ ràng:**
- Dữ liệu nào cần có sẵn (user account, order, ...)
- Trạng thái hệ thống cần thiết (email server chạy, feature flag bật, ...)
- Người test cần quyền gì

### Bước 5 — Đảm bảo coverage

Kiểm tra mỗi item trong `## Test plan` của spec đã có ít nhất 1 TC:

```
[ ] happy path → TC-XX-XX
[ ] edge case 1 → TC-XX-XX
[ ] không làm hỏng → TC-XX-XX (regression)
```

Nếu có item trong Test plan chưa có TC → thêm vào trước khi lưu.

### Bước 6 — Báo cáo

Sau khi tạo xong file, báo cáo:
```
Đã tạo: .ai/tasks/<tên-task>-manual-tests.md
- X use cases
- Y test cases (Z high priority)
```

---

## Format nhanh cho từng loại TC

**Happy path:** Actor chính, điều kiện bình thường, flow thẳng từ đầu đến cuối.

**Edge case:** Input biên (rỗng, quá dài, ký tự đặc biệt), trạng thái bất thường (hết hạn, đã dùng, trùng lặp), concurrent action.

**Error handling:** Hệ thống phản hồi đúng khi có lỗi — message rõ ràng, không crash, không lộ thông tin nhạy cảm.

**Regression:** Flow cũ không liên quan trực tiếp đến feature mới — phải verify vẫn hoạt động bình thường.
