# AXIO Freight Calculator — Changelog

Live: https://axio-freight-quote.netlify.app
Quote Log Sheet: https://docs.google.com/spreadsheets/d/1TSOmdPxJaK6-GZ4uqLJcht0fiJIN0xZqo3lJMfVu4Ts/edit

---

## 2026-06-27 — Counties added: Surry, Stokes, Scotland → J&J NC Zone 1

Service worker cache **v13 → v14**.

Added three previously-unserviced NC counties to **J&J Van Lines, North Carolina Zone 1** (min $170; $0.70/lb 251–1000, $0.68 1001–1999, $0.66 2000+; 20% fuel):
- **Scotland** — on J&J's rate sheet; neighbors Hoke/Robeson/Cumberland are all J&J NC Zone 1.
- **Surry** and **Stokes** — geographically Townsend's area but absent from Townsend's rate sheet; per Kody, quoted at **J&J NC Zone 1** rates (J&J covers them).

Before, any of these addresses returned "Not serviced in this area." Now the address lookup routes them to J&J NC Zone 1. Verified end-to-end via Mapbox (Mount Airy→Surry, King→Stokes, Laurinburg→Scotland all resolve to J&J NC Zone 1). Commit follows.

---

## 2026-06-27

Service worker cache **v12 → v13** (forces installed home-screen apps to update — close & reopen the app once on Wi-Fi).

### Overweight threshold 300 → 375 lbs (per piece, both carriers)
The overweight-surcharge note now reads **"$1.00/lb over 375 lbs per piece"** (was 300), for **both Townsend and J&J** — matching the carriers' updated rate sheets.
- Updated the yellow on-result warning banner ("Pieces over **375** lbs may incur $1.00/lb overweight surcharge per piece") and **both** carriers' Policies panels. The banner's appearance threshold also moved to 375 (it shows when order weight passes 375).
- **No calculation change:** the surcharge stays informational only, exactly as before — the calculator does **not** add it to the quote and does **not** try to decipher single-piece vs. total-order weight (per request, since a piece over 375 lbs can't be represented in the app today — the picker tops out at 325 lbs and the weight box is an order total). Quote totals are unchanged.

---

## 2026-06-13

Service worker cache **v8 → v12** (forces installed home-screen apps to update — close & reopen the app once on Wi-Fi).

### Greenville NC vs SC — fixed (sw v9)
Address suggestions used to show two **identical** "Greenville" lines (state only in tiny grey subtext), so the same place could resolve to NC for one user and SC for another → wrong carrier/rate. Now every suggestion shows its state in the main line ("…Greenville, **NC**" vs "…, **SC**" vs "…, **OH**").
Root cause: the app matches a carrier by **county**, so any city name shared across states (NC/SC/GA/…) is inherently ambiguous; showing the state is the guardrail.

### Quote logging → Google Sheet (sw v10)
Each settled quote is logged to the Google Sheet **"AXIO Freight — Quote Log"**: **timestamp, carrier, quote total** (no PII).
- `calc()` → `logQuote(c, q.total)` → debounced 2.5s + de-duped → `navigator.sendBeacon` → Google **Apps Script** web app (container-bound to the Sheet, "Anyone" access, `doPost` appends a row).
- To-do: delete the 4 test rows; optionally set Apps Script timezone to America/New_York (currently Pacific, ~3h behind ET).

### "2× weight" warning (sw v11; top banner removed v12)
Red inline callout above the weight input: *"Patio / Outdoor / Metal / Resin furniture = 2× actual weight"* (per Townsend rate sheet). A top red banner was added then removed per preference; only the inline note remains.

### Still open
- **Surry / Stokes / Scotland** — the only 3 NC counties missing from the app (and from Townsend's rate sheet). Surfaced via 194 Caprice Trail, State Road NC 28676 (Surry County) → "not serviced." Awaiting Townsend's confirmation of carrier + zone for each, then add to the `CO` object.
- **Cloudflare Web Analytics** — offered (free), deferred.
