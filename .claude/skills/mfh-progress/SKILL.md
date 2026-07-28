---
description: Log progress, decisions, and what remains for the active phase
---

# /mfh-progress

You are logging a progress update for an active phase. This command accepts an optional argument (e.g. `/mfh-progress M4-P3`).

**Step 1 — Identify the phase:**
Read `.mfh/state/progress.md`.

- If a phase was provided as an argument (e.g. `M4-P3`), use that phase.
- If no argument and only one active phase exists, use that one.
- If no argument and multiple active phases exist, ask: "Which phase are you updating? (e.g. M4-P3)"

**Step 2 — Gather context automatically:**
Read the active plan file (e.g. `.mfh/plans/m{N}-p{N}-plan.md`) if one exists, and run `git log --oneline` to see commits since the last update date recorded in progress.md. Do NOT ask the user — derive the following:

- **Completed:** tasks checked off in the plan file, or commits made since the last update
- **Remaining:** unchecked tasks in the plan file, or planned work not yet reflected in commits
- **Decisions/Notes:** any notable patterns or blockers evident from the above (omit if nothing stands out)

**Step 3 — Append to the phase's Notes section:**
Re-read `.mfh/state/progress.md` fresh right before writing — don't rely on the copy read in Step 1 if any time has passed, since a concurrent session may have modified it since. Find the `## M#-P#` section. Append a new timestamped entry to its Notes field — never overwrite existing notes:

```
### Update — [today's date]
**Completed:** [what was done]
**Remaining:** [what's left]
**Decisions/Notes:** [any decisions or issues worth noting]
```

**Step 4 — Flag the phase as ready to close, if it is:**
If **Remaining** came out empty/none — every plan task is checked off (or there's no plan and the user's described work is simply done) — update this phase's status icon in `.mfh/design/milestones.md` from 🔄 to 🟡 (skip if it's already 🟡 or ✅). This is what makes "done but not yet closed" a real, visible status instead of something only mentioned in prose. If anything is still outstanding, leave the icon at 🔄.

**Step 5 — Confirm:**
Tell the user: "Progress updated. Run `/mfh-execute M#-P#` to jump back in next session." If the icon was flipped to 🟡, add: "This phase looks ready to close — run `/mfh-done M#-P#` whenever you're ready."
