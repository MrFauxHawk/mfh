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

**Check for an existing plan file** (`.mfh/plans/m{N}-p{N}-plan.md` or `wi-p{N}-plan.md`) for this phase. If one exists, read it in full before drafting anything new — it may already contain a prior "Round" of work (goals, design decisions, tasks) that this new planning pass needs to build on rather than duplicate or contradict. This phase is being re-planned (reopened for a new round of substantial work), not planned from scratch.

**Step 2 — Ask the user what they expect:**
Before drafting anything, identify the active phase from `milestones.md` and ask the user one focused question:

> "I'm about to plan **[Phase name]**. What are your expectations or priorities for this phase — anything specific you want included, excluded, or approached differently?"

Wait for their response. Use it to shape the plan. Do not skip this step or assume you already know the answer from the milestone description alone.

**Step 3 — Draft a plan and present it to the user:**

The plan must include these sections:

### Goal
One sentence: what does this phase deliver when done?

### Relevant Library Context
Pull out the specific conventions, patterns, or architectural rules from `.mfh/library/` that apply to this phase. Be selective — not everything.

### Implementation Tasks
A numbered, step-by-step task list. Each step concrete enough to act on without further clarification.

### Verification Checklist
"How do I know this is done correctly?" — UI tested in browser, API response verified, correct fallback behavior shown, code follows library conventions, no dead code, etc.

**Step 4 — Wait for approval:**
Present the plan and ask: "Does this plan look good, or would you like to make changes?"

- If approved: proceed to Step 4
- If changes requested: revise and re-present
- If rejected: ask what they want instead, then stop

**Step 5 — Save the approved plan:**

**If no plan file existed yet for this phase** (first planning pass), write a new file:
- Milestone phase: `.mfh/plans/m{N}-p{N}-plan.md`
- Weekly Improvement phase: `.mfh/plans/wi-p{N}-plan.md`

Find the corresponding `## M#-P#` or `## WI-P#` section in `.mfh/state/progress.md` and update these fields:
- **Plan:** [plan filename]
- **Status:** planned
- **Started:** [today's date] (if not already set)
- **Tasks:** a checkbox list — one line per Implementation Task from the plan, in order:
  ```
  - [ ] 1. Brief task title
  - [ ] 2. Brief task title
  …
  ```
  Keep each title short (≤ 60 chars). This list is the live progress tracker; `/mfh-execute` will tick items off as they complete.
- **Notes:**
  _(none yet)_

If the section doesn't exist yet (plan created before `/mfh-start`), append a new section.

**If a plan file already existed for this phase** (re-planned mid-phase — this is Round 2 or later): do not overwrite the file or touch the original **Plan:**, **Status:**, **Started:**, or top-level **Tasks:** fields in `progress.md` — those still reflect the first round. Instead:
- Append a new section to the *existing* plan file, titled `# M#-P# Plan — Round N: [Round Title]` (N = next round number; the original untitled plan counts as Round 1), containing the same Goal / Relevant Library Context / Implementation Tasks / Verification Checklist structure.
- Append a new block to that phase's **Notes** field in `progress.md`:
  ```
  ### Round N — [Round Title] (planned [today's date])
  **Goal:** [one sentence]
  **Tasks:**
  - [ ] 1. Brief task title
  - [ ] 2. Brief task title
  …
  ```
  This keeps every round's history in one file per phase (gitignored, deleted only when `/mfh-done` closes the phase) instead of spawning a new plan file per round.

For a **Milestone phase**:
```
---

## M#-P#

**Milestone:** M# — [Milestone Name]
**Phase:** P# — [Phase Name]
**Plan:** m{N}-p{N}-plan.md
**Status:** planned
**Started:** [today's date]
**Tasks:**
- [ ] 1. Brief task title
- [ ] 2. Brief task title
**Notes:**
_(none yet)_
```

For a **Weekly Improvement phase**:
```
---

## WI-P#

**Track:** Weekly Improvements
**Phase:** WI-P# — [Phase Name]
**Plan:** wi-p{N}-plan.md
**Status:** planned
**Started:** [today's date]
**Tasks:**
- [ ] 1. Brief task title
- [ ] 2. Brief task title
**Notes:**
_(none yet)_
```

If the file currently contains `_(no active phases)_`, replace that line with the new section.

Then tell the user: "Plan saved. Clear this conversation and run `/mfh-execute [phase]` to begin work."
