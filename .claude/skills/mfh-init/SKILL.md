---
description: Initialize a new project with MFH planning structure, or change an existing project's setup (track, push & release, tags, optional docs, scopes)
---

# /mfh-init

You are initializing a new project with the MFH planning and state management structure.

Before starting, mention to the user: the full command reference, the three tracks, and how everything fits together are documented in `~/mfh/README.md` — worth a skim if they haven't already read it, but setup below works either way.

**Step 1 — Confirm the project root:**
Ask: "What is the path to your project root? Press enter to use the current working directory."

If the user presses enter or provides no path, use the current working directory.

**Step 2 — Check for existing MFH structure:**
Check if `.mfh/` already exists at the project root. If it does, this project is already set up. Don't re-initialize anything. Switch to **Settings mode** (the section at the end of this file) and follow it instead of Steps 3–7.

**Step 3 — Gather setup info:**
Ask via `AskUserQuestion`: "Is this a monorepo?" — **Yes** (multiple apps/packages in one repo; uses the App Backlogs track) / **No** (single app or codebase; uses the Weekly Improvements track). This is a structural fork the rest of setup depends on, so offer it as a pick rather than free text.

Also ask via `AskUserQuestion` (multi-select): "Does this project have any of these?" — **A database** / **User accounts or authentication** / **Role-based permissions**. Leave all unselected if none apply. This decides which conditional library docs get scaffolded in Step 5 — unlike `style.md`, `helpers.md`, `architecture.md`, `deploy.md`, `components.md`, and `testing.md` (scaffolded for every project regardless, since they apply universally), `database.md`/`auth.md`/`roles.md` only make sense when the project actually has that concern.

Also ask via `AskUserQuestion`: "How does code reach production?" — **Pushing main deploys it** (CI/CD) / **I deploy manually** (a script or command; pushing changes nothing live) / **From a release branch** (production runs only from a separate `release` branch that `main` is released into) / **Not deployed yet**. If they pick release branch, also ask what happens after it's pushed: **CI deploys it automatically** / **I run a deploy command** (ask which, free text). This fills the **Push & Release** section of `git.md` (Step 5). `/mfh-push` reads it on every push: in a single-branch project a push is just a push, and in a release-branch project it asks **Normal push** (backup) or **Release** (ship `main` to `release`).

If the answer was **Pushing main deploys it** or **From a release branch** (the two styles where MFH knows when a release happens), also ask via `AskUserQuestion`: "How should releases be tagged?". Each release gets a permanent git tag marking exactly what went live, with release notes in the tag message. The options are **Date tags** (`2026.10.20`, with `.2` for a second release that day) / **Semantic versions** (`v1.4.0`: major.minor.patch, chosen each release) / **No tags**. Recommend based on what the project is. For an internal app or a website, recommend **Date tags**: nobody depends on its version number and it releases whenever work is ready, so a date never forces a "minor or major?" judgment that means nothing. For a library, package, API or installable app that other people depend on, recommend **Semantic versions**: there, the number tells consumers whether a release can break them. Put the recommended option first, mark it (Recommended), and say why in one line. For the other two styles, write `n/a`.

Also ask via `AskUserQuestion`: "How should commit scopes be chosen?" The scope is the label in `type(scope): description`. The options are:
- **Phase IDs:** the scope is the MFH phase the work belongs to (`feat(emp-p3)`, `fix(m4-p2)`), or a plain area name for ad-hoc work outside any phase. It says *which piece of planned work* a commit is part of. `git log --grep emp-p3` pulls up a whole phase, matching MFH's records exactly.
- **Modules:** the scope is the part of the codebase touched (`feat(api)`, `fix(ui)`). It says *where* in the code the change is.

Recommend **Phase IDs** for a monorepo, since one phase often spans several apps and a module scope can't name them all. Recommend **Modules** for a single-app project, where "which part of the code" is the more useful thing to know. Put the recommended option first, mark it (Recommended), and say why in one line.

Then ask together in one message:
1. "What is the project name?"
2. Only if the scope style is **Modules:** "What are the main modules or areas of this codebase? These become your commit scopes. (e.g. `api, ui, auth, db, config`)"

Use the project name to replace `[Project Name]` in all template files. Use the scope style (and the modules list, if any) to fill the Scopes section of `git.md`.

If the user picked Modules but isn't sure what their modules are, ask: "What is the app for?" Then suggest sensible scope defaults based on the answer — for example, a web app might default to `api, ui, auth, db, config`; a mobile app to `screens, api, state, config`; a CLI tool to `cmd, core, config`. Offer the suggested list via `AskUserQuestion` as a selectable option (its free-text fallback covers "something else entirely") rather than asking them to type it back.

