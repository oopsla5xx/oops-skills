# Conventions

<!-- Mỗi rule: tên rõ ràng, ví dụ ❌ (sai) và ✅ (đúng), lý do ngắn -->
<!-- Xóa các section không liên quan đến project của bạn -->

---

## Error Handling

### Rule: Không nuốt lỗi âm thầm

❌
```typescript
try {
  await doSomething()
} catch (e) {
  // ignore
}
```

✅
```typescript
try {
  await doSomething()
} catch (e) {
  logger.error('doSomething failed', { error: e, context: ... })
  throw e // hoặc handle cụ thể
}
```

**Lý do:** Lỗi bị nuốt làm mất trace, debug rất khó về sau.

---

## Naming

### Rule: <!-- TODO: thêm rule naming của project -->

❌
```
// ví dụ sai
```

✅
```
// ví dụ đúng
```

---

## Data Access

### Rule: <!-- TODO: thêm rule về cách access database/store -->

---

## API / Interface

### Rule: <!-- TODO -->

---

## Testing

### Rule: <!-- TODO: unit test hay integration test? mock ở đâu, không mock ở đâu? -->

---

<!-- Thêm nhóm rule mới theo cùng format -->
