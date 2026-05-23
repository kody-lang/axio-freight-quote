# AXIO Freight Quote

A single-page freight rate calculator for two carriers: **Townsend Delivery** and **J&J Van Lines**.

## Features

- County-level delivery zone routing for NC, SC, GA, TN, FL, and VA
- Per-pound and minimum-charge rate tiers with automatic fuel & admin surcharges
- Item weight picker (sofa, mattress, recliner, etc.) with patio furniture 2× multiplier
- Live quote calculation with itemized breakdown
- Carrier-specific policies panel

## Carriers

| Carrier | Service Area | Fuel Surcharge |
|---------|-------------|----------------|
| Townsend Delivery | Western/Piedmont NC + SC | 25% |
| J&J Van Lines | Eastern NC, SC, GA, Eastern TN, FL, VA | 20% |

## Usage

Open `index.html` directly in a browser — no build step or server required.

To run a local dev server:
```bash
python3 serve.py
# then open http://localhost:3000
```

## Hosting on GitHub Pages

1. Push this repo to GitHub
2. Go to **Settings → Pages**
3. Set source to the `main` branch, root `/`
4. The calculator will be live at `https://<username>.github.io/<repo>/`

## Rate Updates

Rates are defined in the `CO` object near the top of `index.html`. Each zone has a `tiers` array with per-pound rates and minimum charges.

Effective dates:
- Townsend Delivery: **4/20/2026**
- J&J Van Lines: **1/11/2026**
