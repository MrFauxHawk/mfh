---
description: Add a new milestone or phase to the project
---

# /mfh-newfeature

You are adding a new feature to the MFH planning system. Ask the user these questions in order:

1. "Are we adding a **new milestone**, or a **new phase to an existing milestone**?"

**If new milestone:**
- Ask: "What is the milestone name?"
- Ask: "What is a one-sentence description of what this milestone delivers?"
- Ask: "What phases does it include? List them (e.g. P1: Name — description)."
- Ask: "What status should it start with? (not started / in progress / complete)"

Then:
- Append the new milestone as a new row to the table in `.mfh/design/milestones.md`
- If it has a broader scope implication, add a note to the Goals section of `.mfh/design/roadmap.md`
- Confirm: "Added M# — [Name] to milestones.md with [N] phases."

**If new phase to existing milestone:**
- Ask: "Which milestone? (provide M# or name)"
- Ask: "What is the phase name and a one-sentence description?"
- Ask: "What status should it start with?"

Then:
- Find that milestone's row in `.mfh/design/milestones.md` and add the new phase to its Phases column
- Confirm: "Added P# — [Name] to M# in milestones.md."
