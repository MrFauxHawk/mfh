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

Only one of the next two sections applies — check which second track `milestones.md` actually has (a `# Weekly Improvements` heading, or a `# App Backlogs` heading) and use only that one.

**Weekly Improvements** (non-monorepo projects) — show only open (⬜, 🔄, or 🟡) WI phases and the current position:

**Weekly Improvements**
*(Current Position line from milestones.md, verbatim)*
- 🟡 WI-P#: [Phase Name] — ready to close
- 🔄 WI-P#: [Phase Name]
- ⬜ WI-P#: [Phase Name]

**App Backlogs** (monorepo projects) — collect every incomplete phase (⬜, 🔄, or 🟡) across all app sections.

If the app tables have a **Priority** column, group by priority tier first, then by app within each tier — Critical, then High, then Medium, then Low, then a final "No Priority Set" tier for any incomplete phase whose table has no Priority column or an empty value. Skip a tier entirely if no incomplete phase carries that priority — do not print an empty tier header. Within a tier, order apps alphabetically by prefix and phases in file order:

**Critical**
- 🟡 {PREFIX}-P#: [Phase Name] ({App Name}) — ready to close
- 🔄 {PREFIX}-P#: [Phase Name] ({App Name})
- ⬜ {PREFIX}-P#: [Phase Name] ({App Name})

**High**
- ⬜ {PREFIX}-P#: [Phase Name] ({App Name})

*(continue for Medium, Low, No Priority Set — only the tiers that actually have phases in them)*

If the project's app tables have **no** Priority column at all, fall back to the original per-app grouping instead — show the section heading and only the incomplete phases:

**{PREFIX} — [App Name]**
- 🟡 {PREFIX}-P#: [Phase Name] — ready to close
- 🔄 {PREFIX}-P#: [Phase Name]
- ⬜ {PREFIX}-P#: [Phase Name]

Either way, list every qualifying phase — there can be many app sections; don't sample.

---

## Active Work

If progress.md has active phases, list each one:

**M#-P# — [Phase Name]** *(or WI-P# for Weekly Improvement phases, or {PREFIX}-P# for App Backlog phases)*
- Track: M# — [Milestone Name] *(or "Weekly Improvements", or "App Backlog — [App Name]")*
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

If the App Backlogs track is in play and has a Priority column, look at the highest-priority incomplete phase across all apps (Critical > High > Medium > Low > No Priority Set, file order as tiebreak within a tier) and suggest the next action. Otherwise (milestones, Weekly Improvements, or an App Backlog with no Priority column), look at the first incomplete phase in file order. If a phase is in progress, say so. Examples:
- "M2-P3 ([Phase Name]) is next. Run `/mfh-start M2-P3` to begin."
- "WI-P4 ([Phase Name]) is in progress. Run `/mfh-execute WI-P4` to continue."
- "EMP-P3 ([Phase Name]) is next. Run `/mfh-start EMP-P3` to begin."
If nothing is active: suggest starting the next incomplete phase.

---

**IMPORTANT**: This command is read-only. Do not write or modify any files.
