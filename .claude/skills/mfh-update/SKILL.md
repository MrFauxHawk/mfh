---
description: Generate friendly, shareable project updates for one or more audience profiles — recent progress and what's next
---

# /mfh-update

You are generating one or more project updates: readable summaries of what's happened recently and what's coming next. This is not a raw dump of `built.md` — `built.md` is written dense and technical so a future AI session has full context (schema fields, migration IDs, edge cases); this skill's job is **translation**, re-expressing that as something a person actually wants to read. Aggregating facts is the easy part; making them read as outcomes instead of implementation mechanics is the actual value here.

A project can have several audience **profiles** at once (e.g. a personal devlog, a users-facing note, and a stakeholder report, all from the same `built.md` history) — each tracked and generated independently. The document design itself — layout, typography, and devices like Before/After and StatTile — is a fixed system MFH ships with; the only things that vary between projects are the actual colors, and, per run, which profiles are in play.

**Step 1 — Determine this run's setup:**
Check whether `.mfh/updates/last-run.md` exists.

**No previous run (first time for this project):** this is a baseline run. Go through "Audience selection" and then "Visual settings" below in full, then continue to Step 2.

**A previous run exists:** ask via `AskUserQuestion`: "How do you want to run this update?"
- **Use previous settings** (Recommended) — reuse the exact profile selection and visual settings recorded in `last-run.md`. Skip both sub-sections below entirely and go straight to Step 2.
- **Change audience, keep visual settings** — go through "Audience selection" below; reuse the cached color source and light/dark mode as-is.
- **Change visual settings, keep audience** — reuse the cached profile selection (if any cached profile no longer exists in `roadmap.md`'s `## Updates` section, drop it and say so); go through "Visual settings" below.
- **Start from scratch** — go through both sub-sections below in full, as if this were a baseline run.

**Audience selection:**
Read `.mfh/design/roadmap.md` for an `## Updates` section — each `###` subsection under it is one previously-defined profile, with **Category**, **Delivery**, and an optional **Standing guidance** line.

If no profiles exist yet, skip straight to "Define a new profile" below for exactly one profile.

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

**Visual settings:**
1. Check `.mfh/library/` (commonly, but not only, a file named `style.md`) for actual documented color values — a token table, hex codes, a "brand colors" list — not just generic advice like "use Tailwind."
   - If one exists: ask via `AskUserQuestion`: "Which colors for this update?" — **[Project Name]** (this project's own documented brand colors — get the name from `roadmap.md`'s title line) / **MFH** (MFH's own standard look, independent of this project's branding).
   - If none exists anywhere in `.mfh/library/`: use **MFH** automatically — don't ask.
2. Ask via `AskUserQuestion`: "Light or dark?" — **Light** / **Dark**.

Both answers get written to `.mfh/updates/last-run.md` in Step 6, becoming next run's default.

**Step 2 — Per-run inputs:**
Ask (plain text, since the useful answers here are inherently open-ended): "Anything specific to emphasize in this run? Leave blank if not." Applies to all profiles selected this run, on top of each one's own standing guidance.

If any selected profile is category `client`, also ask via `AskUserQuestion`: "Current status?" — **On track** / **At risk** / **Blocked** / **Complete**. Skip this question entirely if no `client` profile is in this run — it doesn't fit the other categories.

**Step 3 — Determine what's new, per profile:**
For each selected profile:
- Check `.mfh/updates/{slug}/latest.md` for its most recent entry's date (`{slug}` = the profile name, lowercased and hyphenated). If it doesn't exist yet (first run for this profile — check `.mfh/updates/{slug}/history/` too, in case it was already rotated out), treat this as a first update for that profile.
- Read `.mfh/state/built.md` entries newer than that date (or all of it, on a first run for this profile).
- **Exception — `newcomers` category ignores the cutoff for its main content.** A newcomer has no baseline to compare against, so read `.mfh/design/roadmap.md`'s **Live Sections** table in full and skim `.mfh/design/milestones.md`'s **Completed Milestones** section every time, regardless of what's "new." The delta still gets tracked internally (so the small "being refined right now" note stays accurate) but it never drives the main content.
- Read `.mfh/design/milestones.md` — every currently 🔄/🟡 phase across active tracks (feeds the `client` profile's Active Work table and the `personal` profile's Still Open note), and every currently-⬜ phase including its **Priority** column where present (feeds the `client` profile's Planned Work table and the `personal` profile's top-priority highlight) — and `.mfh/design/roadmap.md` (Goals) for the forward-looking half.
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

If any currently-⬜ phase across active tracks is tagged **Critical** or **High**, add a short **Top priority right now** line (just the top 1–2, not a list of everything) — this is the one piece of forward-looking info worth surfacing even in a profile that's otherwise just for you. Skip it entirely if nothing's tagged that high, rather than listing a Medium/Low item to fill the space.

If any currently 🔄/🟡 phase across active tracks *isn't* already covered by today's narrative, add a short **Still open** line naming it (phase ID and name only, not the full description) — a lightweight nudge that something's mid-flight elsewhere, most useful for catching a phase that's gone stale (nobody's touched it in weeks) before it's forgotten entirely. Skip it if the only thing in flight is exactly what "Today" already narrated — no need to repeat yourself.

**`users`** — "What's New": benefit-framed bullets (what you can do now, not how it works), ~300 words. Open with a **StatTile row** of New/Improved/Fixed counts for this update, using the stat-tile red/orange/green/blue family (see the StatTile device note below) — New is blue, Improved is green, Fixed is orange — the same family every other profile's StatTiles use, so a reader flipping between profiles sees one consistent set of tile colors rather than each profile inventing its own. Tag each bullet New/Improved/Fixed with the small chip too (see the tag-chip device note) — the chip text reuses these exact same hex values, not a separately-tuned shade. Use the **Before/After device** for one or two headline fixes when there's a genuinely clear story — a short "Before: … / After: …" pairing, optionally with a one-line technical-cause note underneath for the curious. Don't force it onto every bullet. Close with a small **On the Horizon** note (1–3 sentences, from the next unstarted phase) — every update needs both a look-back and a look-forward, even a small one.

**`client`** — formal register. Open with the status line (from Step 2) and the relevant milestone/phase name. Sections in order:
1. **What was delivered** — plain-language outcomes tied to value, not implementation
2. **Key decisions** — bold label + one-sentence rationale, only decisions that affect scope/behaviour/what they'll see
3. **Outstanding items** — anything deferred, referencing the phase ID where relevant
4. **Active Work** — every currently 🔄/🟡 phase across active tracks, as an `Item | Status` table — the full picture of what's happening right now, not just a single "next" item. Status reads "Ready to close" for 🟡, "In progress" for 🔄, taken directly from the icon, not guessed. Sort ready-to-close first, then in-progress (ties broken by track order) — a phase waiting to be closed is more actionable right now than one still being built. No priority column here; priority is for deciding what to pick up next among work that hasn't started, and this is already committed effort, not a decision point.
5. **Planned work** — every currently-⬜ phase across active tracks, as a `Priority | Item | Status` table, sorted Critical → High → Medium → Low (ties broken by track order). Status is always "Not started," since that's what ⬜ means — this is committed backlog, not speculative ideas, so don't frame it as suggestions. Pull priority straight from `milestones.md`'s **Priority** column where a phase has one. A handful of phases predate that column and won't have one — group those at the bottom of the table (below anything with a real priority) rather than interleaving guesses, and add a one-line note that these predate priority tracking, only if at least one such phase is actually present. In the HTML render, color the priority word itself using the same stat-color family as everywhere else — Critical red, High orange (the same orange as the Users profile's "Fixed" tag and stat-tile), Medium green, Low grey. This is hardcoded, not brand-derived — the meaning of "Critical" doesn't change with the project's palette.
6. **Platform snapshot** — closing/reference section: one entry per `roadmap.md` Live Section (2–3 bullets each, current capabilities) plus anything else live but not yet in that table. This is a refreshed complete picture for a repeat reader, not new information — keep it compact. Each card's header is a solid accent-filled bar with white text, matching the eyebrow badge's treatment — not a soft tint.

Keep the narrative sections (1–3) under ~600 words combined; the three tables (Active Work, Planned Work, and Platform Snapshot's grid) don't count against that.

**`newcomers`** — full project introduction, every time, not a delta:
1. Open with a **StatTile row** of real scale/maturity numbers — apps or sections live, people using it, milestones shipped, whatever's genuinely countable and gives a stranger a sense this is real and established. Every tile uses the primary accent (see the StatTile device note below on why — there's no good/bad meaning to invent here).
2. **What this is** — from `roadmap.md`'s "What This Is"; if the project also documents a Goals/Vision section, fold in a line of *why* it exists, not just what it does — a newcomer benefits from purpose framing more than any other profile
3. **What you can do here** — the full feature tour, one entry per `roadmap.md` Live Section. Each entry renders as a small card with a 4px top-border in the secondary accent — tying it visually to the rest of the document's card language instead of a plain unstyled box, even without an icon
4. A short callout for anything live but not yet in Live Sections (rolling out now)
5. **Being refined right now** — small closing note only, from the actual delta tracked in Step 3; day-to-day, everything above is what's stable

**All categories:** close with `*Generated [today's date] · [project name]*`. Each document also opens with a small filled eyebrow badge naming the profile ("Personal Update," "What's New," "Stakeholder Report," "Project Introduction") — solid accent background, white text — regardless of which palette or brand is in play; this is a fixed part of the document system, not something that varies by category or color source.

**The Before/After device** (available to any category, not just `users`):
```
**Before:** [what the old behaviour was, in plain terms]
**After:** [what it is now]
```
Optionally followed by a one-line *Technical cause:* note. Use it only where a real before/after story exists — don't manufacture one for a change that doesn't have a clean "old way vs. new way" shape.

**The StatTile device** (available to any category): a small row of stat cards for anything genuinely countable (bugs fixed, phases complete, commits, outstanding items, live sections). This exact treatment is locked in as part of MFH's own design system — not something to detect or reinvent per project: a surface-color card, small border-radius, a 4px colored top accent, a bold ~2rem value, and a small muted uppercase label underneath. Use it when a profile has 2–4 real numbers worth headlining; skip it rather than padding with a stat that isn't actually meaningful. When a stat has no inherent status meaning (e.g. `newcomers`' "apps live" or "people using it"), just use the document's primary accent for every tile rather than inventing a false red/orange/green/blue meaning — the colored variation below is for when tiles genuinely mean different things (good/at-risk/neutral/new), not decoration.

Optionally, a tile can carry a small delta line underneath the label (e.g. "↓ 3 fewer than last entry") when there's a genuine prior value to compare against — `personal` uses this most. Color it with the same hardcoded family: `--stat-green` when the change is a good-news direction, neutral muted ink when the direction has no inherent good/bad meaning. Don't invent a palette-derived color here — same reasoning as everywhere else in this family. "Increased" and "unchanged" share that same neutral color (neither is good or bad), so differentiate them by weight instead — unchanged renders lighter and italic, increased stays at the line's normal weight — otherwise the two read as identical and it's unclear whether anything changed at all.

When a tile *does* carry meaning (bugs fixed, outstanding items, phases complete, and the `users` profile's New/Improved/Fixed counts), use this hardcoded four-color family instead of the document's brand accent — identical across every palette, never brand-derived, because "outstanding" or "critical" doesn't mean something different just because a project's brand happens to be blue instead of red:
- Red `#dc2626` — problems/attention (outstanding items, Critical priority)
- Orange `#ea580c` — pending/in-between (queued/planned items, High priority, the `users` profile's "Fixed")
- Green `#16a34a` — good/complete (phases complete, commits, Medium priority, the `users` profile's "Improved")
- Blue `#2563eb` — new (the `users` profile's "New" count only — deliberately not red, since red already means "problem" here)

**The tag-chip device** (`users` only): small inline pills tagging each bullet and summarizing the update's mix up top — New/Improved/Fixed, using the exact same red/orange/green/blue family as the StatTile device above (blue/orange/green respectively), not a separately-tuned shade. The summary StatTile row and the per-bullet chips should look like one literal system, down to the hex value.

**Step 5 — Present the drafts:**
Show every drafted profile's Markdown together and ask via `AskUserQuestion`: "Do these look right?" — **Yes, finalize all** / **I want changes**. If changes are requested, find out which profile(s) need revision and re-present just those.

**Step 6 — Save and render, per profile:**
For each finalized profile:
- If `.mfh/updates/{slug}/latest.md` already exists, move it (and its `.html` counterpart) into `.mfh/updates/{slug}/history/` first, named with its own entry date — never overwrite history.
- Write the approved draft to `.mfh/updates/{slug}/latest.md`.
- Render a clean, self-contained HTML version to `.mfh/updates/{slug}/latest.html` — inline CSS, readable typography, no external dependencies or build step, and a print stylesheet (`@media print` + `@page`) so it holds up if exported to PDF or printed (page-break control on cards/tables, tighter margins, exact color printing for badges/pills).
- Apply the color source and light/dark mode decided in Step 1's "Visual settings":
  - **MFH:** use MFH's own locked-in default palette — no per-project derivation needed. Primary/secondary role assignment (below) is fixed regardless of source; teal happens to be primary and gold secondary here because that's what the reference palette's own real UI does (a CTA-button color vs. a supporting nav-button color), not because of any rule either color's source project applies to itself.
    - **Light:** background `#f1f5f9`, surface `#ffffff`, ink `#0a243f`, muted ink `#64748b`, faint ink `#94a3b8`, accent (primary, teal) `#0d9488`, soft accent tint `#ccfbf1`, secondary accent (gold) `#d99410`, soft secondary tint `#fdd98a`, rule `#e2e8f0`.
    - **Dark:** background `#051220`, surface `#0a243f`, ink `#f1f5f9`, muted ink `#94a3b8`, faint ink `#64748b`, accent (primary, teal) `#0d9488`, soft accent tint `#0f2c29`, secondary accent (gold) `#fbac15`, soft secondary tint `#3d2f0f`, rule `#1a3a5f`. Navy (background/surface here) is the same value used as light-mode ink — one color playing both roles is intentional, not a mistake.
  - **`[Project Name]`:** derive this project's own colors from whatever `.mfh/library/` documents:
    - Use its primary/CTA color as this document's accent.
    - If it documents a secondary/complementary color, map it to this document's own secondary role (StatTile tiles without inherent meaning, the Rough Edges border, the Platform Snapshot header, callouts) — freely, not restricted to a narrow slice like an active-tab underline. This document's primary/secondary usage is decided by this skill, not by whatever internal rule the source project applies to its own UI (e.g. "accent only, never primary" is a rule about *that* project's interface, not about this one) — don't carry a source app's own usage restriction over into this document.
    - Map its documented semantic colors (success/error/warning, or equivalent) onto this document's status pills (on track / blocked / at risk) instead of inventing new ones.
    - Reuse its documented neutral/background/text colors for this document's background and ink if given.
    - Treat the doc as authoritative — its stated rules override any general design preference here.
    - If the palette is only usable in one mode (e.g. no dark-mode equivalents given) and this run needs the missing side, it's fine to derive a reasonable guess rather than blocking on it.
    - If a font is documented, use it for the body face; keep the display/meta face pairing otherwise.
    - If multiple files in `.mfh/library/` document colors (rare), prefer the one most clearly about visual/brand style over one that's mostly about layout or code conventions.
    - Note in the confirmation (Step 8) which file the palette came from.
  - Either way, the structural design — layout, typography pairing, card/tab treatment, and the devices above — stays exactly the same; only the token *values* change with color source.
- Write (creating it if this is the first run) `.mfh/updates/last-run.md` with this run's profile selection and visual settings, so the next run can offer to reuse them:
  ```markdown
  # Last Update Run

  **Profiles:** [comma-separated profile slugs selected this run]
  **Color source:** MFH | [Project Name]
  **Mode:** Light | Dark
  **Date:** [today's date]
  ```
- If the `artifact-design` skill is available in this environment, load it first for visual guidance on the HTML pass.

**Step 7 — Deliver, per profile's stated preference:**
- **"Just the file" / no strong preference given:** tell the user where it is and move on to the next profile.
- **Artifact link:** publish that profile's `latest.html` content via the Artifact tool and give the user the link.
- **Anything else project-specific** (e.g. SFTP, a custom conversion pipeline): follow it if you know how; if it needs tooling this general skill doesn't have, tell the user the file is ready and that delivery from here is on them (or a project-specific extension of this skill).

**Step 8 — Confirm:**
Summarize, per profile: ready, where it lives, the color source and mode used (MFH default, or `[Project Name]`'s own colors picked up from `[filename]`) — noting this is now saved as the default for next run — and, if applicable, the delivery result or link.
