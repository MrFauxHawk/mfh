---
description: Pick up active work and begin executing the plan
---

# /mfh-execute

You are picking up active work and executing it according to the plan and progress notes.

**Step 1 — Read state:**
Read `.mfh/state/progress.md` to find:
- Active Milestone and Phase
- Active Plan filename (if any)
- Current Status
- Notes (what has been done, what remains)

**Step 2 — Read plan (if one exists):**
If a plan file is referenced, read it from `.mfh/plans/`.

**Step 3 — Read library and decisions:**
Read all files in `.mfh/library/`. These contain the coding standards, conventions, and architectural rules you must follow throughout all work in this session.

Also read `.mfh/state/decisions.md`. This is the project's key decisions log — non-obvious choices made during implementation and why. Do not contradict or work around these decisions without discussing it first.

**Step 4 — Summarize context:**
Tell the user:
- What milestone and phase is being worked on
- What the goal is (from the plan or milestones.md)
- What has already been completed (from the Notes section of progress.md)
- What remains to be done

**Step 5 — Begin working:**
Execute the remaining tasks from the plan (or if no plan, begin working based on the phase description from milestones.md and the Notes in progress.md).

Throughout all work:
- Follow all conventions in `.mfh/library/` (brand theme, code organization, centralization rules, backend patterns, notification components, data flow patterns)
- Follow the task order from the plan
- After completing each task, briefly note it so the user can track progress

**IMPORTANT**: This command is read-only with respect to MFH state files. Do not update progress.md or built.md during execution — that is done by `/mfh-update` and `/mfh-done`. However, if a non-obvious decision is made during work, append it to `.mfh/state/decisions.md` immediately so it isn't lost.
