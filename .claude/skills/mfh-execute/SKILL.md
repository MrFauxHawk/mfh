---
description: Pick up active work and begin executing the plan
---

# /mfh-execute

You are picking up active work and executing it. This command accepts an optional argument (e.g. `/mfh-execute M4-P3`, `/mfh-execute WI-P5`, or `/mfh-execute EMP-P3`). This project has two tracks: **Milestones** (`M#-P#`) and, depending on which variant this project uses, either **Weekly Improvements** (`WI-P#`) or **App Backlogs** (`{PREFIX}-P#`, e.g. `EMP-P3`).

**Step 1 — Identify the phase:**
Read `.mfh/state/progress.md`.

- If a phase was provided as an argument (e.g. `M4-P3`, `WI-P5`, or `EMP-P3`), use that phase.
- If no argument and only one active phase exists, use that one.
- If no argument and multiple active phases exist, ask via `AskUserQuestion`: "Which phase do you want to work on?" — one option per active phase found in `progress.md` (its Other fallback covers anything not listed).

**Step 2 — Read the phase state:**
From the identified `## M#-P#`, `## WI-P#`, or `## {PREFIX}-P#` section, note:
- Track (Milestone, Weekly Improvements, or App Backlog) and Phase name
- Plan filename (if any)
- Status
- Notes (what has been done, what remains)

**Step 3 — Choose session mode (only if a plan already exists):**
If the phase's `Plan:` field is `(none)` or no plan file exists on disk, skip this step entirely — proceed to Step 4 and execute directly against the phase description (no question needed).

If a plan file does exist, ask via `AskUserQuestion`: "This phase has an active plan. What's this session's work?"
- **Continue the existing plan** (Recommended) — pick up the next unchecked task
- **Small ad-hoc work, no plan** — I'll do it and log it to `progress.md` when done, without touching the plan's tasks
- **New substantial scope** — hand off to `/mfh-plan` to add a new Round first

- **Continue the existing plan:** proceed normally through the rest of this skill, working the plan's unchecked tasks.
- **Small ad-hoc work, no plan:** proceed through Step 4 and Step 7 (still mark 🔄, still read library/decisions for context), but skip Step 9's task-checkbox tracking — do the user's described work directly in Step 9 without ticking plan tasks, and when done tell the user to run `/mfh-progress` to log it. Do not modify the plan file.
- **New substantial scope:** read the existing plan file first for context (prior rounds, decisions), then invoke `/mfh-plan` for this phase directly — do not ask the user to run it separately; the phase context carries over since it's the same conversation.

This question fires every time `/mfh-execute` is called on a phase with an active plan — including the first call right after that plan was created — and persists until `/mfh-done` closes the phase and deletes the plan file.

**Step 4 — Mark phase in progress in milestones.md:**
Read `.mfh/design/milestones.md`. Find the table row for the active phase — Milestone phase rows use a bare number (`| 🔄 3 |`), Weekly Improvement and App Backlog rows use the full phase identifier (`| 🔄 WI-P5 |`, `| 🔄 EMP-P3 |`). If it is not already marked `🔄`, update the status symbol to `🔄` now — before doing any other work. (This also correctly reverts a `🟡` phase back to `🔄` if work resumes on something previously marked ready-to-close.)

**Step 5 — Read plan (if one exists and session mode is "continue plan" or "ad-hoc"):**
If a plan file is referenced, read it from `.mfh/plans/`. In "ad-hoc" mode this is for context only — do not treat its tasks as this session's work.

**Step 6 — Read git log:**
Run `git log --oneline -20` to see recent commits. Cross-reference against the plan tasks and any Notes entries in progress.md. Committed work is authoritative — if a commit covers a plan task or describes work beyond the plan, treat it as done regardless of whether a checkbox is ticked or an `/mfh-progress` entry exists. Note any work done in commits that deviates from or extends the plan, so the summary in Step 8 reflects reality rather than the plan alone.

**Step 7 — Read library and decisions:**
Read all files in `.mfh/library/`. These contain the coding standards, conventions, and architectural rules you must follow throughout all work in this session.

Also read `.mfh/state/decisions.md`. Do not contradict or work around these decisions without discussing it first.

**Step 8 — Summarize context:**
Tell the user:
- What milestone and phase is being worked on
- What the goal is (from the plan or milestones.md)
- What has already been completed — prioritise git commits and Notes entries over unchecked plan tasks; if commits show work was done, it's done
- What remains to be done — only tasks not covered by commits or Notes

