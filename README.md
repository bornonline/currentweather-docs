# CurrentWeather.info

**CurrentWeather.info** is an ad-free, tracker-free weather dashboard built around **data provenance, validation, and transparency**.

We don't average everything and hope.  
We decide what's *current* **per field**, using explicit rules, validation gates, and clearly labeled sources.

🌐 **Live Site:** https://currentweather.info  
📚 **Methodology:** https://currentweather.info/methodology  
🔧 **Technical Updates:** https://realweather.org/updates

---

## What "Current" Means

Weather data is selected using a **tiered fallback system with type-safety and freshness checks**:

### Data Selection Logic

**1. NWS Observations (Preferred)**  
If a station report is:
- Less than **60 minutes old** (freshness threshold)
- Within **20°F (11°C)** of Open-Meteo model temperature (sanity delta check)
- Successfully parsed and type-valid

→ Use NWS observations for current conditions

**2. Model Data Fallback (Per Field)**  
If NWS data is stale, missing, or fails the delta check:
- Fall back to Open-Meteo current conditions **for that field only**
- Other valid NWS fields remain unchanged

→ Results in "Hybrid" mode when mixed sources are used

**3. Provenance Labels (Always)**  
Every displayed value is explicitly tagged:
- `NWS` — Direct observation from weather station
- `Open-Meteo` — Model-based current conditions
- `Hybrid` — Mixed sources across different fields

---

## Validation Architecture

### What Gets Validated

**Type & Null Safety:**
- All numeric values checked with `typeof === 'number'`, `Number.isFinite()`, `Number.isNaN()`
- Missing or invalid values render as `—` (null-safe display)
- No crashes from malformed API responses

**Freshness Gates:**
- NWS observations > 60 minutes old are marked "stale"
- Data auto-refreshes every 10 minutes when tab is active
- Confidence scoring:
  - **LOW:** Data > 2 hours old or model-only
  - **MEDIUM:** Fresh observations or hybrid mode

**Sanity Checks:**
- NWS temperature must be within **20°F (11°C)** of Open-Meteo model to qualify
- If delta exceeds threshold → fall back to model data
- Coordinates validated: `lat ∈ [-90, 90]`, `lon ∈ [-180, 180]`
- NWS territory checks (CONUS, Alaska, Hawaii, Puerto Rico, Guam)

**NOT Validated (Trust Source):**
- Physical bounds (temperature extremes, humidity > 100%, pressure ranges)
- Weather values accepted as-is from external APIs (Open-Meteo, NWS)
- No runtime bounds checking on magnitude (e.g., wind speed, precipitation)

### Architecture: "Trust the Source"

CurrentWeather relies on:
- **Source integrity** — Open-Meteo and NWS provide physically valid data
- **Type-safety** — Prevent JavaScript crashes, not meteorological impossibilities
- **Freshness indicators** — Age-based confidence, not value validation

If a data provider returns an invalid value, we render it (or null). We don't second-guess the APIs.

---

## Why This Design?

**Observation data is authoritative.**  
If NWS says it's 134°F in Death Valley, we show it—because it might be real.

**Model data is fallback, not smoothing.**  
We don't blend, average, or "fix" values to look consistent. Gaps appear as gaps.

**Transparency over polish.**  
Users see exactly what the source APIs provide, with clear labels on where each value came from.

---

## Data Sources

CurrentWeather.info aggregates data from multiple public APIs:

### Primary Weather Data

