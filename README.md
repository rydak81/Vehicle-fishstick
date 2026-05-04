# Vehicle Decision Engine

> A South Carolina-calibrated buy vs. lease comparison tool with full total-cost-of-ownership modeling, VIN decoding, and dealership negotiation reference points. Built for [BeaconPath Holdings](https://beaconpath.example).

[![Vercel](https://img.shields.io/badge/Deploy-Vercel-black?logo=vercel)](https://vercel.com)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)

---

## What it does

Compare any two vehicles head-to-head — buy vs. lease, gas vs. hybrid vs. EV, different hold periods, different mileage profiles. Designed to walk into a dealership armed with real numbers.

### Core capabilities

- **Two-vehicle comparison** — Independent buy/lease toggle on each side
- **40+ vehicle database** — 2025–2026 MSRP, residuals, depreciation rates, EPA fuel economy
- **VIN decoder** — Live NHTSA vPIC API + offline WMI fallback
- **South Carolina tax calibration**
  - $500 IMF cap (no sales tax on vehicles per S.C. Code § 56-3-627)
  - Annual property tax (assessment ratio × millage rate, Greenville County defaults)
  - SC-specific registration ($40 gas / $60 hybrid / $120 EV)
- **Full TCO modeling**
  - Powertrain-conditioned maintenance (gas baseline · hybrid -15% · EV -40%)
  - Mileage-scaled depreciation (>12k/yr penalizes residual)
  - Class-conditioned insurance estimates
  - Fuel/energy cost (MPG × gas price for ICE; kWh/100mi × $/kWh for EV)
  - Opportunity cost on tied-up capital
- **Two compare modes**
  - **Natural** — Each vehicle uses its own hold period, verdict on annualized cost
  - **Unified Timeline** — Both vehicles compared over the longer period (leases auto-cycle)
- **Save / load scenarios** — Track multiple dealer counter-offers in real time
- **Dealership negotiation references** — Discount-off-MSRP, money-factor-to-APR conversion, residual targets
- **Six chart views** — Cumulative cost, cost-per-mile, equity, depreciation, fuel, monthly outflow

---

## Quick start

### Run locally

```bash
git clone https://github.com/YOUR_USERNAME/vehicle-decision-engine.git
cd vehicle-decision-engine
npm start
```

Then open `http://localhost:8000` in your browser.

> **Important:** Don't open `index.html` by double-clicking it. The browser's `file://` protocol blocks the NHTSA VIN decoder API. Always use a local server (or just deploy to Vercel).

### Deploy to Vercel

The fastest way:

1. Push this repo to GitHub
2. Visit [vercel.com/new](https://vercel.com/new)
3. Import the repo
4. Click **Deploy** (no configuration needed — Vercel auto-detects this as a static site)

That's it. Live URL in ~30 seconds.

Or via CLI:

```bash
npm install -g vercel
vercel
```

---

## Architecture

This is a **single-file static HTML app**. No build step. No framework. No backend.

```
vehicle-decision-engine/
├── index.html          # The entire app — HTML, CSS, JS in one file
├── package.json        # Vercel metadata + local dev script
├── vercel.json         # Static deploy config + security headers
├── README.md
└── .gitignore
```

### Why monofile?

- Zero build complexity
- Works from any static host (Vercel, Netlify, GitHub Pages, S3, even local file)
- Easy to fork, modify, and self-host
- ~140 KB total — loads instantly
- Future Next.js migration is straightforward when you outgrow this

### External dependencies (CDN)

- [Chart.js 4.4.0](https://www.chartjs.org/) — visualization
- Google Fonts (Fraunces, JetBrains Mono, Geist)
- [NHTSA vPIC API](https://vpic.nhtsa.dot.gov/api/) — VIN decoding (no API key required)

---

## South Carolina tax methodology

This tool is calibrated for **South Carolina** specifically. Key SC differences from most states:

| Item | South Carolina | Most other states |
|------|----------------|-------------------|
| Vehicle sales tax | None — replaced by IMF | 5–8% sales tax on full price |
| IMF (Infrastructure Maintenance Fee) | 5% capped at **$500** | N/A |
| Annual property tax on vehicles | Yes (FMV × 6% × millage) | Most states: no |
| Lease tax treatment | IMF paid once at lease start (not on monthly payments) | Tax on each monthly payment |
| EV registration | $120/yr (vs. $40/yr gas) | Varies |

**For other states:** Set IMF cap to your state's sales tax rate (no cap), zero out the property tax slider, and adjust registration fees. Calculator logic remains valid.

---

## Vehicle database

The curated database in `index.html` (search for `VEHICLE_DB`) contains ~40 popular 2025–2026 vehicles. Each entry includes:

- MSRP and typical sale price
- 36-month residual percentage
- Annualized depreciation rate
- Typical money factor
- EPA combined MPG (gas/hybrid) or kWh/100mi (EV)
- Vehicle class (drives insurance/maintenance assumptions)

**Adding a vehicle:** Edit the `VEHICLE_DB` array directly. Each entry takes ~10 seconds.

---

## Roadmap

Things I might add later (or that you might fork and add):

- [ ] **Dealer quote tracker** — Paste actual dealer quotes, model flags each as Below/At/Above target with counter-offer suggestions
- [ ] **PDF export** — Generate a 1-page negotiation sheet to print
- [ ] **Section 179 / bonus depreciation calculator** — For BeaconPath Holdings business-use scenarios on >6,000 lb GVWR vehicles
- [ ] **Lease buyout vs trade-in** — Decide whether to buy out current lease, trade, or walk
- [ ] **Persistent saved scenarios** — Currently saved scenarios live in browser state only; could persist via Supabase
- [ ] **Mobile-optimized layout** — Currently usable on phone but could be tighter
- [ ] **Multi-state tax engine** — Generalize beyond SC

---

## Disclaimers

- **Not financial advice.** This is an analytical tool. Verify all numbers against actual dealer quotes before signing.
- **SC tax rules can change.** Verify current IMF cap and Greenville County millage with [SCDMV](https://scdmvonline.com) and [Greenville County Auditor](https://www.greenvillecounty.org/auditor/) annually.
- **Vehicle pricing data is curated.** Real-time pricing from Edmunds/KBB requires paid licenses and is not used here.
- **Depreciation rates are class-conditioned estimates.** Actual resale depends on condition, market timing, and demand.

---

## License

MIT — fork it, modify it, ship your own version. Attribution appreciated but not required.
