# TopSet

A small PWA that calculates Week 1 starting weights for a new training mesocycle, based on your best top set from the end of the previous one — plus a standalone 1RM estimator and a full Material 3 app shell (bottom nav, Settings, Guide). Built with an eventual native Android wrapper in mind.

**Live:** https://jh923-ath.github.io/rir-calculator/
**Repo:** https://github.com/JH923-ATH/rir-calculator

## What it does

The app opens with a brief splash screen, then (on first visit only) a 3-slide onboarding carousel introducing the Week 1 Planner, the 1RM Calculator, and the Guide tab. After that it's a 3-tab shell:

- **Calculator** — the Week 1 Planner and 1RM Calculator, switched via a segmented control at the top
- **Guide** — a static explainer for the Epley formula, Conservative vs. Aggressive, and the 3-step flow
- **Settings** — Default RIR, rounding increment, units, theme, custom exercises, and clearing all entries

### Week 1 Planner

For each exercise, you pick a lift and enter:

- **Exercise** — chosen from a built-in picker (grouped by muscle group, with search). You can also add your own custom exercises, which are saved and reusable across cards, and managed (removed) from Settings.
- **Last mesocycle's best top set** — weight, reps, and RIR (reps in reserve)
- **This mesocycle's Week 1 target** — target reps and target RIR (defaults to your Default RIR setting, 3 out of the box)

It estimates your e1RM with the Epley formula, adjusted for RIR:

```
effectiveReps = reps + RIR
e1RM = weight * (1 + effectiveReps / 30)
```

Then it back-calculates two suggested Week 1 weights, both rounded to the nearest increment:

- **Aggressive** — targets your set default RIR directly
- **Conservative** — assumes one extra RIR of buffer (slightly lighter, safer first-session weight)

**Bodyweight exercises:** leave the weight field blank (e.g. pull-ups, dips, push-ups) and the planner switches to solving for a **target rep count** instead of a load — reps to failure (last reps + last RIR) minus the RIR you want in reserve, floored at 0.

Entries, custom exercises, and settings persist in the browser via `localStorage`, so you can close the tab and come back mid-setup.

### 1RM Calculator

A standalone one-rep-max estimator. For each lift you pick an exercise and enter the **weight** and **reps** of a set taken to (or near) failure. It estimates your 1RM with the plain Epley formula:

```
e1RM = weight * (1 + reps / 30)
```

and shows a table of common training percentages (95% down to 60%), each rounded to the nearest increment. Planner entries and 1RM entries are stored separately, and the active mode is remembered between visits.

### Settings

- **Default RIR** — a stepper (0–10, in 0.5 steps) for the Week 1 target effort level.
- **Rounding increment** — 2.5 / 5 / 10, applied to every Planner and 1RM Calculator result.
- **Units** — lb or kg. All weight is stored internally in lb; the display unit only affects what you see (inputs and results), and rounding happens in whichever unit is currently selected so results land on a clean number either way.
- **Dark theme** — follows your system preference by default; the switch overrides it explicitly and updates immediately, including the browser's own theme-color chrome.
- **Manage custom exercises** — remove any custom exercise you've added from the picker.
- **Clear all entries** — resets both the Planner and 1RM Calculator back to a single blank card each.

## Files

| File | Purpose |
|---|---|
| `index.html` | The whole app — markup, styles, and logic in one file |
| `manifest.json` | PWA manifest (name, icons, theme colors, display mode) |
| `sw.js` | Service worker — caches the app shell for offline use |
| `icon-192.png`, `icon-512.png` | App icons for home-screen install |
| `favicon.png`, `favicon.ico` | Browser tab icon |

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

- Material 3 dark/light theme (teal accent), following system preference by default; Roboto Flex for headings/numbers, Roboto for UI text.
- Bottom navigation (Calculator / Guide / Settings) with a floating action button for adding exercises, matching the Android mockup this redesign was built from.
- Barbell icon (drawn programmatically, not a stock asset) used throughout — header, splash, onboarding, and empty states. Note: the static home-screen icon files (`icon-192.png`, `icon-512.png`, `favicon.png`/`.ico`) still use the original blue palette and haven't been regenerated to match.
- Mobile-first layout — built primarily for installing on an Android home screen via Chrome's "Add to Home Screen," with a native Android wrapper in mind long-term.
