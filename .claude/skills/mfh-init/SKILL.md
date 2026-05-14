---
description: Initialize a new project with MFH planning structure
---

# /mfh-init

You are initializing a new project with the MFH planning and state management structure.

**Step 1 — Confirm the project root:**
Ask: "What is the path to your project root? Press enter to use the current working directory."

If the user presses enter or provides no path, use the current working directory.

**Step 2 — Check for existing MFH structure:**
Check if `.mfh/` already exists at the project root. If it does, say:
"An `.mfh/` folder already exists in this project. Run `/mfh-status` to see the current state."
Then stop.

**Step 3 — Gather setup info:**
Ask all questions together in one message:
1. "What is the project name?"
2. "Is this a monorepo? (yes/no)"
3. "What are the main modules or areas of this codebase? These become your commit scopes. (e.g. `api, ui, auth, db, config`)"

Use the project name to replace `[Project Name]` in all template files. Use the modules list to populate the Scopes section of `git.md`.

If the user isn't sure what their modules are, ask: "What is the app for?" Then suggest sensible scope defaults based on the answer — for example, a web app might default to `api, ui, auth, db, config`; a mobile app to `screens, api, state, config`; a CLI tool to `cmd, core, config`. Present the suggestions and let the user confirm or adjust before continuing.

**Step 4 — Create the folder structure:**
Create these directories:
- `{project}/.mfh/design/`
- `{project}/.mfh/library/`
- `{project}/.mfh/plans/`
- `{project}/.mfh/state/`

**Step 5 — Create template files:**

`.mfh/design/roadmap.md`:
```markdown
# [Project Name] — Roadmap

## Vision
[What does this project ultimately deliver?]

## Tech Stack
[Key technologies, frameworks, databases]

## Goals
- [Goal 1]
- [Goal 2]

---

## Current Track

[MONOREPO: **App Backlogs** — Continuous per-app improvement tracks. Each app has its own backlog with a 3-letter prefix, plus a cross-app Platform track (PLT). No fixed end date.]
[NON-MONOREPO: **Weekly Improvements** — Continuous rolling backlog of improvements worked on as capacity allows. No fixed end date.]
```

`.mfh/design/milestones.md` — use the correct variant based on the monorepo answer:

**Non-monorepo variant:**
```markdown
# [Project Name] — Milestones & Weekly Improvements

This file has two tracks:

**Milestones** — named delivery cycles with a fixed goal. Numbered `M1, M2, …`. Phases within a milestone are `P1, P2, …`. Plans saved as `.mfh/plans/m{N}-p{N}-plan.md`.

**Weekly Improvements** — continuous rolling backlog with no end date. Phases use the `WI-P#` prefix. Plans saved as `.mfh/plans/wi-p{N}-plan.md`.

**Workflow per phase (both tracks):**
1. `/mfh-start` — select milestone/track + phase; creates `progress.md` entry
2. `/mfh-plan` — write the plan
3. `/mfh-execute` — execute the plan
4. `/mfh-done` — close the phase, update changelog

**File structure:** Active milestones first → Weekly Improvements → Completed milestones in numbered order. When a milestone ships, move it into the Completed section in its numbered position.

**Status legend:** ✅ Complete · 🔄 In progress · ⬜ Not started

---

## M1 — [Milestone Name] (Active)

**Goal:** [What does this milestone deliver?]

### Current Position: P1 next — not started

| Phase | Description |
|-------|-------------|
| ⬜ 1 | [Phase description] |

---

# Weekly Improvements — Continuous Track

Rolling backlog of improvements worked on as capacity allows. No fixed end date — phases are added as new items come in and closed when done.

Phases use the `WI-P#` prefix. Plans saved as `.mfh/plans/wi-p{N}-plan.md`.

### Current Position: no phases started

| Phase | Description |
|-------|-------------|
| ⬜ WI-P1 | [First improvement] |

---

# Completed Milestones

Milestones move here when all phases are done. Slotted in numbered order.

---
```

**Monorepo variant:**
```markdown
# [Project Name] — Milestones & App Backlogs

This file has two tracks:

**Milestones** — named delivery cycles with a fixed goal. Numbered `M1, M2, …`. Phases within a milestone are `P1, P2, …`. Plans saved as `.mfh/plans/m{N}-p{N}-plan.md`.

**App Backlogs** — continuous per-app improvement tracks with no end date. Each app uses a 3-letter prefix. Phases use the `{PREFIX}-P#` format (e.g. `PRT-P1`). Plans saved as `.mfh/plans/{prefix}-p{N}-plan.md`. These sections never move — phases are appended and closed in place. New app sections are added here automatically when a milestone that ships a new app completes.

**Workflow per phase (both tracks):**
1. `/mfh-start` — select milestone/track + phase; creates `progress.md` entry
2. `/mfh-plan` — write the plan
3. `/mfh-execute` — execute the plan
4. `/mfh-done` — close the phase, update changelog

**File structure:** Active milestones first → App Backlogs → Completed milestones in numbered order. When a milestone ships, move it into the Completed section in its numbered position.

**Status legend:** ✅ Complete · 🔄 In progress · ⬜ Not started

---

## M1 — [Milestone Name] (Active)

**Goal:** [What does this milestone deliver?]

### Current Position: P1 next — not started

| Phase | Description |
|-------|-------------|
| ⬜ 1 | [Phase description] |

---

# App Backlogs — Continuous

---

## PLT — Platform

Cross-app work: shared packages, auth, infra.

| Phase | Description |
|-------|-------------|

---

_(App backlog sections are added here automatically by `/mfh-done` each time a milestone ships a new app.)_

---

# Completed Milestones

Milestones move here when all phases are done. Slotted in numbered order.

---
```

`.mfh/state/built.md`:
```markdown
# [Project Name] — Changelog

Append-only record of completed phases. Most recent first.

---
```

`.mfh/state/decisions.md`:
```markdown
# [Project Name] — Decisions

Non-obvious implementation choices and their rationale. Reference before making architectural decisions.

---
```

`.mfh/state/progress.md`:
```markdown
# Active Work

Tracks all currently active phases. Updated by `/mfh-start`, `/mfh-plan`, `/mfh-update`, and `/mfh-execute`. Entries removed by `/mfh-done` when a phase completes.

_(no active phases)_
```

`.mfh/library/git.md` — populate the Scopes section using the module names the user provided in Step 3. Generate one example commit per module:
```markdown
# Git Conventions

## Commit Format

```
type(scope): short description
```

### Types
- `feat` — new feature
- `fix` — bug fix
- `chore` — config, tooling, dependencies
- `style` — UI/styling only
- `refactor` — restructure, no behaviour change
- `docs` — documentation only
- `test` — tests only

### Scopes
Use the affected module or area as the scope:

[List each module from Step 3 as a bullet: `- \`module-name\``]

### Examples
[One example commit per module, e.g. `feat(api): add user list endpoint`]

## Branch Rules
- [e.g. work on `main` / feature branches off `main`]
- [e.g. always push after each commit]
```

**Step 6 — Update .gitignore:**
Check if `{project}/.gitignore` exists. If it does, check whether the MFH entries are already present. If not, append:

```
# MFH
.mfh/plans/
.mfh/state/progress.md
```

If `.gitignore` doesn't exist, create it with those entries.

**Step 7 — Confirm:**
Tell the user:
"MFH initialized. Next steps:
1. Fill in `.mfh/design/roadmap.md` with your project vision and stack.
2. Review `.mfh/library/git.md` and add your branch rules.
3. Define your first milestone in `.mfh/design/milestones.md`.
4. Run `/mfh-start` to begin your first phase."
