---
description: Close out a completed phase and update the changelog
---

# /mfh-done

You are closing out a completed phase. This command accepts an optional argument (e.g. `/mfh-done M4-P3`).

**Before Step 1 — Check whether this is a completion or a cancellation:**
`/mfh-done` closes a phase as **completed** by default. If the user's request indicates the phase is being cancelled/abandoned/scrapped instead (cut before finishing, decided against, no longer needed) — either stated directly or clear from context — follow the **Cancellation path** below instead of Steps 1–7. If it's ambiguous, ask: "Is this phase actually complete, or are we cancelling it?"

**Cancellation path (instead of Steps 1–7):**
1. Identify the phase (same lookup as Step 1 below).
2. If not already given, ask: "What's the reason for cancelling this phase?" — keep it brief.
3. Append a short entry to `.mfh/state/decisions.md` noting the phase was cancelled and why. This is a decision record, not a shipped-feature record — do **not** write anything to `built.md`, since nothing was built.
4. In `.mfh/design/milestones.md`, change the phase's icon to `❌` (not `✅`). For Milestone phases, don't touch the `### Current Position:` line the way a completion would (a cancelled phase isn't "next" or "complete" — leave the line as-is unless the user wants it reworded). Don't trigger the "all phases ✅" milestone-completion logic from Step 4 below — a milestone with a `❌` phase isn't fully shipped, but also isn't blocked from ever being considered done; use judgment, or ask the user if unsure.
5. Remove the phase from `.mfh/state/progress.md` (same as Step 5 below).
6. Delete the plan file if one exists (same as Step 6 below).
7. Confirm: "Phase cancelled and logged in decisions.md. Run `/mfh-status` to see the updated picture."

**Step 1 — Identify the phase:**
Read `.mfh/state/progress.md`.

- If a phase was provided as an argument (e.g. `M4-P3`), use that phase.
- If no argument and only one active phase exists, use that one.
- If no argument and multiple active phases exist, ask: "Which phase are you closing? (e.g. M4-P3)"

**Step 2 — Gather context automatically:**
Read `.mfh/state/progress.md`, `.mfh/design/milestones.md`, and `.mfh/state/decisions.md`. Do NOT ask the user for a summary or decisions — derive them from what is already recorded in these files.

**Step 2b — Check for unresolved verification:**
If a plan file is referenced for this phase, read it and check for a Verification Checklist section. If it contains any unchecked `- [ ]` items, tell the user which ones and ask: "This phase's Verification Checklist has unchecked items — [list them]. Close anyway, or walk through them first?" Proceed to Step 3 only once they've answered — don't silently close over an unverified checklist, and don't check items off on their behalf.

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

For an **App Backlog phase** (PREFIX-P# format, e.g. `EMP-P3`):
```
## [today's date] — [PREFIX]-P# — [Phase Name]

**Summary:** [what was built/changed, derived from the phase notes and milestones]
**Decisions:** [any decisions recorded in decisions.md relevant to this phase, or "none" if none]
```

**Step 4 — Update milestones.md:**
Find the active phase in `.mfh/design/milestones.md` and change its icon from 🔄, 🟡, or ⬜ to ✅.

**For Milestone phases:**
- Update the milestone's `### Current Position:` line to reflect the newly completed phase (e.g. "P1–P3 complete — P4 next" or "All phases complete").
- If every phase in the milestone is now ✅ or ❌ (nothing left ⬜, 🔄, or 🟡), move the entire milestone block (heading, Goal, planning decisions, and phase table) to the **Completed Milestones** section at the bottom of the file, inserting it in numbered order (M1, M2, M3…). Do not move partial milestones.
- Under the same condition, also update `.mfh/design/roadmap.md`:
  - If the milestone introduced a new app/section, add it to the **Live Sections** table.
  - Update **Current Track** or **Current Focus** to reflect what's active next.
  - Move the milestone out of **Next Up** / **Upcoming** if it was listed there.
  - If no next milestone is planned, set Current Track to "Weekly Improvements — Continuous rolling backlog" (non-monorepo) or "App Backlogs — Continuous per-app improvement tracks" (monorepo).
- Under the same condition, if `milestones.md` contains an `# App Backlogs` section (monorepo project), ask: "Did this milestone ship a new app? If yes, what's the app name and its 3-letter prefix?" If the user provides one, insert a new app backlog section into `milestones.md` immediately before the PLT section:
  ```
  ## [PREFIX] — [App Name]

  | Phase | Description |
  |-------|-------------|

  ---
  ```

**For WI phases:**
- Update the `### Current Position:` line in the Weekly Improvements section to name the newly completed phase and what's next (e.g. "WI-P13 complete — WI-P14 in progress" or "WI-P14 complete — WI-P15 next").

**For App Backlog phases:**
- Mark the phase ✅ in its app section. No `Current Position` line to update — the emoji status is self-documenting.

**Step 5 — Remove the phase from progress.md:**
Find the `## M#-P#`, `## WI-P#`, or `## {PREFIX}-P#` section and remove it entirely, including its preceding `---` divider.

If no other phases remain after removal, replace the entire file contents with:

```markdown
# Active Work

Tracks all currently active phases. Updated by `/mfh-start`, `/mfh-plan`, `/mfh-update`, and `/mfh-execute`. Entries removed by `/mfh-done` when a phase completes.

_(no active phases)_
```

**Step 6 — Delete the plan file (if one exists):**
If a plan file was referenced, delete it. Milestone plans follow `m{N}-p{N}-plan.md` naming; WI plans follow `wi-p{N}-plan.md` naming; App Backlog plans follow `{prefix}-p{N}-plan.md` naming (e.g. `emp-p3-plan.md`).

**Step 7 — Confirm:**
Tell the user: "Phase complete. Run `/mfh-status` to see the updated project picture, or `/mfh-commit` to commit the work."
