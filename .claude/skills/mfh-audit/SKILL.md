---
description: Retroactive sweep for stale library docs, orphaned plan files, and other drift MFH's day-to-day commands don't catch
---

# /mfh-audit

You are running a retroactive consistency sweep across the whole project — catching drift that's already accumulated, not just what changed in the current session. `/mfh-commit`'s own stale-doc check only looks at the diff about to be committed; this command looks backward across however much history the user wants covered.

This command is read-mostly and diagnostic-first: gather findings, present them grouped by category, and only act on what the user approves. Don't fix anything before Step 9.

**Step 1 — Establish scope:**
Ask via `AskUserQuestion`: "How far back should I look?" — **Since last week** / **Last 30 commits** / **Since the oldest open phase started** / **Entire project history** (its Other fallback covers a custom range).

If the user isn't sure, suggest a default based on `.mfh/state/built.md`'s most recent entry date, or the oldest **Started:** date among currently active phases in `.mfh/state/progress.md` — whichever is more recent, since that bounds "things that could plausibly have drifted since the last known-good checkpoint."

**Step 2 — Gather context:**
Read `.mfh/design/roadmap.md`, `.mfh/design/milestones.md`, `.mfh/state/progress.md`, `.mfh/state/decisions.md`, `.mfh/state/built.md`, every file in `.mfh/library/`, and list `.mfh/plans/`. Run `git log` over the scoped range (`--stat` or `--name-only` so file paths are visible) to see what actually changed.

**Step 3 — Check for stale library docs:**
For each file in `.mfh/library/`, note what area or concern it documents. Cross-reference against the files touched in the scoped git history. Flag a likely gap when a commit clearly touches an area a doc describes (schema, routes, shared components, conventions) but that doc's own last-touched commit is well before it — a new API route with nothing added to a routes/architecture doc, a schema field with the database doc untouched, a new shared component absent from a components doc. Use judgment, not exhaustiveness — this is the same check `/mfh-commit` runs on a single diff, just over a wider window, so the same "plausible gap, not everything adjacent" bar applies.

**Step 4 — Check for orphaned or stray plan files:**
List every file in `.mfh/plans/`. For each, check whether it matches the expected naming convention for an active phase (`m{N}-p{N}-plan.md`, `wi-p{N}-plan.md`, `{prefix}-p{N}-plan.md`) **and** whether that phase still has an open section in `progress.md`. Flag any plan file that:
- Doesn't match any currently active phase (the phase it belonged to was likely already closed by `/mfh-done`, or never matched the naming convention `/mfh-done` looks for and so was never cleaned up)
- Doesn't match the naming convention at all (typo, manual copy, one-off)

**Step 5 — Check for stuck "ready to close" phases and unresolved verification:**
Scan each active phase's Notes in `progress.md` for repeated language indicating the work is done and just awaiting closure (e.g. "ready to close," "ready for `/mfh-done`," "phase ready for commit") across multiple dated updates without the phase ever actually being closed. Flag these explicitly — this is exactly the kind of thing that sits for weeks because nothing forces the closing step. Also check each phase's plan file for an unchecked Verification Checklist on a phase whose tasks are otherwise all checked off — flag that too, since it means the described "done" state was never actually confirmed.

**Step 6 — Check decisions.md references:**
Grep `progress.md` and `built.md` for references to `decisions.md` (typically "See `decisions.md` → \"...\""). For each, confirm a matching heading/entry actually exists in `decisions.md`. Flag any reference that doesn't resolve — it means either the entry was never written or was written under a different title than what's referenced.

**Step 7 — Check roadmap.md for drift:**
Compare `roadmap.md`'s **Current Track**/**Current Focus** against what `milestones.md` actually shows as active — flag it if roadmap.md still names a milestone that's already fully shipped and moved to Completed Milestones, or doesn't mention a milestone that's clearly the active one. Check the **Goals** list for anything that reads as already achieved (cross-reference against `built.md`) but is still phrased as aspirational. Check whether **Live Sections** (if present) is missing an app that a completed milestone shipped. This is the same category of drift as library docs, just at the project-vision level instead of the code level.

**Step 8 — Present findings:**
Group findings under clear headers (Stale Library Docs / Orphaned Plan Files / Stuck Phases / Unresolved Verification / Broken Decision References / Roadmap Drift). For each finding, state what you found and why it's flagged — not just a bare file list. If a category has nothing to report, say so briefly rather than omitting it silently (an empty audit result should read as "checked and clean," not "not checked").

Ask via `AskUserQuestion` (multi-select): "Want me to fix any of these now?" — one option per category that actually has findings, plus a **Fix everything** option and a **Fix nothing, just wanted the report** option — let the user pick individual categories rather than assuming all-or-nothing.

**Step 9 — Apply approved fixes:**
For each approved finding:
- Stale library doc → make the edit, following the compact-header convention where applicable
- Orphaned plan file → confirm before deleting (it may represent real unfinished work, not just cleanup)
- Stuck phase → don't force-close it yourself; tell the user to run `/mfh-done [phase]` once they confirm it's actually ready
- Unresolved verification → don't check items off on the user's behalf; surface it so they can walk it live or explicitly waive it
- Broken decision reference → either write the missing `decisions.md` entry (if the rationale is recoverable from context) or correct the reference
- Roadmap drift → make the edit directly (this is the same kind of update `/mfh-done` already makes automatically when a milestone completes — you're just catching up on one it missed)

Report what was fixed and what was left for the user to handle themselves.
