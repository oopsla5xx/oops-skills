# Contributing to oops-skills

Thank you for your interest in contributing. This document explains how to get started and what we expect from contributions.

**[Tiếng Việt](CONTRIBUTING.vi.md)**

---

## Getting started

1. **Fork** the repository and clone your fork.
2. Create a **feature branch** off `main`:
   ```bash
   git checkout -b feat/your-feature-name
   ```
3. Make your changes, following the conventions below.
4. Open a **Pull Request** against `main` with a clear description.

---

## What we welcome

- Bug fixes and corrections in workflow files
- New skill scripts under `.claude/skills/`
- Improvements to `.ai/context/` templates
- Documentation improvements and translations
- New adapter support (Cursor, Copilot, etc.)

## What we won't merge

- Changes that break the single-source-of-truth principle
- Skills that hard-code project-specific values instead of using `.ai/` references
- Workflow files that skip the Phase 3 self-check requirement
- New external dependencies added without a corresponding ADR in `.ai/decisions/`

---

## Conventions

Follow `.ai/context/conventions.md` — that file is the authoritative style guide for this project. Key points:

- Keep skill scripts short and focused on a single responsibility
- Use relative links inside `.ai/` documents; avoid absolute URLs
- Write comments only when the *why* is non-obvious
- No TODO comments left in submitted code

---

## Pull request checklist

Before requesting review, confirm:

```
[ ] Tests pass (if applicable)
[ ] Lint is clean
[ ] Conventions followed (re-read .ai/context/conventions.md)
[ ] Scope is tight — no changes outside the brief
[ ] No unintended side effects in other files
[ ] Security checklist in .ai/context/security-checklist.md completed
[ ] Docs updated if the change affects them
```

---

## Reporting issues

Open a GitHub Issue with:

- **What you expected** to happen
- **What actually happened**
- Steps to reproduce
- Your environment (OS, shell, Claude Code / Cursor version if relevant)

---

## Code of conduct

Be respectful. Assume good intent. Focus feedback on the code, not the person.

