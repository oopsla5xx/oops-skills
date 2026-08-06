# oops-skills

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
![Status](https://img.shields.io/badge/status-production-brightgreen.svg)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)

> A structured starter kit for AI-assisted development — giving every tool the same source of truth, the same workflow, and the same project context.

**[Tiếng Việt](README.vi.md)** | English

---

## Table of Contents

- [Overview](#overview)
- [Why it exists](#why-it-exists)
- [Quick start](#quick-start)
- [Daily workflow](#daily-workflow)
- [Directory structure](#directory-structure)
- [Design principles](#design-principles)
- [Legacy support](#legacy-support)
- [Contributing](#contributing)

---

## Overview

`oops-skills` is a drop-in starter kit that standardizes how AI tools interact with your repository.

Instead of scattering instructions and commands across many files, it consolidates everything important into a `.ai/` directory — so Claude Code, Cursor, GitHub Copilot, and any other markdown-aware agent all share the same knowledge base.

---

## Why it exists

Most AI workflows break down because:

- Instructions are scattered across multiple places
- Commands are guessed rather than explicitly defined
- Each tool follows a different process
- Context goes stale quickly

`oops-skills` fixes this by putting all project knowledge in `.ai/` and having every adapter point there.

---

## Quick start

Copy into your project:

```bash
cp -r oops-skills/. your-project/
```

Then run:

```
/setup-ai-context
```

This command auto-detects your stack and fills in the core files under `.ai/`.

To configure manually, edit these three files:

| File | Purpose |
|------|---------|
| `.ai/context/architecture.md` | System overview — what it is, who uses it |
| `.ai/context/conventions.md` | Coding rules with ❌/✅ examples |
| `.ai/commands.md` | Actual build / test / lint / deploy commands |

---

## Daily workflow

### 1. One-time setup

```
/setup-ai-context
```

### 2. Sync before starting work

```
/sync-ai-context
```

### 3. Task flow (see `.ai/workflows/task-flow.md` for full detail)

```
Phase 1 — Brief
  /clarify-scope          Small/clear → proceed. Large/vague → ask + state assumptions
  /write-spec             Compile context into .ai/tasks/<name>.md
  /plan-tasks             Break spec into steps with "Done when:" criteria

Phase 2 — Implement
  /implement-tdd          Red → Green → Refactor for each step

Phase 3 — Self-check
  /write-test-scenarios   Write acceptance scenarios and test cases
  ✓ Tests green, lint clean, conventions followed, security checked

Phase 4 — Ship
  /review-pr              Review diff against spec
  /create-pr-description  Auto-generate PR description from spec
  /ship                   Push branch, open PR, clean up task file
```

---

## Directory structure

```
.ai/                          # Single source of truth for all agents
├── commands.md               # Abstract commands → actual shell commands
├── status.md                 # Shared coordination state
├── context/
│   ├── architecture.md       # System overview
│   ├── conventions.md        # Coding rules with examples
│   ├── security-checklist.md # Pre-ship security checklist
│   └── domain-glossary.md    # Project-specific terms
├── workflows/
│   ├── onboarding.md         # First-time setup protocol
│   └── task-flow.md          # 4-phase task lifecycle
├── tasks/
│   ├── TEMPLATE.md           # Task brief template
│   └── <name>.md             # Active task briefs
└── decisions/                # Architecture Decision Records (ADRs)

.claude/skills/               # Claude Code adapter scripts
.github/
└── copilot-instructions.md   # GitHub Copilot adapter
.cursorrules                  # Cursor adapter
```

---

## Design principles

1. **`.ai/` is the single source of truth** — update one place, every adapter benefits.
2. **Commands must be explicit and runnable** — don't guess `npm test` if the project uses `make test`.
3. **Workflows are readable and plugin-free** — no hidden magic, just markdown files.
4. **Spec drives everything** — Spec → Plan → Implement → Test → Review → PR → Ship.
5. **Coordination state lives in a file** — `status.md` lets multiple agents work safely in parallel.

---

## Legacy support

To help existing projects or older adapters migrate without disruption:

- Add a `LEGACY.md` describing how old commands map to the new `.ai/commands.md`.
- Optionally provide a `legacy-migrate.sh` script to detect and flag outdated commands in CI.
- Link `LEGACY.md` clearly from your README so users know where to look.

Suggested `LEGACY.md` contents:

- Mapping table: `old-command` → equivalent in `.ai/commands.md`
- Minimal manual steps to keep old CI jobs running in parallel
- Rollback advice if migration hits an issue

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for the full guide.

Quick summary:
- Fork → branch off `main` → open a PR
- Follow `.ai/context/conventions.md` before opening a PR
- Run the self-check in `.ai/workflows/task-flow.md` Phase 3 before requesting review

---

Licensed under the [MIT License](LICENSE).
