---
description: Generate a friendly, shareable project update — recent progress and what's next, written for your project's chosen audience
---

# /mfh-update

You are generating a project update: a readable summary of what's happened recently and what's coming next. This is not a raw dump of `built.md` — `built.md` is written dense and technical so a future AI session has full context (schema fields, migration IDs, edge cases); this skill's job is **translation**, re-expressing that as something a person actually wants to read. Aggregating facts is the easy part; making them read as outcomes instead of implementation mechanics is the actual value here.

**Step 1 — Check for audience/delivery preference:**
Read `.mfh/design/roadmap.md` for an `## Updates` section. If it exists, use its **Audience** and **Delivery** fields for everything below.

If it doesn't exist yet, ask both via `AskUserQuestion` (one call, two questions):
- "Who are these updates for?" — **My own devlog** / **A Discord/friends channel** / **A client or stakeholder report** / **A public blog** (Other covers anything else).
- "How should each update end up somewhere?" — **Just leave the file for me to handle** / **Publish as a shareable Claude Artifact link** / **Something project-specific** (Other covers a custom pipeline to describe).

Write the answers into `.mfh/design/roadmap.md` as a new section, inserted after **Current Track** (or at the end of the file if that section isn't present), so this is never asked again:
```
## Updates

**Audience:** [answer]
**Delivery:** [answer]
```

**Step 2 — Determine what's new:**
Check `.mfh/updates/latest.md` for its most recent entry's date. If it doesn't exist yet (first run — check `.mfh/updates/history/` too, in case `latest.md` was already rotated out), treat this as a first update covering the whole project so far.

Read `.mfh/state/built.md` entries newer than that date (or all of it, on a first run). Also read `.mfh/design/milestones.md` (Current Position lines, next unstarted phases) and `.mfh/design/roadmap.md` (Goals) for the forward-looking half. Skim `.mfh/state/progress.md` for anything actively in flight worth a one-line mention.

If nothing has shipped since the last update, say so directly and ask via `AskUserQuestion`: "Nothing's shipped since the last update. Still want one generated (just the 'what's next' half), or skip it this time?" — **Generate it anyway** / **Skip it this time** — rather than producing a report that just restates emptiness.

**Step 3 — Draft the update:**
Write two sections:

### Recent Progress
Translate the relevant `built.md` entries into outcome-focused prose matching the Audience's tone from Step 1 — what changed and why it matters, not implementation mechanics. Group related shipped phases together if they tell one story rather than listing them as disconnected bullet points. Skip purely-internal plumbing with no visible outcome unless the audience is technical enough to care.

### What's Next
Pull from the next unstarted phase(s) in `milestones.md` and the relevant goals in `roadmap.md`. Frame it as direction — what the project is working toward — not a bare task list.

Keep length proportional to how much actually happened. A quiet stretch gets a short update, not padded filler to look substantial.

**Step 4 — Present the draft:**
Show the user the drafted Markdown and ask via `AskUserQuestion`: "Does this look right?" — **Yes, finalize it** / **I want changes**. Wait for approval before finalizing — tone and framing are subjective here, and this may be read by someone other than the user.

**Step 5 — Save and render:**
Once approved:
- If `.mfh/updates/latest.md` already exists, move it (and its `.html` counterpart) into `.mfh/updates/history/` first, named with its own entry date (e.g. `.mfh/updates/history/2026-07-28.md` / `.html`) — never overwrite history.
- Write the approved draft to `.mfh/updates/latest.md`.
- Render a clean, self-contained HTML version to `.mfh/updates/latest.html` — inline CSS, readable typography, no external dependencies or build step. If the `artifact-design` skill is available in this environment, load it first for visual guidance on the HTML pass.

**Step 6 — Deliver, per the project's stated preference:**
- **"Just the file" / no strong preference given:** tell the user where it is (`.mfh/updates/latest.md` and `.mfh/updates/latest.html`) and stop.
- **Artifact link:** publish `.mfh/updates/latest.html`'s content via the Artifact tool and give the user the link.
- **Anything else project-specific** (e.g. SFTP, a custom conversion pipeline): follow it if you know how; if it needs tooling this general skill doesn't have, tell the user the file is ready and that delivery from here is on them (or a project-specific extension of this skill).

**Step 7 — Confirm:**
Tell the user the update is ready, where it lives, and — if applicable — the delivery result or link.
