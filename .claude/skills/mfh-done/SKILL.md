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
Append a new entry to `.mfh/state/built.md` at the top (most recent first), using the phase description and any notes from progress.md and milestones.md.

For a **Milestone phase**:
```
## [today's date] — M# P# — [Phase Name]

**Summary:** [what was built/changed, derived from the phase notes and milestones]
**Decisions:** [any decisions recorded in decisions.md relevant to this phase, or "none" if none]
```

For a **Weekly Improvement phase**:
```
## [today's date] — WI-P# — [Phase Name]

**Summary:** [what was built/changed, derived from the phase notes and milestones]
**Decisions:** [any decisions recorded in decisions.md relevant to this phase, or "none" if none]
```

**Step 4 — Update milestones.md:**
Find the active phase in `.mfh/design/milestones.md` and change its icon from 🔄 or ⬜ to ✅.

**For Milestone phases:**
- Update the milestone's `### Current Position:` line to reflect the newly completed phase (e.g. "P1–P3 complete — P4 next" or "All phases complete").
- If ALL phases in the milestone are now ✅, move the entire milestone block (heading, Goal, planning decisions, and phase table) to the **Completed Milestones** section at the bottom of the file, inserting it in numbered order (M1, M2, M3…). Do not move partial milestones.
- If ALL phases are now ✅, also update `.mfh/design/roadmap.md`:
  - If the milestone introduced a new app/section, add it to the **Live Sections** table.
  - Update **Current Track** or **Current Focus** to reflect what's active next.
  - Move the milestone out of **Next Up** / **Upcoming** if it was listed there.
  - If no next milestone is planned, set Current Track to "Weekly Improvements — Continuous rolling backlog."

**For WI phases:**
- Update the `### Current Position:` line in the Weekly Improvements section to name the newly completed phase and what's next (e.g. "WI-P13 complete — WI-P14 in progress" or "WI-P14 complete — WI-P15 next").

**Step 5 — Remove the phase from progress.md:**
Find the `## M#-P#` or `## WI-P#` section and remove it entirely, including its preceding `---` divider.

If no other phases remain after removal, replace the entire file contents with:

```markdown
# Active Work

Tracks all currently active phases. Updated by `/mfh-start`, `/mfh-plan`, `/mfh-update`, and `/mfh-execute`. Entries removed by `/mfh-done` when a phase completes.

_(no active phases)_
```

**Step 6 — Delete the plan file (if one exists):**
If a plan file was referenced, delete it. Milestone plans follow `m{N}-p{N}-plan.md` naming; WI plans follow `wi-p{N}-plan.md` naming.

**Step 7 — Confirm:**
Tell the user: "Phase complete. Run `/mfh-status` to see the updated project picture, or `/mfh-commit` to commit the work."
