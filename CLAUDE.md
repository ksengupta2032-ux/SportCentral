# The Wire — Sports Tracker

A single-file static website that shows live scores, upcoming schedules, and
standings across every major sport (NFL, NBA, MLB, NHL, Premier League, Golf,
MMA, Tennis, plus placeholders for WNBA, NCAA football/basketball, La Liga,
Serie A, Bundesliga, Ligue 1, MLS, Champions League, World Cup, NASCAR, and
Cricket).

## Repo structure

```
index.html   — the entire site (HTML + CSS + JS in one file, no build step)
CLAUDE.md    — this file
```

There is no backend, no package.json, and no build tooling. The site is
meant to be opened directly or served as static files (e.g. GitHub Pages).

## How it works

- All sport data lives in a single `DATA` object near the top of the
  `<script>` block in `index.html`. Each key is a league (`nfl`, `nba`,
  `mlb`, etc.) with a `label`, a `loaded` flag, and either a `games` array
  (head-to-head matchups), an `events` array (golf/MMA/tennis-style
  schedules), and/or a `standings` array.
- Leagues with `loaded: false` render a placeholder card with a "Get live
  data" button instead of a game grid.
- The `ORDER` array controls tab order down the page.
- Tabs, cards, and standings tables are all rendered client-side by plain
  JS functions (`renderLeague`, `gameCard`, `eventCard`, `standingsTable`)
  — no frameworks, no dependencies.

## Data refresh model — important

**This site has no live API connection.** All scores/schedules/standings
are a hand-embedded snapshot from the last time Claude fetched them via its
sports-data tool. There is no client-side key to call that tool from the
browser.

Refreshing data means:
1. The person asks Claude (in claude.ai chat) to pull fresh data for a
   league.
2. Claude fetches it and edits the relevant entry in the `DATA` object in
   `index.html`.
3. The updated `index.html` gets committed/pushed.

Each league section also has a "Refresh" button wired to
`window.sendPrompt(...)`, which only works inside a Claude.ai artifact
(it sends a chat message asking Claude to update that board) — it does
nothing when the file is hosted standalone on GitHub Pages. If deploying
outside claude.ai, that button can be left as a harmless no-op or removed.

## Design system

- Fonts: Oswald (display/scoreboard numerals), Barlow Condensed (labels,
  tabs, eyebrows), Inter (body text). Loaded from Google Fonts.
- Palette: near-black background (`--bg: #0a0c10`), amber accent
  (`--amber: #ffb627`) for the scoreboard feel, red (`--live: #ff4d4d`) for
  live-game indicators, teal (`--teal: #2dd4bf`) as a secondary accent.
- All colors/spacing are CSS custom properties at the top of `<style>` —
  change the palette there, not inline.
- Keep the "scoreboard" visual language (tabular numbers, amber glow,
  pulsing live dot) consistent if adding new card types.

## Conventions when editing

- Keep everything in one file unless the person explicitly asks to split
  it up — this project is intentionally dependency-free and buildless.
- When adding a new league to `DATA`, follow the existing shape exactly
  (`games` items need `status` of `live` / `final` / `sched`) so the
  existing render functions pick it up with no new code.
- Don't add a JS framework, bundler, or npm dependency without asking —
  the whole point of this project is that it runs by opening the HTML
  file.
- If asked to deploy: this is a plain static site, so GitHub Pages
  (serving `index.html` from the repo root or `/docs`) is the simplest
  option — no Actions workflow is required unless the person wants one.
