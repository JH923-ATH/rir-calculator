# RIR Calculator

A small PWA that calculates Week 1 starting weights for a new training mesocycle, based on your best top set from the end of the previous one.

**Live:** https://jh923-ath.github.io/rir-calculator/
**Repo:** https://github.com/JH923-ATH/rir-calculator

## What it does

For each exercise, you pick a lift and enter:

- **Exercise** — chosen from a built-in picker (grouped by muscle group, with search). You can also add your own custom exercises, which are saved and reusable across cards.
- **Last mesocycle's best top set** — weight, reps, and RIR (reps in reserve)
- **This mesocycle's Week 1 target** — target reps and target RIR (defaults to 3, per the standard Week 1 effort level)

It estimates your e1RM with the Epley formula, adjusted for RIR:

```
effectiveReps = reps + RIR
e1RM = weight * (1 + effectiveReps / 30)
```

Then it back-calculates two suggested Week 1 weights, both rounded to the nearest increment (default 5 lb):

- **Aggressive** — targets your set default RIR directly
- **Conservative** — assumes one extra RIR of buffer (slightly lighter, safer first-session weight)

Entries, custom exercises, and settings (default RIR, rounding increment) persist in the browser via `localStorage`, so you can close the tab and come back mid-setup.

## Files

| File | Purpose |
|---|---|
| `index.html` | The whole app — markup, styles, and logic in one file |
| `manifest.json` | PWA manifest (name, icons, theme colors, display mode) |
| `sw.js` | Service worker — caches the app shell for offline use |
| `icon-192.png`, `icon-512.png` | App icons for home-screen install |
| `favicon.png` | Browser tab icon |

No build step, no dependencies, no framework — plain HTML/CSS/JS.

## Running locally

Because the service worker requires `http(s)`, opening `index.html` directly via `file://` will work for the calculator itself but won't register the service worker. To test the full PWA behavior, serve it locally:

```bash
# Python
python3 -m http.server 8000

# or Node
npx serve .
```

Then visit `http://localhost:8000`.

## Deploying

The `main` branch is deployed automatically to GitHub Pages on every push (Settings → Pages → Deploy from a branch → `main` / root). No CI config needed — just push to `main` and the site rebuilds within a minute or two.

## Git setup note

This folder was initialized with `git init` and `origin` pointed at the GitHub repo, but the sandbox this was set up from can't reach github.com directly to fetch history. Before you start committing from your editor, run this once to properly sync with the remote:

```bash
git fetch origin
git reset --hard origin/main
```

Since the local files already match what's on GitHub exactly, this is a no-op for file content — it just links up the real commit history so `git log`, `git pull`, and `git push` behave normally from here on.

## Design notes

- Dark theme, Inter typeface, cards with layered shadows/gradients.
- Barbell icon (drawn programmatically, not a stock asset) used for both the in-app header mark and the home-screen icon.
- Mobile-first layout — built primarily for installing on an Android home screen via Chrome's "Add to Home Screen."
