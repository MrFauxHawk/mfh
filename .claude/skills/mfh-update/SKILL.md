---
description: Log progress, decisions, and what remains for the active phase
---

# /mfh-update

You are logging a progress update for the current active phase.

**Step 1 — Ask the user:**
1. "What has been completed since the last update?"
2. "What is still remaining?"
3. "Any decisions made or issues encountered worth noting?"

**Step 2 — Read the current progress.md:**
Read `.mfh/state/progress.md` to see existing notes. You must append to them — never overwrite.

**Step 3 — Update the Notes section:**
Append a new timestamped entry to the Notes section of `.mfh/state/progress.md` in this format:

```
### Update — [today's date]
**Completed:** [what was done]
**Remaining:** [what's left]
**Decisions/Notes:** [any decisions made or issues worth noting]
```

Preserve all previous notes above the new entry. The Notes section should be a running log.

**Step 4 — Confirm:**
Tell the user: "Progress updated. Run `/mfh-execute` to jump back in next session."
