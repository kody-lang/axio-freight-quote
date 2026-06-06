# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-page freight rate calculator (PWA) for two carriers: **Townsend Delivery** and **J&J Van Lines**. There is **no build system, no dependencies, no framework, and no tests** — the entire application is hand-written HTML/CSS/JS inlined in one file. Live at https://axio-freight-quote.netlify.app.

## Commands

```bash
# Run locally — open the file directly (no server needed)
open index.html            # macOS

# Or serve over HTTP (required to exercise the service worker / PWA install)
python3 -m http.server 3000   # then http://localhost:3000

# Deploy to production (Netlify)
./deploy.sh                # runs: netlify deploy --dir=. --prod
```

Also hostable on GitHub Pages (Settings → Pages → `main` branch, root `/`).

## Architecture

Everything lives in **`index.html`** (~292 KB, written as three very long lines: an inline `<style>` block, the markup, then one big `<script>`). When editing, search by identifier rather than scrolling.

**`Code/axio-freight-calculator.html` is a byte-identical duplicate of `index.html`.** Any change to one must be mirrored to the other or they drift.

### Data model — the `CO` object

All rate logic is driven by the `CO` constant near the top of the `<script>`:

- `CO.t` — Townsend: has a flat `zones` map (`"Zone 1"`…`"Zone 4"`, `"SC Zone"`).
- `CO.j` — J&J: has `states` (NC, SC, GA, TN, FL, VA), each containing its own `zones`.
- Each carrier also has `fuel` (decimal surcharge, e.g. `.25`), `admin` (flat $ fee), and `pol` (an HTML string rendered into the policies panel).
- Each **zone** = `{ counties:[…], desc:"…", tiers:[…] }`.
- Each **tier** = `{ max, tp, r }` where `tp:"min"` is a flat minimum charge (`r` = dollars) and `tp:"lb"` is per-pound (`r` = $/lb). The open-ended top tier uses `max: 1/0` (Infinity sentinel).

`FURN` is a separate array of furniture presets (`{n, w}` name/weight, e.g. Sofa 225 lb) powering the item-weight picker.

### Quote pipeline

- `S` is the global UI state: `{ co, zone, zn, zd, wt, fu, jjst }` (selected carrier, zone, weight, picked furniture items, J&J state).
- `gZD()` resolves the active zone data, handling Townsend's flat zones vs. J&J's state-nested zones.
- `cQ(zd, w)` computes the quote: walks `tiers` to find the first whose `max ≥ w`. A `"min"` tier returns a flat base with **fuel and admin waived**; a `"lb"` tier computes `base = w * r`, then `total = Math.ceil(base + base*fuel + admin)`. Flags `ow` (overweight) when `w > 300`.
- `calc()` renders the result card from `cQ`. **All non-minimum charges are rounded up to the whole dollar** (`Math.ceil`).
- UI builders: `bCG` (Townsend county grid + search), `bSP` (J&J state pills), `bFG` (furniture picker), `uPol` (policy panel).

### Address lookup

Uses the Mapbox geocoding API (`_MAPBOX_TOKEN`, an inline public, domain-restricted `pk.` token) for address autocomplete, then auto-detects carrier/zone by matching the resolved county.

### PWA

`manifest.json` + `sw.js`. The service worker is **network-first for the HTML page** (users always get the latest when online) and **cache-first for static assets** (icons, manifest).

## Conventions & gotchas

- **Bump the `CACHE` version in `sw.js`** (currently `axio-freight-v8`) whenever assets change — otherwise installed PWA users keep stale cached copies. Git history shows this bump happening on essentially every functional change.
- **Keep `index.html` and `Code/axio-freight-calculator.html` in sync** — they are duplicates.
- **Rate updates** are made by editing the `tiers`/`fuel`/`admin` values in the `CO` object. Effective dates are tracked in `README.md` (Townsend 4/20/2026, J&J 1/11/2026).
- `*.rtf`, `*.pdf`, and a local `serve.py` are gitignored.