**Step 4 — Create the folder structure:**
Create these directories:
- `{project}/.mfh/design/`
- `{project}/.mfh/library/`
- `{project}/.mfh/plans/`
- `{project}/.mfh/state/`

**Step 5 — Create template files:**

`.mfh/design/roadmap.md`:
```markdown
# [Project Name] — Roadmap

## Vision
[What does this project ultimately deliver?]

## Tech Stack
[Key technologies, frameworks, databases]

## Goals
- [Goal 1]
- [Goal 2]

---

## Current Track

[MONOREPO: **App Backlogs** — Continuous per-app improvement tracks. Each app has its own backlog with a 3-letter prefix, plus a cross-app Platform track (PLT). No fixed end date.]
[NON-MONOREPO: **Weekly Improvements** — Continuous rolling backlog of improvements worked on as capacity allows. No fixed end date.]
```

`.mfh/design/milestones.md` — use the correct variant based on the monorepo answer:

**Non-monorepo variant:**
```markdown
# [Project Name] — Milestones & Weekly Improvements

This file has two tracks:

**Milestones** — named delivery cycles with a fixed goal. Numbered `M1, M2, …`. Phases within a milestone are `P1, P2, …`. Plans saved as `.mfh/plans/m{N}-p{N}-plan.md`.

**Weekly Improvements** — continuous rolling backlog with no end date. Phases use the `WI-P#` prefix. Plans saved as `.mfh/plans/wi-p{N}-plan.md`.

**Workflow per phase (both tracks):**
1. `/mfh-start` — select milestone/track + phase; creates `progress.md` entry
2. `/mfh-plan` — write the plan
3. `/mfh-execute` — execute the plan
4. `/mfh-done` — close the phase, update changelog

**File structure:** Active milestones first → Weekly Improvements → Completed milestones in numbered order. When a milestone ships, move it into the Completed section in its numbered position.

**Status legend:** ✅ Complete · 🟡 Implementation done, awaiting `/mfh-done` · 🔄 In progress · ⬜ Not started · ❌ Cancelled

---

# Weekly Improvements — Continuous Track

Rolling backlog of improvements worked on as capacity allows. No fixed end date — phases are added as new items come in and closed when done.

Phases use the `WI-P#` prefix. Plans saved as `.mfh/plans/wi-p{N}-plan.md`.

### Current Position: no phases started

| Phase | Description | Priority |
|-------|-------------|----------|

---

# Completed Milestones

Milestones move here when all phases are done. Slotted in numbered order.

---
```

No placeholder milestone or WI phase — `/mfh-newfeature` inserts the real first one in exactly this spot (before Weekly Improvements), asking name/goal/phases/priority properly instead of leaving hand-edited bracket text. Tell the user in Step 7 to run it now.

**Monorepo variant:**
```markdown
# [Project Name] — Milestones & App Backlogs

This file has two tracks:

**Milestones** — named delivery cycles with a fixed goal. Numbered `M1, M2, …`. Phases within a milestone are `P1, P2, …`. Plans saved as `.mfh/plans/m{N}-p{N}-plan.md`.

**App Backlogs** — continuous per-app improvement tracks with no end date. Each app uses a 3-letter prefix. Phases use the `{PREFIX}-P#` format (e.g. `PRT-P1`). Plans saved as `.mfh/plans/{prefix}-p{N}-plan.md`. These sections never move — phases are appended and closed in place. New app sections are added here automatically when a milestone that ships a new app completes.

**Workflow per phase (both tracks):**
1. `/mfh-start` — select milestone/track + phase; creates `progress.md` entry
2. `/mfh-plan` — write the plan
3. `/mfh-execute` — execute the plan
4. `/mfh-done` — close the phase, update changelog

**File structure:** Active milestones first → App Backlogs → Completed milestones in numbered order. When a milestone ships, move it into the Completed section in its numbered position.

**Status legend:** ✅ Complete · 🟡 Implementation done, awaiting `/mfh-done` · 🔄 In progress · ⬜ Not started · ❌ Cancelled

---

# App Backlogs — Continuous

## At a Glance — Open Work

_Every non-complete (⬜ / 🔄 / 🟡) phase across the app backlogs below, grouped by app. Apps with nothing open aren't listed. Maintained automatically by `/mfh-start`, `/mfh-execute`, `/mfh-progress`, `/mfh-done` and `/mfh-newfeature`; never hand-edit._

