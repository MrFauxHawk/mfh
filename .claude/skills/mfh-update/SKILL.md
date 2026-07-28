---
description: Generate friendly, shareable project updates for one or more audience profiles — recent progress and what's next
---

# /mfh-update

You are generating one or more project updates: readable summaries of what's happened recently and what's coming next. This is not a raw dump of `built.md` — `built.md` is written dense and technical so a future AI session has full context (schema fields, migration IDs, edge cases); this skill's job is **translation**, re-expressing that as something a person actually wants to read. Aggregating facts is the easy part; making them read as outcomes instead of implementation mechanics is the actual value here.

A project can have several audience **profiles** at once (e.g. a personal devlog, a users-facing note, and a stakeholder report, all from the same `built.md` history) — each tracked and generated independently.

**Step 1 — Determine which profile(s) this run is for:**
Read `.mfh/design/roadmap.md` for an `## Updates` section — each `###` subsection under it is one previously-defined profile, with **Category**, **Delivery**, and an optional **Standing guidance** line.

If no profiles exist yet, skip straight to "Define a new profile" below for exactly one profile, then continue to Step 2.

Otherwise ask via `AskUserQuestion` (multi-select): "Who is this update for?" — one option per existing profile, plus **Define a new profile**. More than one can be selected — each produces its own separate update this run.

**Define a new profile** (once per profile being defined; can happen alongside selecting existing ones):
1. Ask via `AskUserQuestion`: "What kind of audience is this?"
   - **My own history of what I've built** → category `personal`
   - **Updating users on what's new** → category `users`
   - **A client or stakeholder report** → category `client`
   - **People who don't know the project yet** → category `newcomers`
   - *(Other → ask what to call this category and treat it as a blend of `users` and `client` in tone unless the description says otherwise)*
2. If **Other** was picked, ask for a short name for the profile. Otherwise default the name to the category's label ("Mine" is a friendlier default than "Personal" if the user doesn't offer one — use judgment) and only ask if it's ambiguous.
3. Ask via `AskUserQuestion`: "How should this profile's updates be delivered?" — **Just leave the file for me to handle** / **Publish as a shareable Claude Artifact link** / **Something project-specific** (Other covers a custom pipeline to describe).
4. Ask (plain text, optional): "Any standing guidance for this profile — something to always mention or always avoid? Leave blank to skip."
5. Append the new profile to `.mfh/design/roadmap.md`'s `## Updates` section (create the section, inserted after **Current Track**, if this is the first profile ever):
   ```
   ### [Profile Name]
   **Category:** [personal | users | client | newcomers | custom label]
   **Delivery:** [answer]
   **Standing guidance:** [answer, omit this line entirely if left blank]
   ```

**Step 2 — Per-run inputs:**
Ask (plain text, since the useful answers here are inherently open-ended): "Anything specific to emphasize in this run? Leave blank if not." Applies to all profiles selected this run, on top of each one's own standing guidance.

If any selected profile is category `client`, also ask via `AskUserQuestion`: "Current status?" — **On track** / **At risk** / **Blocked** / **Complete**. Skip this question entirely if no `client` profile is in this run — it doesn't fit the other categories.

