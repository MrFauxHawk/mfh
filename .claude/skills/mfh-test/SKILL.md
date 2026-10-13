---
description: Browser-test the active phase — runs the test itself when a browser is on this machine, or hands off a paste-ready prompt to a desktop Claude when it isn't; reading a report ticks the plan's Verification Checklist automatically
---

# /mfh-test

You are running a test round for the active phase, reading a test report, or tidying up after testing. This command accepts an optional sub-command as its first argument:

| Invocation | What it does |
|---|---|
| `/mfh-test [phase]` | Start a test round (default) |
| `/mfh-test report [folder]` | Read a finished report, tick the plan's Verification Checklist, log progress, triage. Also triggered when the user says "report's in", "check the report", "read the latest report", or similar. |
| `/mfh-test cleanup [tag]` | Delete the test data a round created (dev only) |
| `/mfh-test prune` | Delete old test-report folders so `.mfh/test-reports/` doesn't bloat |

## Files this skill uses

| File | Committed? | Holds |
|---|---|---|
| `.mfh/state/testing-setup.md` | **No** (gitignored) | How *this machine* runs tests — the mode, and for remote mode the server address/user/project path and the desktop folder. Differs per machine, so the same project on another machine gets asked once there. |
| `.mfh/library/testing.md` | Yes | What to test in *this project* — dev URLs, test-data conventions (which records to use, test tag format), how to view as another user/role (simulation, test logins), how to find and delete test data, project gotchas. |
| `.mfh/test-reports/<YYYY-MM-DD-HHmm>-<PHASE>-<tag>/` | **No** (gitignored) | One folder per test run: `report.md` + step-named screenshots. The phase ID is **always** in the folder name — `/mfh-done` and `prune` rely on it. Ad-hoc rounds (no phase) use `<AREA>-ADHOC` in its place (see Step 1). |

If `.gitignore` lacks `.mfh/test-reports/` or `.mfh/state/testing-setup.md`, add them (under the `# MFH` block) the first time this skill runs.

---

## `/mfh-test` — start a test round

**Step 1 — Identify the phase.** Same lookup as `/mfh-execute`: use the argument if given; otherwise the only active phase in `.mfh/state/progress.md`; otherwise ask via `AskUserQuestion` (one option per active phase, plus **Ad-hoc — not part of a phase**).

**Ad-hoc rounds** — testing a small fix or follow-up made outside any active phase (e.g. a tweak after a phase closed, or work the user said not to plan). Use `<AREA>-ADHOC` wherever a phase ID would go — the test tag, the report folder name — where `<AREA>` is the app/area prefix the work belongs to (e.g. `MAT-ADHOC`; plain `ADHOC` if the project has no prefixes). There's no plan, so Step 2 asks what to prove, and the report flow skips the checklist and `progress.md` steps (see there). Don't invent a phase or attach the round to an unrelated active phase.

**Step 2 — Read what needs proving.** Read the phase's plan file in `.mfh/plans/` and find its **Verification Checklist**. Number the items `V1, V2, …` in order — every test step you write gets tagged with the item(s) it proves (e.g. `A3 [V2]`), and every browser-testable item must be covered by at least one step. Items that aren't browser-testable (tsc/lint clean, migration applied, a report reviewed) aren't put in the test — you verify those yourself and tick them in the report step.
If there's no plan or no checklist (ad-hoc phase), ask the user what this round should prove and number those.

**Step 3 — Load (or establish) this machine's setup.** Read `.mfh/state/testing-setup.md`. If it doesn't exist, ask via `AskUserQuestion`: "How can testing run on this machine?"
- **Same machine** — "A browser runs on the machine this Claude is on, and I can drive it (Claude in Chrome, the desktop app's built-in browser, or Playwright reaching the app)."
- **Separate desktop Claude** — "This Claude runs on a remote server; the browser is on my own PC. I'll run a second Claude Code there and paste prompts in."
- **Manual** — "I'll click through it myself and tell you the results."

Then do that mode's one-time setup and write the file:

**Same machine:** find the browser tools actually available (`ToolSearch` for chrome / browser / playwright tools; load the matching browser skill if one is listed). Do a read-only reachability check: open the project's dev URL (from `.mfh/library/testing.md`) and confirm the real app loads — not a login wall, "Access Denied", or a zero-trust/VPN block page. If it's blocked, say what you saw and suggest **Separate desktop Claude** instead. Record the working tool and URL.

**Separate desktop Claude:** gather this server's details yourself — don't ask for what you can read:
- Server address as the desktop sees it: the 3rd field of `$SSH_CONNECTION` (the server IP of the user's current SSH session). Don't rely on `hostname -f` — internal names often don't resolve from the desktop.
- SSH user: `whoami`. Project path: the repo root. Reports path: `<repo>/.mfh/test-reports/` (create it).

Ask only for what you can't detect: the desktop OS (Windows / Mac) and the folder to use there (suggest `~/claude-testing`, e.g. `C:\Users\<name>\claude-testing`). Then give the user a numbered one-time setup checklist, **only including steps they haven't already done** (ask once via multi-select which are already in place):
1. Install Claude Code natively on the desktop (Windows PowerShell: `irm https://claude.ai/install.ps1 | iex`); make sure its folder is on the user PATH; open a **fresh** terminal from the Start menu / Dock (an already-open terminal — including VS Code's — keeps the old PATH, and quitting VS Code would end a remote session like this one).
2. Install the Claude browser extension in a browser dedicated to testing (e.g. Edge if Chrome is their everyday browser), sign it in to the same claude.ai account, and sign in to the app once in that browser.
3. Start Claude in the testing folder with `claude --chrome`; run `/chrome` → **Select browser…** and pick the testing browser. If the names are generic ("Browser 1/2"), have it open a page and report the user agent (`Edg/` = Edge).
4. Create `CLAUDE.md` in the testing folder with the template below, filled in.
5. Check `ssh <user>@<server-ip> "echo connected"` works from the desktop (if the name/IP doesn't resolve, use the IP from `$SSH_CONNECTION`; if it asks for a password, look at the desktop's `~/.ssh/config` for the host alias the editor uses).
6. Dry run: paste a "SETUP TEST" prompt (open the dev URL, screenshot, write + upload a report) and confirm on this side that the folder arrived and you can read the screenshot.

Desktop `CLAUDE.md` template (fill in every `<…>`):
```markdown
# Browser testing for <project> (DEV)

You run UI tests on <dev URL>, driven by prompts pasted in from the project's main Claude session.

## Rules
- Dev only. Creating test data is fine; start every Notes/description field you fill with the test tag given in the prompt.
- Work only in your own browser window/tab group; never touch the user's other tabs.
- When a run is done, leave your testing window/tab group open — don't close tabs or the browser. More tests usually follow, and reusing the open, signed-in session saves setting it up again.
- If you hit a login, "Access Denied", or VPN/zero-trust page, stop and report exactly what you see.
- If a step needs a different user or role, stop and ask the user to log in / switch to a simulated user, wait for their reply, confirm the active user with a screenshot, and ask them to switch back at the end. Never try to change users yourself.
- Don't try to fix anything — just report.

## Reporting (end of every run)
1. Write the full report (PASS/FAIL table with each step's [V#] tag, details for every FAIL/PARTIAL/SKIPPED, minor issues, data created/changed) to `reports\<YYYY-MM-DD-HHmm>-<tag>\report.md` here. The tag always starts with the phase ID (e.g. `MAT-P22-TEST`).
2. Save every screenshot into that folder, named by step (`A3.jpg`, `B6-detail.png`).
3. Upload: `scp -r "reports\<that folder>" <user>@<server-ip>:<repo>/.mfh/test-reports/`
4. Tell the user the folder name, and copy the report text to the clipboard.
```

**Manual:** nothing to set up.

`testing-setup.md` format:
```markdown
# Testing setup (this machine — not committed)
- **Mode:** same-machine | remote-desktop | manual
- **Confirmed:** <date>
- [same-machine] **Browser tool:** <tool> · **Dev URL:** <url>
- [remote-desktop] **Server:** <user>@<ip> · **Repo:** <path> · **Desktop:** <OS>, `<folder>`, browser <name> ("Browser N")
- [remote-desktop] **Upload line (must match the desktop CLAUDE.md):** `scp -r "reports\<folder>" <user>@<ip>:<repo>/.mfh/test-reports/`
- [optional] **Previous reports path:** `<old path>` — where reports used to land before this setup; the report step checks it for strays.
```
If a saved setup stops working (e.g. the IP changed, or a same-machine browser is now blocked), say so and offer to redo this step.

**Step 4 — Load (or create) the project's testing doc.** Read `.mfh/library/testing.md`. If it doesn't exist — or is still just the `/mfh-init` stub — **create/fill it now, without asking first**; the rest of this skill depends on it. Find as much as you can in the codebase yourself before asking anything:
- **Dev site** — URL(s) from env files, compose/proxy config, README, or `.mfh/library/deploy.md`.
- **Test data** — tag format `<PHASE> TEST`; where tagged data would live (DB tables / collections with a free-text notes/description field).
- **Viewing as another user** — look for a role-simulation / impersonation feature or seeded test logins; note whether simulated sessions can write.
- **Cleaning up test data** — the dev database and how to reach it (from `.mfh/library/database.md` or config); cascades worth knowing. Dev only — never production.
- **Gotchas** — e.g. whether outbound email/notifications are suppressed outside production, whether a schema/client change needs an app restart before testing.

Then ask **one** `AskUserQuestion` round for only what you couldn't find (typically: the dev URL if undetectable, a good record to test against, anything that must never be touched). Leave a clearly marked `_(unknown — fill in)_` under any heading still unknown rather than guessing. Tell the user in one line that you created it.

**Step 5 — Write the test.** Build it from the numbered checklist:
- Test tag `<PHASE> TEST` (e.g. `MAT-P22 TEST`) at the start of every Notes field the run fills.
- The specific records to use (pick real dev records that suit — query the dev DB if needed).
- Numbered sections and steps, each with its `[V#]` tag(s), an **Expected** result, and 📸 where a screenshot proves it. Include phone / tablet / desktop widths (~390 / ~820 / full) whenever layout changed; a same-origin iframe at that width is an acceptable fallback when the window won't resize.
- Any step needing a different user goes in its own section, opened with **⏸ PAUSE — ask the user** to switch (naming exactly where to click, from `testing.md`), confirmed with a screenshot, and closed with another pause to switch back. Keep simulated-user steps read-only if the project's simulation blocks writes.
- Before testing changes that touched the database client or server config, check the app container/process actually restarted since (e.g. container start time vs. the client regeneration time) — otherwise the test runs old code.
- End with: "PASS/FAIL table (with [V#]) plus details for every FAIL/PARTIAL/SKIPPED, minor issues, and data created."

Then deliver it by mode:
- **Same machine:** show the test briefly and ask "Run it now?" via `AskUserQuestion`. Then drive the browser through it yourself, saving screenshots and `report.md` into a new `.mfh/test-reports/<YYYY-MM-DD-HHmm>-<PHASE>-<tag>/` folder, and go straight into the **report** flow below. Never mention PowerShell, a desktop Claude, or copy/paste.
- **Separate desktop Claude:** print the prompt in one fenced block, preceded by "Written for: the Claude Code session on your desktop." Tell the user: paste it into the desktop Claude, then say **"report's in"** (or run `/mfh-test report`) when it's done. Don't repeat the setup steps unless they're not done.
- **Manual:** give the steps as a checklist; when the user replies with results, write them into a report folder yourself, then run the **report** flow.

---

## `/mfh-test report [folder]` — read a report and update progress

Run this whenever the user says a report is in — they shouldn't have to name the command.

1. **Find the report:** the folder given, else the newest folder(s) in `.mfh/test-reports/` for an active phase or an `-ADHOC` round. If several new ones arrived since you last looked, read them all (oldest first).
   - **Not there? Check for a stale upload path before asking the user.** In remote-desktop mode the desktop's `CLAUDE.md` decides where reports land, and it can lag behind a change (e.g. an old reports folder from before this skill). Look in any **Previous reports path** listed in `testing-setup.md`, then search the repo for recently-modified report folders outside `.mfh/test-reports/` (e.g. `find <repo> -path '*/test-reports/*/report.md' -newer .mfh/state/testing-setup.md -not -path '*/.mfh/*'`, or `-mmin -120`). If found: move the folder(s) into `.mfh/test-reports/`, tell the user their desktop `CLAUDE.md` upload line is out of date, and give them the exact corrected `scp` line from `testing-setup.md`.
2. **Read** `report.md` and the screenshots (the Read tool shows images). **Don't take the tester's root-cause claims on trust** — confirm against the code or DB before acting; a tester driving the UI often guesses wrong about *why*.
3. **Update the plan's Verification Checklist immediately — unasked, in this same turn:**
   - Tick `- [x]` each item whose tagged steps all **PASS**, and append the evidence: `— ✅ <folder> A1–A3`.
   - Leave FAIL / PARTIAL / SKIPPED items unticked with a short reason: `— ❌ <folder> D1 (label wraps)`, `— ⏭ skipped (needs a real lead login)`.
   - Tick non-browser items (tsc/lint, migration applied, data report reviewed) only once **you've** verified them, citing what you ran.
   - A later passing retest ticks items an earlier round failed — point the note at the retest folder.
   - Never tick without evidence. This is what lets `/mfh-done`'s unchecked-items check pass on its own once testing is genuinely complete.
4. **Log it:** add a one-line entry to the phase's Notes in `progress.md` — report folder, PASS/FAIL/SKIPPED tally, and anything left unticked.
   - **Ad-hoc round:** skip steps 3–4 (there's no plan or phase entry to update) — report the tally in chat instead. If the user later logs the work into a phase (e.g. via the ad-hoc → phase flow), cite the report folder there.
5. **Grow the project's testing doc:** if this round taught something project-specific a future round would want to know — a page that loads empty for some role, a fallback needed for responsive checks, a good test record, a field a test changes that cleanup must restore — append it to the right section of `.mfh/library/testing.md` (usually **Gotchas** or **Test data**). Don't add phase-specific results; those belong in the report and progress notes.
6. **Triage and report back:** summarise what passed; for each failure say whether it's a real bug (and its actual cause), a test-plan mistake, or expected behaviour. Real bugs in scope → fix them; out of scope → offer `/mfh-newfeature`. Minor issues → list with a recommendation each and let the user pick (`AskUserQuestion`). Note which checklist items are still unticked and why.
7. **Retest:** after fixes, write a short retest covering only what changed, keeping the `[V#]` tags (same delivery rules as Step 5 above).

---

## `/mfh-test cleanup [tag]` — delete the test data

Only when the user asks — they sometimes keep test data for the next round.
1. Follow `.mfh/library/testing.md`'s cleanup section. If it has none, work out where tagged data lives and ask before deleting anything.
2. **List** every record carrying the tag (default: tags for the active phase) and show it before deleting. Restore anything a test changed rather than created (e.g. a field a test cleared), if the original value is known from the report or a note.
3. Delete in **one transaction**, checking cascades/dependencies first. **Dev only — never production.**
4. Confirm the counts left are zero.

---

## `/mfh-test prune` — keep `.mfh/test-reports/` lean

1. List report folders that are **older than 30 days**, or whose phase ID **isn't an active phase** in `progress.md` (closed, cancelled, or stray) — with each folder's size and a total (`du -sh`). **`-ADHOC` folders are deliberate, not strays** — they only qualify once they're older than 30 days.
2. Ask via `AskUserQuestion`: **Delete these** / **Keep them**. Delete only on confirmation.

`/mfh-done` already deletes a phase's report folders when it closes the phase, so `prune` mainly catches cancelled/stray phases and anything left from before that existed.

---

**Never claim something was "verified in the browser" without a report from a real run.**
