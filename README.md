# oops-skills

A polished starter kit for AI-assisted development workflows — designed to work with Claude Code, Cursor, GitHub Copilot, and any markdown-aware coding agent.

It gives every tool the same source of truth, the same workflow, and the same project context.

---

## Why this exists

Most AI coding setups fail for the same reasons:

- instructions live in too many places
- commands are guessed instead of defined
- workflows differ between tools
- context gets stale fast

`oops-skills` fixes that by keeping the important project knowledge in `.ai/` and letting every adapter point back to it.

---

## Quick start

```bash
cp -r oops-skills/. your-project/
```

Then run:

```text
/setup-ai-context
```

That will auto-detect your stack and fill in the core `.ai/` files.

If you prefer manual setup, edit these files:

1. `.ai/context/architecture.md` — system overview and architecture
2. `.ai/context/conventions.md` — coding rules with examples
3. `.ai/commands.md` — actual build, test, lint, and deploy commands

---

## Everyday workflow

### 1) Set up once

```text
/setup-ai-context
```

### 2) Refresh context when you start work

```text
/sync-ai-context
```

### 3) Follow the task flow

```text
Phase 1 — Brief
  /clarify-scope          Small/clear → proceed. Large/vague → ask 1 question + state assumptions
  /write-spec             Convert discussion context → .ai/tasks/<name>.md
  /plan-tasks             Turn the spec into ordered steps with “Done when:” criteria

Phase 2 — Implement
  /implement-tdd          Red → Green → Refactor for each planned step

Phase 3 — Self-check
  /write-test-scenarios   Create UC/TC scenarios for manual validation
  [checklist]             Tests green, lint clean, conventions followed, scope respected, security checked

Phase 4 — Ship
  /review-pr              Review diff against spec; approve or request changes
  /create-pr-description  Fill the PR template from the spec + git history
  /ship                   Push branch, open PR, update status, clean up task files
```

**Small, clear task?** If `/clarify-scope` says “proceed”, you can skip spec and planning and go straight to implementation, then self-check and ship.

---

## What’s inside

```text
.ai/                          # Single source of truth for all agents
├── commands.md               # Abstract commands → project-specific shell commands
├── status.md                 # Shared coordination state for multi-agent work
├── context/
│   ├── architecture.md       # System overview and data flow
│   ├── conventions.md        # Coding rules with ❌/✅ examples
│   └── domain-glossary.md    # Project and business terminology
├── workflows/
│   ├── onboarding.md         # What a new agent reads, in what order
│   └── task-flow.md          # Brief → Implement → Self-check → Ship
├── tasks/
│   ├── TEMPLATE.md           # Template for task briefs
│   └── <name>.md             # Active task brief, removed after merge
└── decisions/                # ADRs for important technical decisions

.claude/skills/               # Claude Code skills wired to .ai/
├── setup-ai-context/
├── sync-ai-context/
├── clarify-scope/
├── write-spec/
├── plan-tasks/
├── implement-tdd/
├── write-test-scenarios/
├── review-pr/
├── create-pr-description/
├── ship/
└── debug/

CLAUDE.md                     # Claude Code adapter
.cursorrules                  # Cursor adapter
.github/
├── copilot-instructions.md   # GitHub Copilot adapter
└── PULL_REQUEST_TEMPLATE.md  # PR template used during shipping
```

---

## Design principles

### 1. `.ai/` is the source of truth
All tool-specific configs are just adapters. Update the workflow once, and every tool benefits.

### 2. Commands are explicit
No guessing `npm test`, `pytest`, or `make build`. The project defines its real commands in `.ai/commands.md`.

### 3. Workflow stays readable
The workflow is plain markdown, so any agent can follow it without custom plugins or hidden logic.

### 4. Specs drive everything
Spec → Plan → Implementation → Test scenarios → Review → PR description → Ship.

### 5. Coordination stays in files
`status.md` acts as shared state so multiple agents can work without special infrastructure.

---

## Adding a new tool

Create an adapter for that tool and point it back to `.ai/`:

```markdown
# [Tool Name] Config

## Before writing code
Read `.ai/context/conventions.md`

## Commands
See `.ai/commands.md`

## Task workflow
Follow `.ai/workflows/task-flow.md`
```

---

## Want to customize it?

Update the files in `.ai/` and the adapters will stay aligned automatically.

If you want, I can also make the README more visually branded with badges, a prettier table, and a stronger marketing-style intro.