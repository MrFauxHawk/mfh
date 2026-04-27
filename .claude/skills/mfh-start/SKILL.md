---
description: Start a new milestone and phase of work
---

# /mfh-start

You are starting a new phase of work.

**Step 1 — Ask the user:**
"Which milestone and phase do you want to work on? (e.g. M7-P2, or just describe it)"

**Step 2 — Read milestones.md:**
Read `.mfh/design/milestones.md`. Find the requested milestone and phase. Summarize what that phase involves based on its description.

**Step 3 — Check progress.md:**
Read `.mfh/state/progress.md`. If a section for this milestone + phase (e.g. `## M7-P2`) already exists, say:
"M7-P2 is already in progress. Run `/mfh-execute M7-P2` to continue working, or `/mfh-update M7-P2` to log what's been done."
Then stop.

**Step 4 — If not in progress, present the phase:**
Show the user:
- What the phase is
- What it involves (from `.mfh/design/milestones.md`)
- Any relevant context you can infer

Then ask: "Do you want to **create a plan** before starting, or just **get to work**?"

**If plan:**
Say: "Handing off to /mfh-plan." Then invoke `/mfh-plan` with the milestone and phase context already established.

**If no plan:**
Update the phase icon in `.mfh/design/milestones.md` from ⬜ to 🔄 for this phase.

Append a new section to `.mfh/state/progress.md`. If the file currently contains `_(no active phases)_`, replace that line with the new section. Otherwise append after the last `---` divider:

```
---

## M#-P#

**Milestone:** M# — [Milestone Name]
**Phase:** P# — [Phase Name]
**Plan:** (none)
**Status:** in progress (no plan)
**Started:** [today's date]
**Notes:**
_(none yet)_
```

Then tell the user: "M#-P# added to active work. Run `/mfh-execute M#-P#` when you're ready to begin."
