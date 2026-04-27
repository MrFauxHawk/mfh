---
description: Create an implementation plan for the active phase
---

# /mfh-plan

You are creating an implementation plan for a phase established by `/mfh-start`. The active milestone and phase context has been passed to you.

**Step 1 — Read context:**
Read the following files:
- `.mfh/design/roadmap.md` — project vision, tech stack, goals
- `.mfh/design/milestones.md` — milestone and phase details
- All files in `.mfh/library/` — coding standards and architectural rules
- `.mfh/state/decisions.md` — review past decisions; don't contradict them without discussion

**Step 2 — Draft a plan and present it to the user:**

The plan must include these sections:

### Goal
One sentence: what does this phase deliver when done?

### Relevant Library Context
Pull out the specific conventions, patterns, or architectural rules from `.mfh/library/` that apply to this phase. Be selective — not everything.

### Implementation Tasks
A numbered, step-by-step task list. Each step concrete enough to act on without further clarification.

### Verification Checklist
"How do I know this is done correctly?" — UI tested in browser, API response verified, correct fallback behavior shown, code follows library conventions, no dead code, etc.

**Step 3 — Wait for approval:**
Present the plan and ask: "Does this plan look good, or would you like to make changes?"

- If approved: proceed to Step 4
- If changes requested: revise and re-present
- If rejected: ask what they want instead, then stop

**Step 4 — Save the approved plan:**
Write the plan to `.mfh/plans/m{N}-p{N}-plan.md`.

Find the corresponding `## M#-P#` section in `.mfh/state/progress.md` and update these fields:
- **Plan:** m{N}-p{N}-plan.md
- **Status:** planned
- **Started:** [today's date] (if not already set)

If the section doesn't exist yet (plan created before `/mfh-start`), append a new section:

```
---

## M#-P#

**Milestone:** M# — [Milestone Name]
**Phase:** P# — [Phase Name]
**Plan:** m{N}-p{N}-plan.md
**Status:** planned
**Started:** [today's date]
**Notes:**
_(none yet)_
```

If the file currently contains `_(no active phases)_`, replace that line with the new section.

Then tell the user: "Plan saved. Clear this conversation and run `/mfh-execute M#-P#` to begin work."
