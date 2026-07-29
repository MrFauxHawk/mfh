# MFH — Planning & State Management for Claude Code

A lightweight planning system for software projects built with [Claude Code](https://claude.ai/code). Uses slash commands to manage milestones, phases, plans, and progress across sessions.

---

## How it works

MFH has two parts:

- **Skills** — Claude Code slash commands installed once at the user level (`~/.claude/skills/`). Available in every project automatically.
- **Project structure** — The `.mfh/` folder inside each project, scaffolded by `/mfh-init`.

Wherever a skill hits a decision point with a small fixed set of outcomes (approve a plan, pick a phase, choose a session mode), it asks via a selectable prompt rather than expecting you to type the answer back — free text is reserved for genuinely open-ended questions (names, descriptions, priorities) where there's nothing sensible to select from.

---

## Setup

### 1. Install the skills (once per machine)

Clone this repo and copy the skills to your user-level Claude directory:

```bash
git clone https://github.com/MrFauxHawk/mfh.git ~/mfh
cp -r ~/mfh/.claude/skills/* ~/.claude/skills/
```

To update skills in the future:

```bash
cd ~/mfh && git pull && cp -r .claude/skills/* ~/.claude/skills/
```

### 2. Initialize a project

Open any project in Claude Code and run:

```
/mfh-init
```

This asks for your project name, the main modules or areas of your codebase, and whether the project has a database/auth/roles, then scaffolds the full `.mfh/` folder structure — including `library/git.md` pre-populated with your commit scopes, plus empty starter docs for the rest of `.mfh/library/` (see [What goes in `.mfh/library/`](#what-goes-in-mfhlibrary) below). It also adds the required `.gitignore` entries.

### 3. Seed your project

Fill in:
- `.mfh/design/roadmap.md` — your project vision, tech stack, and goals
- `.mfh/design/milestones.md` — your milestone and phase breakdown
- `.mfh/library/git.md` — review and add your branch rules (scopes are pre-filled by init)

The rest of `.mfh/library/`'s starter docs (`style.md`, `architecture.md`, etc.) can stay empty for now — fill them in as the project actually takes shape, there's no rush.

Then run `/mfh-start` to begin your first phase.

---

## Project structure

```
.mfh/
  design/
    roadmap.md        ← vision, tech stack, goals (git-tracked)
    milestones.md     ← milestone + phase breakdown (git-tracked)
  library/            ← coding standards, conventions, architecture rules (git-tracked)
  plans/              ← active plan files, one per phase (gitignored)
  state/
    progress.md       ← all active phases (gitignored)
    built.md          ← permanent changelog (git-tracked)
    decisions.md      ← non-obvious decisions and rationale (git-tracked)
  updates/            ← optional; created by /mfh-update on first run
    last-run.md       ← last-used profile selection + color source/mode, offered as a quick-repeat default next run
    {profile}/        ← one folder per audience profile (e.g. mine/, users/, stakeholders/)
      latest.md       ← that profile's latest update (source of truth)
      latest.html     ← rendered, friendly-viewable HTML version
      history/        ← dated archive of that profile's past updates (both formats)
```

---

## Three tracks

MFH supports three parallel planning tracks:

**Milestones** — named delivery cycles with a fixed goal and ship date. Numbered `M1, M2, …`. Phases within a milestone are `P1, P2, …`. Example: `M4-P2`.

**Weekly Improvements** — a continuous rolling backlog with no end date. Phases use the `WI-P#` prefix (e.g. `WI-P4`). New phases are added as items come in and closed when done.

**App Backlogs** — per-app improvement tracks for monorepos with multiple discrete apps. Each app gets a 3-letter prefix (e.g. `EMP`, `FIN`, `SCH`). Phases use the `PREFIX-P#` format (e.g. `FIN-P1`, `EMP-P3`). No ship date — phases are appended as improvements accumulate. Plan files are named `{prefix}-p{N}-plan.md`.

All three tracks live in `milestones.md`. Active milestones appear first, followed by the App Backlogs section (one subsection per app), followed by the Weekly Improvements section, followed by completed milestones.

---

## Commands

| Command | What it does |
|---|---|
| `/mfh-init` | Scaffold `.mfh/` structure into a new project |
| `/mfh-status` | Snapshot of active milestones, open WI/app backlog phases, current work, and anything ready to close |
| `/mfh-start` | Start a new milestone phase, WI phase, or app backlog phase |
| `/mfh-plan` | Create an implementation plan for the active phase |
| `/mfh-execute [phase]` | Pick up active work and begin executing |
| `/mfh-progress [phase]` | Log progress, decisions, and what remains |
| `/mfh-done [phase]` | Close out a completed phase (or cancel one), update changelog |
| `/mfh-newfeature` | Add a new milestone, WI phase, or app backlog phase to the project |
| `/mfh-commit` | Group changes into logical Conventional Commits |
| `/mfh-push` | Push to remote and handle errors |
| `/mfh-audit` | Retroactive sweep for stale library docs, orphaned plan files, stuck phases, and broken decision references |
| `/mfh-update` | Generate friendly, shareable project updates — recent progress and what's next, one or more audience profiles at a time |

Commands marked `[phase]` accept an optional phase argument (e.g. `/mfh-done M4-P3`, `/mfh-done WI-P5`, or `/mfh-done FIN-P1`). If omitted and only one phase is active, it uses that automatically. If multiple phases are active, it asks.

---

## Multiple active phases

MFH supports running multiple phases in parallel. Each active phase gets its own section in `progress.md`:

```markdown
---

## M3-P2

**Milestone:** M3 — Auth Rebuild
**Phase:** P2 — Session Middleware
**Plan:** m3-p2-plan.md
**Status:** in progress
**Started:** 2026-04-24
**Tasks:**
- [x] 1. Add session model
- [ ] 2. Wire middleware
**Notes:**
_(none yet)_

---

## WI-P5

**Track:** Weekly Improvements
**Phase:** WI-P5 — Dark mode polish
**Plan:** (none)
**Status:** in progress
**Started:** 2026-04-27
**Notes:**
_(none yet)_

---

## FIN-P1

**Track:** App Backlog — Finance
**Phase:** FIN-P1 — Employee & User Flags
**Plan:** fin-p1-plan.md
**Status:** in progress
**Started:** 2026-05-17
**Tasks:**
- [x] 1. Add is_finance to Employee schema
- [ ] 2. Wire into session payload
**Notes:**
_(none yet)_
```

---

## What goes in `.mfh/library/`

Coding standards, conventions, and architectural rules that Claude should follow during all work. These are loaded by `/mfh-execute` and `/mfh-plan` so Claude always works within your project's standards.

`/mfh-init` scaffolds six of these as empty starter files for every project, regardless of type or size — the value is that they exist from day one so Claude reads them as a matter of course, not that the placeholder content is useful on its own:

- `git.md` — commit scopes, branch rules, message format
- `style.md` — brand colors, typography, visual conventions (`/mfh-update` reads this to color-match generated updates to the real brand)
- `helpers.md` — a compact index of shared utility/helper function signatures, so their existence and purpose is discoverable without grepping
- `architecture.md` — pages, routes, app structure, cross-app conventions
- `deploy.md` — server config, commands, deploy workflow, secrets
- `components.md` — shared UI component index — worth documenting even in a small project, before an extraction into a dedicated shared package would otherwise force the issue

Three more get scaffolded conditionally, based on an `/mfh-init` question about what the project actually has (no point in an empty `auth.md` for a project with no authentication):

- `database.md` — schema models, migration notes (only if the project has a database)
- `auth.md` — auth cookie, session, and middleware mechanics (only if it has user accounts/authentication)
- `roles.md` — roles, middleware access, route guards (only if it has role-based permissions)

Anything beyond these — API design patterns, data flow rules, business logic, or any other category — still gets added organically as a project needs it, same as always.

### Compact-header convention

For docs that describe structural facts derivable from the code itself — schema fields, component props, function signatures, route shapes — lead each entry with a one-line compact reference before any prose, so it's scannable/grep-able without reading a paragraph:

```
**EstimatingQuote** id(PK) | scope_id -> EstimatingScope, revisions -> EstimatingQuoteRevision[]
(c) OpportunityPanel oppId, canEdit, estimators, onClose, onRowUpdated
fn computeEffectiveFinancials(scope, quotes) -> { amount, margin, due_date }
```

Prose stays underneath for anything non-obvious — *why* a field exists, a gotcha, a rule that isn't visible from the signature alone. The one-line header is for "does this exist and what's its shape," not a replacement for explaining rationale. Keep this convention out of docs that are pure judgment calls with no structural facts to summarize (e.g. `business-logic.md`, `auth.md`) — there's nothing to compact there.

---

## Git tracking

| Path | Tracked |
|---|---|
| `.mfh/design/` | ✅ yes |
| `.mfh/library/` | ✅ yes |
| `.mfh/state/built.md` | ✅ yes |
| `.mfh/state/decisions.md` | ✅ yes |
| `.mfh/state/progress.md` | ❌ no (local session state) |
| `.mfh/plans/` | ❌ no (ephemeral) |
| `.mfh/updates/last-run.md` | ✅ yes |
| `.mfh/updates/{profile}/latest.md` | ✅ yes |
| `.mfh/updates/{profile}/latest.html` | ✅ yes |
| `.mfh/updates/{profile}/history/` | ✅ yes |
