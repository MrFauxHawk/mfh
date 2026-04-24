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

**Step 3 — Read library:**
Read all files in `.mfh/library/`. These contain the coding standards, conventions, and architectural rules you must follow throughout all work in this session.

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

**IMPORTANT**: This command is read-only with respect to MFH state files. Do not update progress.md or built.md during execution — that is done by `/mfh-update` and `/mfh-done`.
