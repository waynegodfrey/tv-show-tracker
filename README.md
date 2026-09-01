# Godfrey TV Show Tracker

A single self-contained `index.html` (no build step) published via GitHub Pages at:
https://waynegodfrey.github.io/tv-show-tracker/

## What this is

A dashboard for the Godfrey household's tracked shows: which streaming service each
is on, when the most recent episode aired, and when the next one (or next season)
is expected. Built for quick access from a phone home-screen bookmark.

## Architecture

Everything lives in `index.html`. The "This Week" strip and each airing show's
"most recent"/"next episode" text and sort order are computed **live in the
browser** via a `<script>` block, using the viewer's America/New_York calendar
date. Only the underlying DATA needs editing — never the computed display text.

Airing shows live inside `<div id="airingList">`. Each card has `data-mode`:

- `data-mode="cadence"` (`data-weekday` 0=Sun..6=Sat, `data-start`, `data-end`,
  ISO dates): assumes a strict every-7-days rhythm. Only use for streaming-only
  shows with no preemptions (currently Reacher, Lioness, Silo).
- `data-mode="dates"` (`data-dates='["2026-10-06",...]'` JSON array of every
  confirmed episode date, `data-complete="true"` once the finale is included):
  always use for CBS/NBC broadcast shows, since those get bumped for holidays,
  awards shows, and sports overruns. A fixed cadence assumption is known to be
  wrong for these.

`window.DATED_EVENTS` (a `{name, date}` list in the same script) drives the
"This Week" strip for shows with only a single confirmed future date that
aren't yet in `#airingList`. Once a show's season actually starts airing, move
it out of `DATED_EVENTS` and into `#airingList` as a `data-mode="dates"` card.

Sections in the page: "New Episodes Now," "On Hiatus — Returning This Fall,"
"On Hiatus — No Date Yet," and "No Longer Producing New Episodes."

## Deployment

Plain GitHub Pages, serving `index.html` from the `main` branch root — no
Actions workflow, no build step. Any push to `main` goes live within about a
minute. A repo-scoped deploy key (write access) is used by the automation
below to push updates without a human in the loop.

## Automation

A scheduled task researches each tracked show weekly, and — only when
something actually changed — edits `index.html` directly in a local clone and
pushes. Quiet weeks (the common case) result in no commit at all, since the
live script keeps showing correct dates for any show already correctly
encoded, with no edit required.

## Tracked shows (baseline as of Sep 1, 2026 — check the live file for current state)

- **Lioness** — Paramount+ — S3 airing, cadence mode, Sundays, 2026-08-02 to 2026-09-20
- **Silo** — Apple TV+ — S3 airing, cadence mode, Fridays, 2026-07-03 to 2026-09-04
- **Reacher** — Prime Video — S4 airing, cadence mode, Wednesdays, 2026-08-12 to 2026-09-16
- **Marshals** — Paramount+/CBS — S2 premieres 2026-10-04
- **Tracker** — Paramount+/CBS — S4 premieres 2026-10-04
- **FBI** — Paramount+/CBS — S9 premieres 2026-10-05
- **NCIS** — Paramount+/CBS — S24 premieres 2026-10-06
- **Chicago Med** — Peacock/NBC — S12 premieres 2026-10-07
- **Chicago Fire** — Peacock/NBC — S15 premieres 2026-10-07
- **Sheriff Country** — Paramount+/CBS — S2 premieres 2026-10-09
- **Fire Country** — Paramount+/CBS — S5 premieres 2026-10-09
- **Ballard** — Prime Video — S2 renewed, no confirmed date (est. late 2026-early 2027)
- **Matlock** — Paramount+/CBS — S3 renewed, midseason, expected ~Jan 2027, no confirmed date
- **NCIS: Sydney** — Paramount+/CBS — S4 renewed, no confirmed date (expected 2027)
- **Landman** — Paramount+ — S3 renewed, filming Sep 2026-Q1 2027, outlook mid-to-late 2027
- **Ride or Die** — Prime Video — S2 renewed (confirmed Aug 31, 2026), no premiere date yet
- **Watson** — Paramount+/CBS — cancelled by CBS March 2026 after 2 seasons

## Note on the older Anthropic Artifact version

This started as a Claude "Artifact" hosted page (claude.ai/code/artifact/...).
That version still exists and is not affected by this repo — it was kept
running in parallel intentionally, for comparison. It has two limitations this
GitHub Pages version doesn't: a public share link there can't auto-track the
latest published version (a human has to manually re-pin it after every
update), and its scheduled-task environment can't reach the Artifact publish
tool at all, so its automation can only research and flag changes, not apply
them. Both problems disappear here since GitHub Pages just serves whatever
was last pushed, and the update mechanism is plain git rather than a
tool that's only available in interactive chat sessions.
