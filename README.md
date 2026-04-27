# MFH — Planning & State Management for Claude Code

A lightweight planning system for software projects built with [Claude Code](https://claude.ai/code). Uses slash commands to manage milestones, phases, plans, and progress across sessions.

---

## How it works

MFH has two parts:

- **Skills** — Claude Code slash commands installed once at the user level (`~/.claude/skills/`). Available in every project automatically.
- **Project structure** — The `.mfh/` folder inside each project, scaffolded by `/mfh-init`.

---

## Setup

### 1. Install the skills (once per machine)

Clone this repo and copy the skills to your user-level Claude directory:

```bash
git clone https://github.com/MrFauxHawk/mfh.git ~/mfh
cp -r ~/mfh/.claude/skills/* ~/.claude/skills/
```

To update skills in the future:

```bash
cd ~/mfh && git pull && cp -r .claude/skills/* ~/.claude/skills/
```

### 2. Initialize a project

Open any project in Claude Code and run:

```
/mfh-init
```

This scaffolds the `.mfh/` folder structure into your project and adds the required `.gitignore` entries.

### 3. Seed your project

Fill in:
- `.mfh/design/roadmap.md` — your project vision, tech stack, and goals
- `.mfh/design/milestones.md` — your milestone and phase breakdown

Then run `/mfh-start` to begin your first phase.

---

## Project structure

```
.mfh/
  design/
    roadmap.md        ← vision, tech stack, goals (git-tracked)
    milestones.md     ← milestone + phase breakdown (git-tracked)
  library/            ← coding standards, conventions, architecture rules (git-tracked)
  plans/              ← active plan files, one per phase (gitignored)
  state/
    progress.md       ← all active phases (gitignored)
    built.md          ← permanent changelog (git-tracked)
    decisions.md      ← non-obvious decisions and rationale (git-tracked)
```

---

## Commands

| Command | What it does |
|---|---|
| `/mfh-init` | Scaffold `.mfh/` structure into a new project |
| `/mfh-status` | Snapshot of all milestones, phases, and active work |
| `/mfh-start` | Start a new milestone + phase |
| `/mfh-plan` | Create an implementation plan for the active phase |
| `/mfh-execute [M#-P#]` | Pick up active work and begin executing |
| `/mfh-update [M#-P#]` | Log progress, decisions, and what remains |
| `/mfh-done [M#-P#]` | Close out a completed phase, update changelog |
| `/mfh-newfeature` | Add a new milestone or phase to the project |
| `/mfh-commit` | Group changes into logical Conventional Commits |
| `/mfh-push` | Push to remote and handle errors |

Commands marked `[M#-P#]` accept an optional phase argument (e.g. `/mfh-done M4-P3`). If omitted and only one phase is active, it uses that automatically. If multiple phases are active, it asks.

---

## Multiple active phases

MFH supports running multiple phases in parallel. Each active phase gets its own section in `progress.md`:

```markdown
---

## M4-P3

**Milestone:** M4 — Technician Scheduling
**Phase:** P3 — Gantt
**Plan:** m4-p3-plan.md
**Status:** in progress
**Started:** 2026-04-24
**Notes:**
_(none yet)_

---

## M6-P5

**Milestone:** M6 — Weekly Improvements
**Phase:** P5 — Safety Extras
**Plan:** (none)
**Status:** in progress
**Started:** 2026-04-27
**Notes:**
_(none yet)_
```

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
