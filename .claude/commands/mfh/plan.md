# /mfh-plan

You are creating an implementation plan for a phase that was just established by `/mfh-start`. The active milestone and phase context has been passed to you.

**Step 1 — Read context:**
Read the following files:
- `.mfh/design/roadmap.md` — project vision, tech stack, goals
- `.mfh/design/milestones.md` — milestone and phase details
- All files in `.mfh/library/` — coding standards and architectural rules that apply

**Step 2 — Draft a plan and present it to the user:**

The plan must include these sections:

### Goal
One sentence: what does this phase deliver when done?

### Relevant Library Context
Pull out the specific conventions, patterns, or architectural rules from `.mfh/library/` that apply to this phase. Don't dump everything — be selective and specific.

### Implementation Tasks
A numbered, step-by-step task list. Each step should be concrete enough to act on without further clarification.

### Verification Checklist
A short list of "how do I know this is done correctly?" checks. Include things like: UI tested in browser, API response verified, correct fallback behavior shown, code follows library conventions, no dead code, etc.

**Step 3 — Wait for approval:**
Present the plan and ask: "Does this plan look good, or would you like to make changes?"

- If the user approves: proceed to Step 4
- If the user requests changes: revise and re-present
- If the user rejects: ask what they want to do instead, then stop

**Step 4 — Save the approved plan:**
Write the plan to `.mfh/plans/m{N}-p{N}-plan.md` using the milestone and phase numbers.

Update `.mfh/state/progress.md` with:
- Active Milestone: M# — Name
- Active Phase: P# — Name
- Active Plan: m{N}-p{N}-plan.md
- Status: planned
- Started: [today's date]
- Notes: (empty)

Then tell the user exactly: "Plan saved. Clear this conversation and run `/mfh-execute` to begin work."
