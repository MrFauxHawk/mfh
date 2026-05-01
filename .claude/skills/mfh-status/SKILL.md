---
description: Show a snapshot of all milestones, phases, and active work
---

# /mfh-status

Read `.mfh/design/milestones.md` and `.mfh/state/progress.md`. Then output a clean snapshot in this exact format:

---

## Project Status

For each **Milestone** in milestones.md, list it with its status and all phases, like this:

**M1 — Foundation** `complete`
- P1: Repo Setup ✅
- P2: useSleeperPlayers ✅
- P3: Site Shell ✅

Use ✅ for complete, 🔄 for in progress, ⬜ for not started.

Then list the **Weekly Improvements** track separately:

**Weekly Improvements**
- WI-P1: [Description] ✅
- WI-P2: [Description] 🔄

---

## Active Work

If progress.md has active phases, list each one:

**M#-P# — [Phase Name]** *(or WI-P# for Weekly Improvement phases)*
- Track: M# — [Milestone Name] *(or "Weekly Improvements")*
- Plan: [filename or "no plan"]
- Status: [status]
- Started: [date]

List all active phases. If nothing is active, show: "Nothing currently in progress."

---

## What to Do Next

Look at the first incomplete phase (milestone or WI) and suggest the next action. If a phase is in progress, say so. Examples:
- "M4-P3 (Gantt) is in progress. Run `/mfh-execute M4-P3` to continue."
- "WI-P5 (Safety Extras) is next. Run `/mfh-start` to begin WI-P5."
If nothing is active: suggest starting the next incomplete phase.

---

**IMPORTANT**: This command is read-only. Do not write or modify any files.
