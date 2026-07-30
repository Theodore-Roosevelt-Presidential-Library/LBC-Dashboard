# LBC Dashboard

A public-facing, real-time dashboard for the Theodore Roosevelt Presidential Library's **Living Building Challenge** performance — solar and geothermal production, energy draw, water capture and use, indoor air quality, and the full seven-petal certification picture.

**Live site:** https://lbc.labs.trlibrary.com

> **Status: prototype.** The dashboard is fully built and interactive but currently runs on **demo data generated in the browser**. The next phase is wiring it to the Library's Schneider Electric EcoStruxure system so the numbers are real. See [Next steps](#next-steps).

---

## What it does

A single, self-contained `index.html` designed for a widescreen lobby display (open it and press F11 for fullscreen). It has two levels:

- **Overview** — a layered North Dakota Badlands vector panorama with floating live KPI tiles (Energy net-positive %, Solar + Geothermal, Water captured vs used, indoor CO₂) and a persistent seven-petal navigation rail across the bottom.
- **Petal drill-in** — click any petal in the rail to see what that petal tracks, sample visualizations, and the public-facing story. The selected petal highlights in its own color. Energy, Water, and Health update live every five seconds.

The seven petals follow **Living Building Challenge 4.0**: Place, Water, Energy, Health + Happiness, Materials, Equity, Beauty. Energy and Water are metered live; Health + Happiness is "live-capable" (can stream from the same building sensors); the remaining four are documented in the companion tracker.

### Living Badlands scene

The backdrop is a real-time North Dakota Badlands panorama. A day-night cycle moves the sun and moon along an arc, shifts the sky through dawn / day / golden hour / sunset / dusk / night, and reveals stars after dark — all keyed to Medora's local time and sunrise/sunset. It also reads **live Medora weather** from the free [Open-Meteo](https://open-meteo.com) API (no key required, so it works on a static page) and renders the conditions: clouds, rain, drizzle, snow, sleet/freezing rain, fog, and thunderstorms with lightning, with wind driving the drift. A small chip shows the current temperature and condition. If the weather request ever fails, the scene falls back to the time-of-day cycle under clear skies.

## Repository layout

```
index.html    The dashboard (single self-contained file — HTML, CSS, JS, inline SVG)
CNAME         Custom domain for GitHub Pages (lbc.labs.trlibrary.com)
.github/workflows/deploy.yml   GitHub Actions workflow that publishes to Pages
docs/
  EcoStruxure-API-Access.md    How to get API credentials from Schneider
  LBC-Petal-Tracker.xlsx       Manual tracker for the five documented petals
```

## How hosting works

GitHub Pages is configured to build from **GitHub Actions**. On every push to `main`, `.github/workflows/deploy.yml` publishes the repository root as a static site. The `CNAME` file points the site at `lbc.labs.trlibrary.com`.

To make a change: edit `index.html`, commit, and push to `main` — the site redeploys automatically in a minute or two. Watch progress under the repo's **Actions** tab.

### One-time DNS + domain checklist

For `lbc.labs.trlibrary.com` to serve the site, confirm the following (most are one-time):

1. **DNS** — a `CNAME` record for `lbc.labs.trlibrary.com` pointing to `theodore-roosevelt-presidential-library.github.io` (the org's Pages host). Set this with whoever manages the `trlibrary.com` DNS zone.
2. **Repo → Settings → Pages** — Source set to **GitHub Actions**; Custom domain set to `lbc.labs.trlibrary.com`; **Enforce HTTPS** checked once the certificate is issued (can take a few minutes after DNS resolves).
3. First deployment runs automatically once this is pushed to `main`.

---

## Next steps

### 1. Connect live data (the big one)

The dashboard reads from a single adapter so the wiring is contained. In `index.html`, find the **`DATA_SOURCE`** block near the top of the `<script>`. It currently calls `tickDemo()` to fabricate values. To go live:

- Replace the demo generators with a `fetchLive()` function that calls the EcoStruxure API and returns the same field names the render code already uses (`solarNow`, `geoNow`, `drawNow`, `waterCap`, `waterUse`, `co2`, etc.).
- Each on-screen metric maps one-to-one to a returned field, so no layout changes are needed.

Which EcoStruxure API depends on the Library's setup — see `docs/EcoStruxure-API-Access.md` for the exact questions to ask the facilities team / Schneider integrator and how to request a **read-only** token.

**Security note:** never put an API token in `index.html` — it's public. Route the API call through a small server-side proxy (or a scheduled job that writes a static `data.json` into this repo) so credentials stay private. Deciding between a live proxy and a periodic JSON snapshot is the first architecture choice for phase two.

### 2. Confirm the metered points exist

Verify that solar inverters, the geothermal loop, total building load, water meters, cisterns, and (optionally) indoor air-quality sensors are all already trending in EcoStruxure. Anything not metered can't be shown live and should be tracked in the spreadsheet instead.

### 3. Promote Health + Happiness to live

Indoor CO₂ and PM2.5 can come off the same building sensors. If those points are available, add them to the API request and the Health petal becomes a third live petal.

### 4. Keep the documented petals current

Place, Materials, Equity, and Beauty are maintained by hand in `docs/LBC-Petal-Tracker.xlsx`. Longer term, the dashboard could read a summary of that workbook (exported to JSON) so those tiles update without code changes.

### 5. Design polish (optional)

- Shift the sky from day to dusk based on the actual time of day.
- Add a real Medora / prairie site map to the Place petal.
- Replace the emoji petal icons with custom line-art matching the vector style.

---

## Accuracy note

The Living Building Challenge imperative names and numbers used here follow LBC 4.0. Confirm them against the official Standard (living-future.org) before publishing any certification claim.
