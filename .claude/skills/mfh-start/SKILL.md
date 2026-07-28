---
description: Start a new milestone and phase of work
---

# /mfh-start

You are starting a new phase of work. This project has two tracks: **Milestones** (`M#-P#`) and, depending on which variant `.mfh/design/milestones.md` uses, either **Weekly Improvements** (`WI-P#`) or **App Backlogs** (`{PREFIX}-P#`, e.g. `EMP-P3` — monorepo projects, one section per app, never move once created). Check which second track this project has before proceeding.

**Step 1 — Ask the user:**
"Which phase do you want to work on? (e.g. M3-P2, WI-P5, EMP-P3, or just describe it)"

**Step 2 — Read milestones.md:**
Read `.mfh/design/milestones.md`. Find the requested phase in the appropriate track. Summarize what that phase involves based on its description.

**Step 3 — Check progress.md:**
Read `.mfh/state/progress.md`. If a section for this phase (e.g. `## M3-P2`, `## WI-P5`, or `## EMP-P3`) already exists, say:
"[phase] is already in progress. Run `/mfh-execute [phase]` to continue working, or `/mfh-progress [phase]` to log what's been done."
Then stop.

**Step 4 — If not in progress, present the phase:**
Show the user:
- What the phase is
- What it involves (from `.mfh/design/milestones.md`)
- Any relevant context you can infer

Then ask via `AskUserQuestion`: "Create a plan before starting, or just get to work?" — **Create a plan** / **Just get to work**.

**If plan:**
Say: "Handing off to /mfh-plan." Then invoke `/mfh-plan` with the phase context already established.

**If no plan:**
Update the phase icon in `.mfh/design/milestones.md` from ⬜ to 🔄 for this phase. Milestone phase rows use a bare number in the table cell (`| ⬜ 3 |`); Weekly Improvement and App Backlog rows use the full phase identifier (`| ⬜ WI-P5 |`, `| ⬜ EMP-P3 |`) — make sure you're editing the right cell.

Append a new section to `.mfh/state/progress.md`. If the file currently contains `_(no active phases)_`, replace that line with the new section. Otherwise append after the last `---` divider.

For a **Milestone phase** (`M#-P#`):
```
---

## M#-P#

**Milestone:** M# — [Milestone Name]
**Phase:** P# — [Phase Name]
**Plan:** (none)
**Status:** in progress (no plan)
**Started:** [today's date]
**Notes:**
_(none yet)_
```

For a **Weekly Improvement phase** (`WI-P#`):
```
---

## WI-P#

**Track:** Weekly Improvements
**Phase:** WI-P# — [Phase Name]
**Plan:** (none)
**Status:** in progress (no plan)
**Started:** [today's date]
**Notes:**
_(none yet)_
```

For an **App Backlog phase** (`{PREFIX}-P#`, e.g. `EMP-P3`):
```
---

## {PREFIX}-P#

**Track:** App Backlog — [App Name] (`{PREFIX}`)
**Phase:** {PREFIX}-P# — [Phase Name]
**Plan:** (none)
**Status:** in progress (no plan)
**Started:** [today's date]
**Notes:**
_(none yet)_
```

Then tell the user: "[phase] added to active work. Run `/mfh-execute [phase]` when you're ready to begin."
