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

**Step 3 — Create the folder structure:**
Create these directories:
- `{project}/.mfh/design/`
- `{project}/.mfh/library/`
- `{project}/.mfh/plans/`
- `{project}/.mfh/state/`

**Step 4 — Create template files:**

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
```

`.mfh/design/milestones.md`:
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

**Status legend:** ✅ Complete · 🔄 In progress · ⬜ Not started

---

## M1 — [Milestone Name] (Not Started)

**Goal:** [What does this milestone deliver?]

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

**Step 5 — Update .gitignore:**
Check if `{project}/.gitignore` exists. If it does, check whether the MFH entries are already present. If not, append:

```
# MFH
.mfh/plans/
.mfh/state/progress.md
```

If `.gitignore` doesn't exist, create it with those entries.

**Step 6 — Confirm:**
Tell the user:
"MFH initialized. Next steps:
1. Fill in `.mfh/design/roadmap.md` with your project vision and stack.
2. Define your first milestone in `.mfh/design/milestones.md`.
3. Run `/mfh-start` to begin your first phase."
