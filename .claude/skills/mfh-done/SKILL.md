---
description: Close out a completed phase and update the changelog
---

# /mfh-done

You are closing out a completed phase. This command accepts an optional argument (e.g. `/mfh-done M4-P3`).

**Step 1 — Identify the phase:**
Read `.mfh/state/progress.md`.

- If a phase was provided as an argument (e.g. `M4-P3`), use that phase.
- If no argument and only one active phase exists, use that one.
- If no argument and multiple active phases exist, ask: "Which phase are you closing? (e.g. M4-P3)"

**Step 2 — Gather context automatically:**
Read `.mfh/state/progress.md`, `.mfh/design/milestones.md`, and `.mfh/state/decisions.md`. Do NOT ask the user for a summary or decisions — derive them from what is already recorded in these files.

**Step 3 — Write to built.md:**
Append a new entry to `.mfh/state/built.md` at the top (most recent first), using the phase description and any notes from progress.md and milestones.md:

```
## [today's date] — M# P# — [Phase Name]

**Summary:** [what was built/changed, derived from the phase notes and milestones]
**Decisions:** [any decisions recorded in decisions.md relevant to this phase, or "none" if none]
```

**Step 4 — Update milestones.md:**
Find the active phase in `.mfh/design/milestones.md` and change its icon from 🔄 or ⬜ to ✅. Update the milestone's overall status to `complete` if all its phases are done, or leave as `in progress` if some remain.

**Step 5 — Remove the phase from progress.md:**
Find the `## M#-P#` section and remove it entirely, including its preceding `---` divider.

If no other phases remain after removal, replace the entire file contents with:

```markdown
# Active Work

Tracks all currently active phases. Updated by `/mfh-start`, `/mfh-plan`, `/mfh-update`, and `/mfh-execute`. Entries removed by `/mfh-done` when a phase completes.

_(no active phases)_
```

**Step 6 — Delete the plan file (if one exists):**
If a plan file was referenced (e.g. `.mfh/plans/m4-p3-plan.md`), delete it.

**Step 7 — Confirm:**
Tell the user: "Phase complete. Run `/mfh-status` to see the updated project picture, or `/mfh-commit` to commit the work."