---

## PLT — Platform

Cross-app work: shared packages, auth, infra.

| Phase | Description | Priority |
|-------|-------------|----------|

---

_(App backlog sections are added here automatically by `/mfh-done` each time a milestone ships a new app.)_

---

# Completed Milestones

Milestones move here when all phases are done. Slotted in numbered order.

---
```

Same as the non-monorepo variant — no placeholder milestone; `/mfh-newfeature` inserts the real first one before App Backlogs.

`.mfh/state/built.md`:
```markdown
# [Project Name] — Changelog

Append-only record of completed phases. Most recent first.

---
```

`.mfh/state/decisions.md`:
```markdown
# [Project Name] — Decisions

Non-obvious implementation choices and their rationale. Reference before making architectural decisions.

---
```

`.mfh/state/progress.md`:
```markdown
# Active Work

Tracks all currently active phases. Updated by `/mfh-start`, `/mfh-plan`, `/mfh-progress`, and `/mfh-execute`. Entries removed by `/mfh-done` when a phase completes.

_(no active phases)_
```

`.mfh/library/git.md`: fill the Scopes and Examples sections from Step 3's scope style, using the matching variant below:
```markdown
# Git Conventions

## Commit Format

```
type(scope): short description
```

### Types
- `feat` — new feature
- `fix` — bug fix
- `chore` — config, tooling, dependencies
- `style` — UI/styling only
- `refactor` — restructure, no behaviour change
- `docs` — documentation only
- `test` — tests only

### Scopes
[MODULES variant:]
Use the affected module or area as the scope:

[List each module from Step 3 as a bullet: `- \`module-name\``]

[PHASE IDS variant:]
Use the MFH phase identifier when the work belongs to a phase, or a short area name for ad-hoc work outside any phase.

| Situation | Scope format | Example |
|---|---|---|
| Milestone phase work | `m{N}-p{N}` | `feat(m2-p3)` |
| [NON-MONOREPO: Weekly improvement phase] [MONOREPO: App backlog phase] | [NON-MONOREPO: `wi-p{N}`] [MONOREPO: `{prefix}-p{N}` (lowercase prefix)] | [NON-MONOREPO: `fix(wi-p12)`] [MONOREPO: `feat(emp-p3)`] |
| Ad-hoc outside any phase | area name | `chore(config)` |

### Examples
[MODULES: one example commit per module, e.g. `feat(api): add user list endpoint`]
[PHASE IDS: three examples, one per row above, e.g. `feat(m2-p3): add user list page`]

## Branch Rules
- Work on `main`

## Push & Release
- **Release style:** [single branch | release branch, from the Step 3 answer]
- **Release branch:** [`release` | none]
- **What a push does:** [one of: "Pushing `main` deploys to production automatically (CI)." / "Nothing live; deploys are run manually ([machine/command if given])." / "Nothing live; production runs only from `release`." / "Nothing live; not deployed yet."]
- **After a release:** [release-branch style only: "CI deploys `release` automatically." / "Run `<command>`." | n/a]
- **Release tags:** [date (`YYYY.MM.DD`, `.2` for a second that day) | semver (`vMAJOR.MINOR.PATCH`) | none | n/a]
- **Hold:** none
```

The **Push & Release** section is the contract `/mfh-push` reads. **Release style** decides whether a push is just a push or `/mfh-push` offers Normal push / Release. **What a push does** decides whether it warns before pushing. **After a release** is what it tells the user to do once the release branch is pushed. **Release tags** decides whether each release is tagged, and how; the tag message carries the release notes. **Hold** is a temporary "don't push `main` until …" (e.g. `M3-P2: commits stay local until its coordinated deploy`) that the user sets and clears by hand. Leave it `none` at init. Don't create the `release` branch here; `/mfh-push`'s first Release creates it.

**Every project gets these six, regardless of type or size** — the value here isn't the placeholder content (there isn't any), it's that the file exists from day one so Claude reads it as a matter of course during `/mfh-execute`/`/mfh-plan` and other skills, and so `/mfh-update`'s brand-color detection and the `helpers.md` convention have something to check against immediately rather than only after a project happens to create one organically:

`.mfh/library/style.md`:
```markdown
# [Project Name] — Style Guide

Brand colors, typography, and visual conventions. Fill in once the project has an actual visual identity — `/mfh-update` reads this to color-match generated updates to the real brand instead of falling back to its own default look.
```

`.mfh/library/helpers.md`:
```markdown
# [Project Name] — Shared Helpers

