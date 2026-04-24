# MFH — Planning & State Management for Claude Code

A lightweight planning system for software projects built with [Claude Code](https://claude.ai/code). Uses slash commands to manage milestones, phases, plans, and progress across sessions.

---

## Structure

```
.mfh/
  design/
    roadmap.md        ← vision, tech stack, goals (tracked in git)
    milestones.md     ← milestone + phase breakdown (tracked in git)
  library/            ← coding standards, conventions, architecture rules (tracked in git)
  plans/              ← active plan files, one per phase (gitignored)
  state/
    progress.md       ← active session state (gitignored)
    built.md          ← permanent changelog (tracked in git)
    decisions.md      ← non-obvious decisions and their rationale (tracked in git)

.claude/
  commands/
    mfh/              ← slash commands
```

---

## Setup

1. Copy this repo's contents into your project root
2. Merge the `.gitignore` entries if your project already has one
3. Seed `.mfh/design/roadmap.md` and `.mfh/design/milestones.md` for your project
4. Add any coding standards or architectural rules to `.mfh/library/`
5. Start a Claude Code session and run `/mfh-status`

---

## Commands

| Command | What it does |
|---|---|
| `/mfh-status` | Snapshot of all milestones, phases, and active work |
| `/mfh-start` | Start a new milestone + phase |
| `/mfh-plan` | Create an implementation plan for the active phase |
| `/mfh-execute` | Pick up active work and begin executing the plan |
| `/mfh-update` | Log progress, decisions, and what remains |
| `/mfh-done` | Close out a completed phase, update changelog |
| `/mfh-newfeature` | Add a new milestone or phase to the project |
| `/mfh-commit` | Group changes into logical Conventional Commits |
| `/mfh-push` | Push to remote and handle errors |

---

## What goes in `.mfh/library/`

Coding standards, conventions, and architectural rules that Claude should follow during all work. Examples:

- Brand/theme guidelines
- Code organization standards
- API design patterns
- Component conventions
- Data flow rules

These are loaded by `/mfh-execute` and `/mfh-plan` so Claude always works within your project's standards.

---

## Git tracking

| Path | Tracked |
|---|---|
| `.mfh/design/` | ✅ yes |
| `.mfh/library/` | ✅ yes |
| `.mfh/state/built.md` | ✅ yes |
| `.mfh/state/decisions.md` | ✅ yes |
| `.mfh/state/progress.md` | ❌ no (local session state) |
| `.mfh/plans/` | ❌ no (ephemeral) |
