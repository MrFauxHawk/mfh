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

**Check for an existing plan file** (`.mfh/plans/m{N}-p{N}-plan.md`, `wi-p{N}-plan.md`, or `{prefix}-p{N}-plan.md` for an App Backlog phase, e.g. `emp-p3-plan.md`) for this phase. If one exists, read it in full before drafting anything new — it may already contain a prior "Round" of work (goals, design decisions, tasks) that this new planning pass needs to build on rather than duplicate or contradict. This phase is being re-planned (reopened for a new round of substantial work), not planned from scratch.

**Step 2 — Ask the user what they expect:**
Before drafting anything, identify the active phase from `milestones.md` and ask the user one focused question:

> "I'm about to plan **[Phase name]**. What are your expectations or priorities for this phase — anything specific you want included, excluded, or approached differently?"

Wait for their response. Use it to shape the plan. Do not skip this step or assume you already know the answer from the milestone description alone.

**Step 3 — Draft a plan and present it to the user:**

The plan must include these sections:

### Plain-Language Summary
Write this section first, and write it for a reader who may have zero technical background and no familiarity with this codebase — a stakeholder, a new team member, anyone. 2–4 sentences, no jargon: no file paths, schema/model/variable names, function names, library names, or dev-process terms (migration, endpoint, refactor, etc.). Cover three things in plain terms:
1. What's true today — the problem, risk, or gap, described by its real-world effect (what goes wrong, what's confusing, what's fragile), not its code-level cause.
2. What's different once this phase ships.
3. Why it's worth doing.

If the phase has no user-visible change (pure internal cleanup, consolidation, technical debt), say that plainly rather than implying something will look or behave differently — then explain the risk it removes or the future mistake it prevents. Never let "refactor for cleanliness" or "improve code quality" stand alone as the reason; name the actual consequence being avoided (e.g., "two places list the same options, and when someone updates one but not the other, the dropdown quietly shows the wrong choices").

Bad (too technical, assumes the reader knows the codebase): "Consolidate duplicated `SCOPE_BADGE`/`CITIES` constants into a shared `lib/constants.ts` and retire orphaned `estimating_dropdown_options` categories."
Good (plain-language): "Right now, several dropdown menus in the Estimating app each keep their own separate copy of the same list of choices (cities, scope types, statuses). When one copy gets updated and the others don't, people see inconsistent options in different places without anyone noticing — and a few of those lists in the database aren't even used anymore, just sitting there as clutter. This phase makes every dropdown pull from one shared source, so they can never drift out of sync again, and removes the unused leftovers."

Follow the paragraph with a **"What's changing:"** bullet list — 3–6 items, same plain-language rules as the paragraph above (no file paths, model/variable names, or dev-process terms). Group by outcome/theme, not one bullet per Implementation Task — this list should never become a second copy of that task list. Each bullet names a real-world outcome (what will be different, what gets removed, what gets fixed), e.g. "Every dropdown that shows scope types, cities, or statuses will pull from one shared list instead of each page keeping its own separate copy," not "Refactor `constants.ts` to export `CITIES`/`SCOPE_TYPES`."

### Goal
One sentence: what does this phase deliver when done?

### Relevant Library Context
Pull out the specific conventions, patterns, or architectural rules from `.mfh/library/` that apply to this phase. Be selective — not everything.

### Implementation Tasks
A numbered, step-by-step task list. Each step concrete enough to act on without further clarification.

### Verification Checklist
"How do I know this is done correctly?" — UI tested in browser, API response verified, correct fallback behavior shown, code follows library conventions, no dead code, etc.

**Step 4 — Wait for approval:**
Present the plan and ask via `AskUserQuestion`: "Does this plan look good?" — **Approved** / **Request changes** / **Reject — I want something different**.

- If approved: proceed to Step 5
- If changes requested: revise and re-present
- If rejected: ask what they want instead, then stop

**Step 5 — Save the approved plan:**

**If no plan file existed yet for this phase** (first planning pass), write a new file:
- Milestone phase: `.mfh/plans/m{N}-p{N}-plan.md`
- Weekly Improvement phase: `.mfh/plans/wi-p{N}-plan.md`
- App Backlog phase: `.mfh/plans/{prefix}-p{N}-plan.md` (e.g. `emp-p3-plan.md`)

Find the corresponding `## M#-P#`, `## WI-P#`, or `## {PREFIX}-P#` section in `.mfh/state/progress.md` and update these fields:
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
- Append a new section to the *existing* plan file, titled `# [phase-id] Plan — Round N: [Round Title]` (e.g. `# M#-P# Plan`, `# WI-P# Plan`, or `# {PREFIX}-P# Plan`; N = next round number; the original untitled plan counts as Round 1), containing the same Plain-Language Summary / Goal / Relevant Library Context / Implementation Tasks / Verification Checklist structure — the summary should stand on its own for this round, not assume the reader already read Round 1.
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

For an **App Backlog phase**:
```
---

## {PREFIX}-P#

**Track:** App Backlog — [App Name] (`{PREFIX}`)
**Phase:** {PREFIX}-P# — [Phase Name]
**Plan:** {prefix}-p{N}-plan.md
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