Compact index of shared utility/helper function signatures. Check here before writing a new helper, in case one already exists.
```

`.mfh/library/architecture.md`:
```markdown
# [Project Name] — Architecture

Pages, routes, app structure, and cross-app conventions. Read before adding pages, routes, or cross-app features.
```

`.mfh/library/deploy.md`:
```markdown
# [Project Name] — Deploy

Server config, commands, deploy workflow, and secrets. Read before touching deploy scripts or infra config.
```

`.mfh/library/components.md`:
```markdown
# [Project Name] — Components

Shared UI component index. Read before reaching for or adding a shared component — worth documenting even in a small project, before an extraction into a dedicated shared package would otherwise force the issue.
```

`.mfh/library/testing.md`:
```markdown
# [Project Name] — Testing

Project specifics for `/mfh-test` (the general workflow lives in the skill). `/mfh-test` fills this in on its first run and adds to it as test rounds learn things.

## Automated checks
## Dev site
## Test data
## Viewing as another user
## Cleaning up test data
## Gotchas
```

**Conditional on Step 3's answers** — only create the ones that apply:

If the project has a database, `.mfh/library/database.md`:
```markdown
# [Project Name] — Database

Schema models and migration notes. Read before writing queries, changing schema, or running migrations.
```

If the project has user accounts/authentication, `.mfh/library/auth.md`:
```markdown
# [Project Name] — Auth

Auth cookie, session, and middleware mechanics. Read before touching auth flows.
```

If the project has role-based permissions, `.mfh/library/roles.md`:
```markdown
# [Project Name] — Roles

Roles, middleware access, and route guards. Read before touching role logic or adding a new guarded route.
```

Also copy `~/mfh/README.md` (the standard clone location per this repo's own setup instructions) into `.mfh/README.md`, so the full command reference and how MFH works travels with the project itself — useful if this project is opened somewhere `~/mfh` isn't available. If `~/mfh/README.md` doesn't exist (a different install location), skip this quietly; it's a nice-to-have, not required for MFH to function. Note it's a point-in-time snapshot, not kept in sync automatically — it'll drift slightly stale as `~/mfh/README.md` is updated over time.

**Step 6 — Update .gitignore:**
Check if `{project}/.gitignore` exists. If it does, check whether the MFH entries are already present. If not, append:

```
# MFH
.mfh/plans/
.mfh/state/progress.md
.mfh/state/testing-setup.md
.mfh/test-reports/
```

If `.gitignore` doesn't exist, create it with those entries.

**Step 7 — Confirm:**
Tell the user:
"MFH initialized. `.mfh/library/` was seeded with starter docs (style.md, helpers.md, architecture.md, deploy.md, components.md, testing.md[, database.md][, auth.md][, roles.md] — list whichever were actually created) — empty for now, fill them in as the project takes shape. Next steps:
1. Fill in `.mfh/design/roadmap.md` with your project vision and stack.
2. Review `.mfh/library/git.md` — its **Push & Release** section tells `/mfh-push` what a push does in this project; adjust it if the answer changes.
3. Run `/mfh-newfeature` to define your first milestone — it'll ask for the name, goal, phases, and a priority for each, rather than hand-editing `milestones.md`.
4. Run `/mfh-start` to begin your first phase."

---

# Settings mode — changing an existing project's setup

Used when `/mfh-init` runs on a project that already has `.mfh/`. Projects outgrow choices made at init: a single app becomes a monorepo, deploys move to a release branch, a database gets added. This mode changes one setting at a time, using the **same questions and recommendations as Step 3**, so first-time setup and later changes can never drift apart.

**Two rules for every change:**
- **Never rewrite history.** `built.md`, closed phases, past commits, tags and archive files keep their original IDs and wording. A change applies from now on.
- **Never rename an in-progress phase.** Plan files, `progress.md` entries and commit scopes all key off the phase ID. Leave active phases under their current IDs until they close, or wait until none are active, whichever the user prefers.

**S1 — Read the current settings** (from the files themselves, not memory) and show them as a short list:
- **Track:** App Backlogs if `milestones.md` has `# App Backlogs`, Weekly Improvements if it has `# Weekly Improvements`
- **Push & Release:** **Release style**, **What a push does**, **After a release** and **Release tags** from `.mfh/library/git.md` (or "not set up")
- **Optional library docs:** which of `database.md`, `auth.md`, `roles.md` exist in `.mfh/library/`
- **Commit scopes:** the scope style in `git.md`'s **Scopes** section (phase IDs or modules), plus the module list if it uses modules
- **Project name:** from `roadmap.md`'s title
- **Active phases:** the `## …` sections in `progress.md`. These matter for the track change.

