---
description: Show a snapshot of all milestones, phases, and active work
---

# /mfh-status

Read `.mfh/design/milestones.md` and `.mfh/state/progress.md`. Then output a clean snapshot in this exact format:

---

## Project Status

For each milestone in milestones.md, list it with its status and all phases, like this:

**M1 — Foundation** `complete`
- P1: Repo Setup ✅
- P2: useSleeperPlayers ✅
- P3: Site Shell ✅

Use ✅ for complete, 🔄 for in progress, ⬜ for not started.

After listing all milestones, output:

---

## Active Work

If progress.md has active phases, list each one:

**M#-P# — [Phase Name]**
- Milestone: M# — [Milestone Name]
- Plan: [filename or "no plan"]
- Status: [status]
- Started: [date]

List all active phases. If nothing is active, show: "Nothing currently in progress."

---

## What to Do Next

Look at the first milestone that is not complete and suggest the next action. If a phase is in progress, say so. Example:
"M4-P3 (Gantt) is in progress. Run `/mfh-execute M4-P3` to continue."
If nothing is active: "M# (Name) is next. Run `/mfh-start` to begin M#-P#."

---

**IMPORTANT**: This command is read-only. Do not write or modify any files.
