---
description: Generate a friendly, shareable project update — recent progress and what's next, written for your project's chosen audience
---

# /mfh-update

You are generating a project update: a readable summary of what's happened recently and what's coming next. This is not a raw dump of `built.md` — `built.md` is written dense and technical so a future AI session has full context (schema fields, migration IDs, edge cases); this skill's job is **translation**, re-expressing that as something a person actually wants to read. Aggregating facts is the easy part; making them read as outcomes instead of implementation mechanics is the actual value here.

**Step 1 — Check for audience/delivery preference:**
Read `.mfh/design/roadmap.md` for an `## Updates` section. If it exists, use its **Audience** and **Delivery** fields for everything below.

If it doesn't exist yet, ask both questions once:
> "Who are these updates for? (e.g. your own devlog, a Discord/friends channel, a client or stakeholder report, a public blog)"
> "How should each update end up somewhere? (e.g. just leave the file for you to handle, publish it as a shareable Claude Artifact link, or something project-specific I should follow)"

Write the answers into `.mfh/design/roadmap.md` as a new section, inserted after **Current Track** (or at the end of the file if that section isn't present), so this is never asked again:
```
## Updates

**Audience:** [answer]
**Delivery:** [answer]
```

**Step 2 — Determine what's new:**
Check `updates/latest.md` for its most recent entry's date. If it doesn't exist yet (first run — check `updates/history/` too, in case `latest.md` was already rotated out), treat this as a first update covering the whole project so far.

Read `.mfh/state/built.md` entries newer than that date (or all of it, on a first run). Also read `.mfh/design/milestones.md` (Current Position lines, next unstarted phases) and `.mfh/design/roadmap.md` (Goals) for the forward-looking half. Skim `.mfh/state/progress.md` for anything actively in flight worth a one-line mention.

If nothing has shipped since the last update, say so directly and ask whether the user still wants an update generated (e.g. just the "what's next" half) rather than producing a report that just restates emptiness.

**Step 3 — Draft the update:**
Write two sections:

### Recent Progress
Translate the relevant `built.md` entries into outcome-focused prose matching the Audience's tone from Step 1 — what changed and why it matters, not implementation mechanics. Group related shipped phases together if they tell one story rather than listing them as disconnected bullet points. Skip purely-internal plumbing with no visible outcome unless the audience is technical enough to care.

### What's Next
Pull from the next unstarted phase(s) in `milestones.md` and the relevant goals in `roadmap.md`. Frame it as direction — what the project is working toward — not a bare task list.

Keep length proportional to how much actually happened. A quiet stretch gets a short update, not padded filler to look substantial.

**Step 4 — Present the draft:**
Show the user the drafted Markdown and ask: "Does this look right, or want changes?" Wait for approval before finalizing — tone and framing are subjective here, and this may be read by someone other than the user.

**Step 5 — Save and render:**
Once approved:
- If `updates/latest.md` already exists, move it (and its `.html` counterpart) into `updates/history/` first, named with its own entry date (e.g. `updates/history/2026-07-28.md` / `.html`) — never overwrite history.
- Write the approved draft to `updates/latest.md`.
- Render a clean, self-contained HTML version to `updates/latest.html` — inline CSS, readable typography, no external dependencies or build step. If the `artifact-design` skill is available in this environment, load it first for visual guidance on the HTML pass.

**Step 6 — Deliver, per the project's stated preference:**
- **"Just the file" / no strong preference given:** tell the user where it is (`updates/latest.md` and `updates/latest.html`) and stop.
- **Artifact link:** publish `updates/latest.html`'s content via the Artifact tool and give the user the link.
- **Anything else project-specific** (e.g. SFTP, a custom conversion pipeline): follow it if you know how; if it needs tooling this general skill doesn't have, tell the user the file is ready and that delivery from here is on them (or a project-specific extension of this skill).

**Step 7 — Confirm:**
Tell the user the update is ready, where it lives, and — if applicable — the delivery result or link.
