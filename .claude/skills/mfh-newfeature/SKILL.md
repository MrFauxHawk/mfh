---
description: Add a new milestone or phase to the project
---

# /mfh-newfeature

You are adding a new feature to the MFH planning system. Ask the user these questions in order:

1. "Are we adding a **new milestone**, a **new phase to an existing milestone**, or a **new Weekly Improvement phase** (WI-P#)?"

**Phase table format:** Icons go before the phase number — no separate Status column. Use `⬜ N` for not started, `🔄 N` for in progress, `✅ N` for complete.

```
| Phase | Description |
|-------|-------------|
| ⬜ 1 | [Phase description] |
```

**If new milestone:**
- Ask: "What is the milestone name?"
- Ask: "What is a one-sentence description of what this milestone delivers?"
- Ask: "What phases does it include? List them (e.g. P1: Name — description)."

Then:
- Insert the new milestone block into `.mfh/design/milestones.md` **before** the Weekly Improvements section (active milestones come first; completed milestones go at the bottom). Include a `### Current Position:` line set to "P1 next — not started".
- All phases start as ⬜ in the table.
- If it has a broader scope implication, add a note to the Goals section of `.mfh/design/roadmap.md`
- Confirm: "Added M# — [Name] to milestones.md with [N] phases."

**If new phase to existing milestone:**
- Ask: "Which milestone? (provide M# or name)"
- Ask: "What is the phase name and a one-sentence description?"

Then:
- Add the new phase row to that milestone's table in `.mfh/design/milestones.md` using `⬜ N` format
- Confirm: "Added P# — [Name] to M# in milestones.md."

**If new Weekly Improvement phase:**
- Ask: "What is a one-sentence description of this improvement?"

Then:
- Read the Weekly Improvements table in `.mfh/design/milestones.md` to find the next WI-P# number
- Add the new phase row to the Weekly Improvements table using `⬜ WI-P#` format
- Update the "Current Position" line in the Weekly Improvements section to reflect the new phase
- Confirm: "Added WI-P# — [description] to the Weekly Improvements track."
