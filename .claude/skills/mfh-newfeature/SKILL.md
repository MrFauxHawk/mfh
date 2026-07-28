---
description: Add a new milestone or phase to the project
---

# /mfh-newfeature

You are adding a new feature to the MFH planning system. First check `.mfh/design/milestones.md` for which second track this project uses — **Weekly Improvements** (`WI-P#`, non-monorepo) or **App Backlogs** (`{PREFIX}-P#`, monorepo, one section per app). Then ask the user these questions in order, using whichever option set matches:

**Non-monorepo:** "Are we adding a **new milestone**, a **new phase to an existing milestone**, or a **new Weekly Improvement phase** (`WI-P#`)?"

**Monorepo:** "Are we adding a **new milestone**, a **new phase to an existing milestone**, a **new phase to an existing App Backlog** (`{PREFIX}-P#`), or a **new App Backlog section** for a brand-new app?"

**Phase table format:** Icons go before the phase number — no separate Status column. Use `⬜ N` for not started, `🔄 N` for in progress, `🟡 N` for implementation done and awaiting `/mfh-done`, `✅ N` for complete, `❌ N` for cancelled. Milestone phase rows use a bare number in the cell; Weekly Improvement and App Backlog rows use the full phase identifier instead (see their examples below).

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
- Insert the new milestone block into `.mfh/design/milestones.md` **before** the Weekly Improvements or App Backlogs section, whichever this project has (active milestones come first; completed milestones go at the bottom). Include a `### Current Position:` line set to "P1 next — not started".
- All phases start as ⬜ in the table.
- Update `.mfh/design/roadmap.md`:
  - If no milestone is currently active (Current Track = Weekly Improvements or App Backlogs), replace Current Track with a **Current Focus** section naming this milestone.
  - If a milestone is already active, add this one under a **Next Up** section.
  - If the milestone will introduce a new app/section, note it in **Upcoming / Planned** until it ships.
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

**If new phase to existing App Backlog:**
- Ask: "Which app backlog? (provide the 3-letter prefix, e.g. EMP)"
- Ask: "What is the phase name and a one-sentence description?"

Then:
- Read that app's table in `.mfh/design/milestones.md` to find the next `{PREFIX}-P#` number
- Add the new phase row to that app's table using `⬜ {PREFIX}-P#` format (the full identifier goes in the cell, same as Weekly Improvement rows)
- Confirm: "Added {PREFIX}-P# — [Name] to the {PREFIX} backlog."

**If new App Backlog section:**
- Ask: "What is the app name and its 3-letter prefix?"

Then:
- Insert a new section into `.mfh/design/milestones.md`'s App Backlogs area, immediately before the PLT section (matching the convention `/mfh-done` already uses when a milestone ships a new app), with an empty phase table:
  ```
  ## {PREFIX} — [App Name]

  | Phase | Description |
  |-------|-------------|

  ---
  ```
- Ask: "Should `git.md`'s Scopes list also include `{prefix}` (lowercase)?" If yes, append it there.
- Confirm: "Added new App Backlog section: {PREFIX} — [App Name]."
