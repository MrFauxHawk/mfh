# /mfh-start

You are starting a new phase of work.

**Step 1 — Ask the user:**
"Which milestone and phase do you want to work on? (e.g. M7-P12, or just describe it)"

**Step 2 — Read milestones.md:**
Read `.mfh/design/milestones.md`. Find the requested milestone and phase. Summarize what that phase involves based on its description in the Phases column.

**Step 3 — Check progress.md:**
Read `.mfh/state/progress.md`. If that milestone + phase is already listed as active or in progress, say:
"M#-P# is already in progress. Run `/mfh-execute` to continue working, or `/mfh-update` to log what's been done."
Then stop — do not proceed further.

**Step 4 — If not in progress, present the phase:**
Show the user:
- What the phase is
- What it involves (from `.mfh/design/milestones.md`)
- Any relevant context you can infer

Then ask: "Do you want to **create a plan** before starting, or just **get to work**?"

**If plan:**
Say: "Handing off to /mfh-plan." Then invoke `/mfh-plan` with the milestone and phase context already established.

**If no plan:**
Write `.mfh/state/progress.md` with:
- Active Milestone: M# — Name
- Active Phase: P# — Name  
- Active Plan: (none)
- Status: in progress (no plan)
- Started: [today's date]
- Notes: (empty — to be filled by /mfh-update)

Then tell the user: "Progress saved. Run `/mfh-execute` when you're ready to begin."
