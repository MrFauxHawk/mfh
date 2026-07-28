---
description: Show a snapshot of all milestones, phases, and active work
---

# /mfh-status

Read `.mfh/design/milestones.md` and `.mfh/state/progress.md`. Then output a clean snapshot in this exact format:

---

## Project Status

**Active Milestones** — list only milestones that have at least one incomplete phase (⬜, 🔄, or 🟡). For each, show the milestone heading and only the incomplete phases:

**M# — [Milestone Name]**
- 🟡 P#: [Phase Name] — ready to close
- 🔄 P#: [Phase Name]
- ⬜ P#: [Phase Name]
*(Current Position line from milestones.md, verbatim)*

Skip completed milestones entirely — they are historical record, not active state.

---

**Weekly Improvements** — show only open (⬜, 🔄, or 🟡) WI phases and the current position:

**Weekly Improvements**
*(Current Position line from milestones.md, verbatim)*
- 🟡 WI-P#: [Phase Name] — ready to close
- 🔄 WI-P#: [Phase Name]
- ⬜ WI-P#: [Phase Name]

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

## Ready to Close

List every phase marked 🟡 across milestones, Weekly Improvements, and App Backlog sections, e.g.:
- M10-P9 — Opportunities Page Refactor: run `/mfh-done M10-P9`
- WI-P14 — [Phase Name]: run `/mfh-done WI-P14`

If there are none, say: "Nothing waiting to close." This section exists so a phase that's been implementation-complete for a while doesn't just sit there silently — surface it every time, not just the first time it goes 🟡.

---

## What to Do Next

Look at the first incomplete phase (milestone or WI) and suggest the next action. If a phase is in progress, say so. Examples:
- "M2-P3 ([Phase Name]) is next. Run `/mfh-start M2-P3` to begin."
- "WI-P4 ([Phase Name]) is in progress. Run `/mfh-execute WI-P4` to continue."
If nothing is active: suggest starting the next incomplete phase.

---

**IMPORTANT**: This command is read-only. Do not write or modify any files.
