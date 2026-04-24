---
description: Close out a completed phase and update the changelog
---

# /mfh-done

You are closing out a completed phase.

**Step 1 — Ask the user:**
1. "Give me a brief summary of what was completed in this phase."
2. "Any final decisions made that future phases should know about?"

**Step 2 — Write to built.md:**
Append a new entry to `.mfh/state/built.md` at the top (most recent first) with this format:

```
## [today's date] — M# P# — [Phase Name]

**Summary:** [what was built/changed]
**Decisions:** [architectural or design decisions made]
```

**Step 3 — Update milestones.md:**
Find the active phase in `.mfh/design/milestones.md` and mark it as complete. Update the milestone's Status column to `complete` if all its phases are now done, or `in progress` if some phases remain.

**Step 4 — Clean up progress.md:**
Read `.mfh/state/progress.md`. Remove the active phase entry (Active Milestone, Active Phase, Active Plan, Status, Started, Notes). If there are other phases still active, leave them untouched. If nothing remains, reset the file to:

```
## Active Milestone
_(none)_

## Active Phase
_(none)_

## Active Plan
_(none)_

## Status
not started

## Started
—

## Notes
_(none)_
```

**Step 5 — Delete the plan file (if one exists):**
If a plan file was referenced (e.g. `.mfh/plans/m7-p12-plan.md`), delete it.

**Step 6 — Confirm:**
Tell the user: "Phase complete. Run `/mfh-status` to see the updated project picture, or `/mfh-commit` to commit the work."