**S2 — Pick one:** ask via `AskUserQuestion`: "What do you want to change?" — **Track (single repo ↔ monorepo)** / **Push & release method** / **Optional library docs** / **Commit scopes**. Its Other fallback covers project name or anything else. Change one setting per pass, and offer another pass at the end.

**S3 — Ask the setting's Step 3 question** with the same options and the same recommendation rule, with the current value marked "(current)".

**S4 — Show the plan, then confirm.** List every file that will change and what will happen to each. Ask via `AskUserQuestion`: **Make these changes** / **Cancel**.

**S5 — Make the change:**

- **Push & release method or tags:** rewrite git.md's **Push & Release** section using the Step 5 template.
  - **Switching to a release branch:** ask which commit production runs right now (default: the current `main`), and create `release` there with `git branch release <commit>`. Never switch the working checkout's branch. Then warn: "Your build/deploy setup must now build from `release`. That's project-specific (deploy scripts, CI config), so MFH can't change it for you." Offer to log that work as a phase via `/mfh-newfeature`.
  - **Switching away from a release branch:** leave the `release` branch and existing tags in place. Mention they can be deleted by hand once nothing builds from them.
  - **Changing the tag scheme:** existing tags stay. Add a line under git.md's **Versioning** heading (create it if absent) noting which scheme applies from today.
- **Optional library docs:** create each newly selected doc from the Step 5 template. Never delete a doc that's being deselected. Point out that it's still there and the user can delete it themselves if it's truly unused.
- **Commit scopes:** ask Step 3's scope-style question (same recommendation rule; the current style is marked "(current)"), plus the modules question if they pick Modules. Rewrite git.md's **Scopes** and **Examples** from the matching Step 5 variant. Old commits keep their old scopes. If the track is changing too, the phase-ID table follows the new track (`wi-p{N}` vs `{prefix}-p{N}`).
- **Track: single repo → monorepo:**
  1. Ask which apps the repo contains, each with a 3-letter prefix, plus whether to keep a cross-app `PLT — Platform` section (recommend yes).
  2. In `milestones.md`, replace the `# Weekly Improvements — Continuous Track` heading and its explanatory text with `# App Backlogs — Continuous` and one `## {PREFIX} — {App Name}` section per app (Step 5's monorepo template). Update the file's intro paragraph to describe App Backlogs instead of Weekly Improvements.
  3. **Open WI phases (⬜):** ask, one by one or as a batch table, which app each belongs to, and move each into that app's table under the next free `{PREFIX}-P#`. Note the old ID in its description ("formerly WI-P12").
  4. **Active WI phases (🔄/🟡)** keep their `WI-P#` IDs until they close. Put them in a short `## WI — Legacy (closing out)` table at the end of App Backlogs. `/mfh-done` closes them there as usual. Delete that section once it's empty.
  5. **Completed WI phases (✅/❌)** stay exactly as written, moved under a `## WI — Weekly Improvements (history)` section at the end of App Backlogs.
  6. Add the **At a Glance — Open Work** section directly under `# App Backlogs — Continuous` (Step 5's monorepo template). Give it one `- {icon} {PREFIX}-P# {Short Title} ({Priority})` line per open phase, grouped under a `**{PREFIX}**` heading per app, and a `**WI**` group for the legacy phases from step 4. See the README's "At a Glance summary" for the format.
  7. Update `roadmap.md`'s **Current Track** to the App Backlogs line from the Step 5 template.
- **Track: monorepo → single repo:** the reverse. Replace App Backlogs with `# Weekly Improvements — Continuous Track` (Step 5's non-monorepo template). Move open phases in as `WI-P#` (next free numbers, noting "formerly EMP-P3"). Active phases keep their IDs in a legacy table until closed. Completed per-app history stays verbatim under a history heading. Remove the **At a Glance** section; Weekly Improvements is a single list and doesn't use one. Update `roadmap.md`'s **Current Track**.

**S6 — Record and confirm:** append an entry to `.mfh/state/decisions.md`: "`[Setting]` changed from X to Y ([date])", with the user's reason in one line (ask if it wasn't given). Then tell the user what changed, that it's uncommitted (`/mfh-commit` will pick it up), and ask via `AskUserQuestion` whether to change another setting: **Change another** / **Done**.

