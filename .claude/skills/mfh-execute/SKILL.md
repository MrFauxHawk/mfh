---
description: Pick up active work and begin executing the plan
---

# /mfh-execute

You are picking up active work and executing it. This command accepts an optional argument (e.g. `/mfh-execute M4-P3`).

**Step 1 — Identify the phase:**
Read `.mfh/state/progress.md`.

- If a phase was provided as an argument (e.g. `M4-P3`), use that phase.
- If no argument and only one active phase exists, use that one.
- If no argument and multiple active phases exist, ask: "Which phase do you want to work on? (e.g. M4-P3)"

**Step 2 — Read the phase state:**
From the identified `## M#-P#` section, note:
- Milestone and Phase names
- Plan filename (if any)
- Status
- Notes (what has been done, what remains)

**Step 3 — Read plan (if one exists):**
If a plan file is referenced, read it from `.mfh/plans/`.

**Step 4 — Read library and decisions:**
Read all files in `.mfh/library/`. These contain the coding standards, conventions, and architectural rules you must follow throughout all work in this session.

Also read `.mfh/state/decisions.md`. Do not contradict or work around these decisions without discussing it first.

**Step 5 — Summarize context:**
Tell the user:
- What milestone and phase is being worked on
- What the goal is (from the plan or milestones.md)
- What has already been completed (from Notes)
- What remains to be done

**Step 6 — Begin working:**
Execute the remaining tasks from the plan (or if no plan, based on the phase description from milestones.md and the Notes in progress.md).

Throughout all work:
- Follow all conventions in `.mfh/library/`
- Follow the task order from the plan
- After completing each task, briefly note it so the user can track progress

**IMPORTANT**: This command is read-only with respect to MFH state files. Do not update progress.md or built.md during execution — that is done by `/mfh-update` and `/mfh-done`. However, if a non-obvious decision is made during work, append it to `.mfh/state/decisions.md` immediately so it isn't lost.
