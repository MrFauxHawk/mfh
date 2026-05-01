---
description: Pick up active work and begin executing the plan
---

# /mfh-execute

You are picking up active work and executing it. This command accepts an optional argument (e.g. `/mfh-execute M4-P3` or `/mfh-execute WI-P5`). This project has two tracks: **Milestones** (`M#-P#`) and **Weekly Improvements** (`WI-P#`).

**Step 1 — Identify the phase:**
Read `.mfh/state/progress.md`.

- If a phase was provided as an argument (e.g. `M4-P3` or `WI-P5`), use that phase.
- If no argument and only one active phase exists, use that one.
- If no argument and multiple active phases exist, ask: "Which phase do you want to work on? (e.g. M4-P3, WI-P5)"

**Step 2 — Read the phase state:**
From the identified `## M#-P#` or `## WI-P#` section, note:
- Track (Milestone or Weekly Improvements) and Phase name
- Plan filename (if any)
- Status
- Notes (what has been done, what remains)

**Step 3 — Mark phase in progress in milestones.md:**
Read `.mfh/design/milestones.md`. Find the table row for the active phase. If it is not already marked `🔄`, update the status symbol to `🔄` now — before doing any other work.

**Step 4 — Read plan (if one exists):**
If a plan file is referenced, read it from `.mfh/plans/`.

**Step 5 — Read library and decisions:**
Read all files in `.mfh/library/`. These contain the coding standards, conventions, and architectural rules you must follow throughout all work in this session.

Also read `.mfh/state/decisions.md`. Do not contradict or work around these decisions without discussing it first.

**Step 6 — Summarize context:**
Tell the user:
- What milestone and phase is being worked on
- What the goal is (from the plan or milestones.md)
- What has already been completed (from Notes)
- What remains to be done

**Step 7 — Begin working:**
Execute the remaining tasks from the plan (or if no plan, based on the phase description from milestones.md and the Notes in progress.md).

Throughout all work:
- Follow all conventions in `.mfh/library/`
- Follow the task order from the plan
- After completing each task:
  1. Briefly note it to the user
  2. In `.mfh/state/progress.md`, find the matching `- [ ] N.` line under **Tasks:** and change it to `- [x] N.`

**Write rules during execution:**
- **DO** tick off task checkboxes as each task completes (change `[ ]` → `[x]`)
- **DO** append to `.mfh/state/decisions.md` immediately when a non-obvious decision is made
- **DO** update `milestones.md` phase status to `🔄` at the start of Step 3 (one-time, if not already set)
- **DO NOT** modify `built.md` or any other state file — those are updated by `/mfh-update` and `/mfh-done`
- **DO NOT** change the **Status**, **Notes**, or any other field in progress.md — only the task checkboxes