**Step 9 — Begin working:**

**If session mode is "continue plan" (or no plan exists at all):** execute the remaining tasks from the plan (or, if no plan, based on the phase description from milestones.md and the Notes in progress.md).

Throughout all work:
- Follow all conventions in `.mfh/library/`
- Follow the task order from the plan
- After completing each task:
  1. Briefly note it to the user
  2. Re-read `.mfh/state/progress.md` fresh from disk (don't rely on a copy read earlier in this conversation — a concurrent session may have modified it since), then find the matching `- [ ] N.` line under **Tasks:** and change it to `- [x] N.`

**Every 3 completed tasks, pause and ask** via `AskUserQuestion`: "That's [N] tasks done. What next?" — **Keep going** / **Run the TypeScript/ESLint check now** / **Save progress and stop (run `/mfh-progress` yourself)**.

Wait for the user's response before continuing. If they choose to stop, do not proceed to the next task — let them run `/mfh-progress` themselves. If they ask for the check, run **Step 10 — Verification** now, scoped to whatever's touched so far, then return to this same question rather than assuming they want to stop.

**When the last plan task is ticked** (whether or not it landed on a 3-task pause), ask once more via `AskUserQuestion`: "All plan tasks are done. Run the TypeScript/ESLint check now, or hold off in case you're adding more to this phase?" — **Run the check** / **Hold off**. Either way, tell them to run `/mfh-progress` when they're ready to log this.

Do not run Step 10 unless they choose to run the check here.

**If session mode is "ad-hoc, no plan":** execute the work the user actually asked for this session directly — it is not on the plan's task list, so there is nothing to tick off. Do not touch the plan file or its checkboxes. Pause for a check-in at a natural break point rather than every 3 tasks (there's no task list to count against) — ask via `AskUserQuestion`: "That's done. Run the TypeScript/ESLint check now, or hold off?" — **Run the check** / **Hold off**. Either way, tell them to run `/mfh-progress` when they're ready to log this.

If they choose to run the check, run **Step 10 — Verification**. Do not run it unprompted.

**Write rules during execution:**
- **DO** tick off task checkboxes as each task completes (change `[ ]` → `[x]`) — **only** in "continue plan" mode
- **DO** re-read `progress.md` fresh immediately before every write to it — a concurrent session may have changed it since your last read
- **DO** append to `.mfh/state/decisions.md` immediately when a non-obvious decision is made
- **DO** update `milestones.md` phase status to `🔄` at the start of Step 4 (one-time, if not already set)
- **DO NOT** modify `built.md` or any other state file — those are updated by `/mfh-progress` and `/mfh-done`
- **DO NOT** change the **Status**, **Notes**, or any other field in progress.md — only the task checkboxes, and only in "continue plan" mode

**Step 10 — Verification (on request only — never run automatically):**

This step never runs on its own, including when every task checkbox ends up ticked. It only runs when the user explicitly asks for it — either directly ("run the check", "run typescript/lint") or by choosing the check option at a 3-task pause point, an ad-hoc break point, or the final "all tasks done" check-in. The reason: verifying too eagerly means re-running it again after every small follow-up fix the user adds to the same phase — expensive and repetitive when the user already knows more work is coming. Let the user pick the moment.

When run, check whether the project uses TypeScript by looking for a `tsconfig.json` in the project root or in any directory touched during this phase. If none exists anywhere in the project, skip the TypeScript half entirely (but still run ESLint if applicable).

**TypeScript:**
Check `.mfh/library/` for a documented TypeScript check command specific to this project. If none is documented, fall back to running `npx tsc --noEmit` from the root (or from inside each touched directory for a monorepo).

**ESLint:**
Check `.mfh/library/` for a documented lint command specific to this project — do not assume it's broken or skip it without checking; confirm current status against the doc rather than a stale prior belief. If none is documented, fall back to running `next lint` per touched app (or the repo's own root-level lint script, e.g. via Turborepo, if one exists).

Identify touched areas from git:
```bash
git diff --name-only HEAD
```

Run both checks for each affected area. Interpret results:
- Errors in files you didn't touch = pre-existing, ignore them
- Errors in files you changed = must fix before reporting done — this includes lint-only errors (e.g. unused-var rules) that TypeScript's own compiler never flags but a production build may still enforce as a hard failure
- If no source files were touched (doc-only phase), skip this step

Report the result to the user: either "TypeScript and ESLint clean" or list the new errors to fix, broken out by which check caught them.
