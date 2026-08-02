# Security Checklist

Use this checklist before moving to Ship and during `/review-pr`.

```
[ ] input-validation: User/external input is validated and constrained before use
[ ] authz-authn: Access control/authentication paths are correct for changed behavior
[ ] secrets: No hardcoded secrets/credentials/tokens in code, config, logs, or tests
[ ] data-access: No SQL/query string concatenation from untrusted input (use parameterization)
[ ] race-condition: Shared-state/concurrent flows are reviewed for race conditions and unsafe ordering
[ ] error-exposure: No stack traces/internal details exposed to end users
[ ] file-handling: If file upload/download is touched, type/size/path handling is validated
[ ] dependency-risk: If new dependencies are added, vulnerability check has been completed
```

If any item is not applicable, mark it as `N/A` *inline* with a short reason (e.g., `N/A file-handling: feature does not touch upload/download`). If an item is applicable and verified, mark it as `[x]`.
