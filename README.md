# NFLBOT 1.3 — standalone site

This is a self-contained static site: `index.html` plus its one data file,
`games_slim.csv`. No build step, no server-side code, no dependencies beyond
the three Google Fonts loaded over the network. Everything else (the Elo
model, the totals model, all the UI) runs client-side in plain JavaScript.

## Test it locally

**Don't double-click `index.html`.** Browsers block `fetch()` of local files
under the `file://` protocol, and this page fetches `games_slim.csv` on
load — opened that way, it'll sit on "loading current ratings…" forever
with no games, odds, or rankings, because the data file never actually
loads. It needs to be served over `http://`, even just locally.

The easy way: double-click **`start.bat`**. It starts a local server and
opens the page in your browser automatically. A console window stays open
while it runs — closing that window stops the server.

The manual way, from this folder:

```
python -m http.server 8000
```

then open `http://localhost:8000/`. (Any static server works — `npx serve`,
VS Code's Live Server extension, etc.)

## Deploy it for real

Any static host works, since it's just two files. A few easy options:

- **GitHub Pages** — push this folder to a repo, enable Pages on the `main`
  branch (or a `docs/` folder), done.
- **Netlify** — drag the `site` folder onto netlify.com/drop for an instant
  URL, or connect a GitHub repo for auto-deploys on push.
- **Vercel** — `vercel deploy` from inside this folder (or connect the repo
  in their dashboard).

All three are free for a project this size and give you a real public URL.

## Editing it

It's plain HTML/CSS/JS in one file — open it in any editor (VS Code, another
AI coding tool, or ask Claude Code to keep editing it directly) and change
away. A few things worth knowing before you do:

- **Data is a snapshot.** `games_slim.csv` was exported from the
  [nflverse](https://github.com/nflverse/nfldata) game log at build time.
  It won't update itself — regenerate it (see `../data.py` and
  `../cli.py` in the parent folder) and re-copy it here when you want fresher
  results.
- **The model constants live near the top of the `<script>` block** —
  `BASE`, `K`, `HFA`, `ALPHA_PTS`, `TOTAL_BIAS`, `MIN_EDGE_SPREAD`,
  `MIN_EDGE_TOTAL`, `qbPenalty` — each has a comment explaining what it does
  and, where relevant, why that value was chosen.
- **CSS custom properties** at the top of the `<style>` block control the
  whole palette, light and dark. Change a `--token` once and it propagates
  everywhere it's used.

## Keeping this in sync with the Claude Artifact version

This file started as a straight export of the Claude Artifact at
`../artifact/index.html` — the same page, just wrapped in a full
`<!doctype html><html>…` document instead of relying on the Artifacts
platform to supply that wrapper. If you go back to that chat and ask for
more changes there, ask it to also rebuild this copy (it can rerun
`../build_site.py`, which does the wrapping automatically) — or just copy
its edits over by hand. The two aren't linked automatically, so it's easy
for this copy to fall behind (it did, once already, after the artifact got
its dark "tactical HUD" redesign and rename to NFLBOT 1.3).
