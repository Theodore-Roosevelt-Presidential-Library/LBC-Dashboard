# EcoStruxure → LBC Dashboard: How to Unlock the Live Data

A one-page guide for identifying which EcoStruxure products the Library runs and getting API access to feed the public Living Building Challenge dashboard.

## The short version

Yes, EcoStruxure can serve real-time solar, geothermal, energy-draw, and water data through documented APIs. Which API depends on which products are installed. The goal is a single JSON feed the lobby dashboard can poll every few seconds.

## Step 1 — Identify what you have

Ask your facilities lead, controls contractor, or Schneider integrator these questions:

1. Which EcoStruxure products are installed? (Most likely: **Building Operation** for the building/HVAC/geothermal/water side, and **Power Monitoring Expert** for electrical + solar metering.)
2. Is any site data connected to Schneider's cloud (**EcoStruxure Energy Hub / Facility Expert**), or is everything on-premise?
3. Who administers the server(s), and can they create an API user/token for us?
4. Are the solar inverters, geothermal loop, and water meters all already trending in the system? (If a point isn't metered, no API can report it.)

## Step 2 — Match the product to the API

| Data you need | Lives in | API to request |
|---|---|---|
| Solar production, building energy draw | Power Monitoring Expert (PME) | **PME Data API** |
| Geothermal loop, HVAC, water meters, cisterns | Building Operation (EBO) | **EcoStruxure Web Services (EWS)** or the **SmartConnector RESTful EWS Gateway** (JSON — preferred for web) |
| Everything, if cloud-connected | Energy Hub / Facility Expert | **Energy Advanced Data API** (GraphQL, token auth) |

The cloud **Energy Advanced Data API** is the cleanest path for a public dashboard if it's available — one endpoint, modern auth, covers energy + water + carbon. If you're fully on-premise, the **SmartConnector REST gateway** on top of EBO gives the same kind of JSON feed.

## Step 3 — What to request specifically

- A **read-only API user / access token** (never wire the dashboard to an admin account).
- The **base URL / endpoint** and API version.
- A **point list**: the exact tag/register names for solar kW, geothermal kW, total load kW, daily kWh totals, water captured/reused, potable used, and cistern level.
- Confirmation of **refresh rate** available (real-time streaming vs. e.g. 1–5 min polling — either works for the dashboard).
- A **sample response** (JSON) so the dashboard's data adapter can be mapped field-by-field.

## Step 4 — Wire it up

The dashboard prototype already has a `DATA_SOURCE` block with a `fetchLive()` stub. Once the token, URL, and point names are in hand, each metric maps 1:1 to a field — swap `fetchDemo` for `fetchLive`, drop in the endpoint, and the lobby display goes live. The five documented petals (Place, Health+Happiness, Materials, Equity, Beauty) are maintained in the companion Excel tracker, not the API.

## Security note

Put the dashboard on a display that reads from a **read-only** feed. If the display faces the public, route the API call through a small server-side proxy so the token is never exposed in the browser — worth a five-minute conversation with whoever hosts the Library website.

---
*Reference API products: EcoStruxure Power Monitoring Expert Data API, EcoStruxure Web Services / SmartConnector REST Gateway, EcoStruxure Energy Advanced Data API (all on exchange.se.com / se.com). Confirm exact product names and versions with your Schneider integrator.*