| Service | Purpose | Coverage | License |
|---------|---------|----------|---------|
| **[National Weather Service (NWS)](https://www.weather.gov/)** | Real-time observations, forecasts, alerts, NEXRAD radar | United States | Public domain (U.S. government) |
| **[Open-Meteo](https://open-meteo.com/)** | Global forecast models, air quality, UV index | Worldwide | [CC BY 4.0](https://open-meteo.com/en/terms) |
| **[RainViewer](https://www.rainviewer.com/)** | Live precipitation radar imagery | Global | Free (non-commercial) |

### Supporting Services

| Service | Purpose | Data Sent |
|---------|---------|-----------|
| **[BigDataCloud](https://www.bigdatacloud.com/)** | Reverse geocoding (coordinates → place names) | GPS coordinates |
| **[ESRI ArcGIS](https://www.esri.com/)** | NOAA NEXRAD radar tiles | Tile coordinates |
| **[CartoDB](https://carto.com/)** | Base map tiles (streets, terrain) | Tile coordinates |

**Privacy Note:** All API requests originate from your browser. CurrentWeather.info does not proxy, log, or store any location data. See [Privacy Policy](https://currentweather.info/privacy).

---

## Features

- **Real-time observations** from NWS weather stations (METAR/ASOS/AWOS)
- **Global forecast data** via Open-Meteo (GFS, ECMWF, ICON models)
- **Live radar** from RainViewer and NOAA NEXRAD
- **Air quality monitoring** (PM2.5, PM10, NO₂, SO₂, O₃, CO, UV index, pollen)
- **NWS alerts** (warnings, watches, advisories)
- **Astronomical data** (sun/moon rise/set, moon phase, solar elevation)
- **Detailed metrics** (wind chill, heat index, dew point, visibility, pressure trends)
- **Provenance transparency** — Every value labeled with its source
- **Ad-free, tracker-free** — No analytics, no cookies, no surveillance

---

## Projects

### CurrentWeather.info  
**Live Site:** https://currentweather.info  

A fast, modern weather dashboard that combines:
- NWS station observations (preferred)
- Open-Meteo forecast models (fallback)
- Explicit per-field validation and selection logic

Built to show *what's actually current*, not what looks smooth.

### RealWeather.org  
**Documentation:** https://realweather.org  
**Updates Feed:** https://realweather.org/updates  

A technical documentation site that explains:
- Data source selection rules
- Fallback behavior and validation gates
- Implementation receipts for all features
- Methodology behind every displayed value

RealWeather exists to answer: **"Why am I seeing this?"**

---

## Principles

✓ **No ads** — No monetization, no affiliate links  
✓ **No tracking** — No analytics, no cookies, no profiling  
✓ **No engagement manipulation** — No dark patterns, no A/B tests  
✓ **Explicit data sources** — Every value labeled with provenance  
✓ **Per-field selection** — Not global blending or smoothing  
✓ **Observation priority** — Ground truth beats model output  
✓ **Transparency** — Open methodology, documented validation logic  

---

## Technical Architecture

**Frontend:**
- React 18 (SPA with Vite build)
- Client-side rendering (no SSR)
- Direct API calls from browser (no proxy)

**Static Documentation:**
- `/about`, `/methodology`, `/privacy`, `/changelog`
- Served as static HTML with JSON-LD structured data
- SEO-optimized with canonical URLs and Open Graph metadata

**Hosting:**
- CDN delivery (Cloudflare + Caddy)
- Static assets cached aggressively
- API responses cached client-side (10-minute TTL)

**Refresh Intervals:**
- Current conditions: 10 minutes (tab active)
- Forecasts: 30 minutes
- Radar: 5-10 minutes (live-streamed)
- Alerts: 5 minutes

---

## Why the Application Source is Private

CurrentWeather.info is a production system with:
- Live infrastructure and CDN configuration
- Rate-limited third-party API keys
- Abuse prevention and operational integrity logic

The **application source code** is private to protect infrastructure.

The **methodology, data sources, and selection rules** are fully documented:
- This repository (public documentation)
- https://currentweather.info/methodology
- https://realweather.org/updates

**We believe in transparency of process, not exposure of infrastructure.**

---

## Documentation Structure

docs/
├── data-sources.md          # Detailed API specifications
├── validation-logic.md      # Freshness, sanity checks, confidence scoring
├── fallback-behavior.md     # NWS → Open-Meteo failover rules
└── provenance-labels.md     # How source badges are determined



*(Coming soon)*

---

## License

**Documentation License:** MIT (see [LICENSE](LICENSE))

This repository contains **public documentation only**. The CurrentWeather.info application source code is not included and remains private.

---

## Contact

- **General inquiries:** contact@currentweather.info
- **Security concerns:** security@currentweather.info
- **Twitter/X:** [@CurrentWXNow](https://twitter.com/CurrentWXNow)

---

## Changelog

See [CHANGELOG.md](CHANGELOG.md) for methodology updates and documentation changes.

For user-facing feature updates, see:
- https://currentweather.info/changelog
- https://realweather.org/updates

---

**Built with provenance, honesty, and respect.**