**Step 3 — Determine what's new, per profile:**
For each selected profile:
- Check `.mfh/updates/{slug}/latest.md` for its most recent entry's date (`{slug}` = the profile name, lowercased and hyphenated). If it doesn't exist yet (first run for this profile — check `.mfh/updates/{slug}/history/` too, in case it was already rotated out), treat this as a first update for that profile.
- Read `.mfh/state/built.md` entries newer than that date (or all of it, on a first run for this profile).
- **Exception — `newcomers` category ignores the cutoff for its main content.** A newcomer has no baseline to compare against, so read `.mfh/design/roadmap.md`'s **Live Sections** table in full and skim `.mfh/design/milestones.md`'s **Completed Milestones** section every time, regardless of what's "new." The delta still gets tracked internally (so the small "being refined right now" note stays accurate) but it never drives the main content.
- Read `.mfh/design/milestones.md` (next unstarted phase(s), and every currently-⬜ phase across active tracks — the latter feeds the `client` profile's suggestions/backlog table) and `.mfh/design/roadmap.md` (Goals) for the forward-looking half.
- Skim `.mfh/state/progress.md` for anything actively in flight worth a one-line mention.

If a non-`newcomers` profile has nothing new since its last update, say so and ask via `AskUserQuestion`: "Nothing's shipped for [profile] since last time. Still generate one (just the 'what's next' half), or skip it?" — **Generate it anyway** / **Skip it**. (`newcomers` profiles always have content — skip this check for them.)

**Step 4 — Draft each selected profile's update:**
Apply the selected profile's standing guidance and this run's ad-hoc emphasis (Step 2) throughout. Match structure to category:

**`personal`** — reflective, first-person, but not just a wall of prose. Break it into short `###` sections rather than one long narrative:
1. **Today** (or the session's actual timeframe) — the main narrative; the messy parts (dead ends, tradeoffs, things that didn't work) belong here, no length ceiling
2. **Fixed** — specific bugs/issues resolved; use the **Before/After device** (see below) for the one with the clearest story, plain prose for the rest
3. **Rough edges** — anything that went wrong or is still bothering you, even if it's not your bug to fix
4. **Picking up next time** — a short forward-looking note; every profile needs both a look-back and a look-forward, this one included

If there's something genuinely countable (bugs fixed, files touched, skills changed), add it to the meta line next to the date — a small tally, not a big deal, just enough that the entry doesn't read as flatter than the other profiles. Skip any of the four sections above that has nothing to say rather than padding it. No suggestions table, no platform snapshot — those are for profiles serving someone who isn't you.

**`users`** — "What's New": benefit-framed bullets (what you can do now, not how it works), ~300 words. Use the **Before/After device** for one or two headline fixes when there's a genuinely clear story — a short "Before: … / After: …" pairing, optionally with a one-line technical-cause note underneath for the curious. Don't force it onto every bullet. Close with a small **On the Horizon** note (1–3 sentences, from the next unstarted phase) — every update needs both a look-back and a look-forward, even a small one.

**`client`** — formal register. Open with the status line (from Step 2) and the relevant milestone/phase name. Sections in order:
1. **What was delivered** — plain-language outcomes tied to value, not implementation
2. **Key decisions** — bold label + one-sentence rationale, only decisions that affect scope/behaviour/what they'll see
3. **Outstanding items** — anything deferred, referencing the phase ID where relevant
4. **What's next** — a small table of the immediate next work
5. **Current suggestions** — every currently-⬜ phase across active tracks, as a `Priority | Suggestion | Status` table (priority/status only if inferable from the phase description — leave the cell as `—` otherwise, don't invent a priority that isn't there)
6. **Platform snapshot** — closing/reference section: one entry per `roadmap.md` Live Section (2–3 bullets each, current capabilities) plus anything else live but not yet in that table. This is a refreshed complete picture for a repeat reader, not new information — keep it compact.

Keep the narrative sections (1–4) under ~600 words combined; the two tables don't count against that.

**`newcomers`** — full project introduction, every time, not a delta:
1. **What this is** — from `roadmap.md`'s "What This Is"
2. **What you can do here** — the full feature tour, one entry per `roadmap.md` Live Section
3. A short callout for anything live but not yet in Live Sections (rolling out now)
4. **Being refined right now** — small closing note only, from the actual delta tracked in Step 3; day-to-day, everything above is what's stable

**All categories:** close with `*Generated [today's date] · [project name]*`.

**The Before/After device** (available to any category, not just `users`):
```
**Before:** [what the old behaviour was, in plain terms]
**After:** [what it is now]
```
Optionally followed by a one-line *Technical cause:* note. Use it only where a real before/after story exists — don't manufacture one for a change that doesn't have a clean "old way vs. new way" shape.

**Step 5 — Present the drafts:**
Show every drafted profile's Markdown together and ask via `AskUserQuestion`: "Do these look right?" — **Yes, finalize all** / **I want changes**. If changes are requested, find out which profile(s) need revision and re-present just those.

**Step 6 — Save and render, per profile:**
For each finalized profile:
- If `.mfh/updates/{slug}/latest.md` already exists, move it (and its `.html` counterpart) into `.mfh/updates/{slug}/history/` first, named with its own entry date — never overwrite history.
- Write the approved draft to `.mfh/updates/{slug}/latest.md`.
- Render a clean, self-contained HTML version to `.mfh/updates/{slug}/latest.html` — inline CSS, readable typography, no external dependencies or build step, and a print stylesheet (`@media print` + `@page`) so it holds up if exported to PDF or printed (page-break control on cards/tables, tighter margins, exact color printing for badges/pills). If the `artifact-design` skill is available in this environment, load it first for visual guidance on the HTML pass.

**Step 7 — Deliver, per profile's stated preference:**
- **"Just the file" / no strong preference given:** tell the user where it is and move on to the next profile.
- **Artifact link:** publish that profile's `latest.html` content via the Artifact tool and give the user the link.
- **Anything else project-specific** (e.g. SFTP, a custom conversion pipeline): follow it if you know how; if it needs tooling this general skill doesn't have, tell the user the file is ready and that delivery from here is on them (or a project-specific extension of this skill).

**Step 8 — Confirm:**
Summarize, per profile: ready, where it lives, and — if applicable — the delivery result or link.
