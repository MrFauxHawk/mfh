---
description: Log progress, decisions, and what remains for the active phase
---

# /mfh-update

You are logging a progress update for an active phase. This command accepts an optional argument (e.g. `/mfh-update M4-P3`).

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
Find the `## M#-P#` section in `.mfh/state/progress.md`. Append a new timestamped entry to its Notes field — never overwrite existing notes:

```
### Update — [today's date]
**Completed:** [what was done]
**Remaining:** [what's left]
**Decisions/Notes:** [any decisions or issues worth noting]
```

**Step 4 — Confirm:**
Tell the user: "Progress updated. Run `/mfh-execute M#-P#` to jump back in next session."
