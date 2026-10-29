# Microgrid Assessment Case Study

## Overview

This repository contains materials and analysis for **Microgrid Assessment** from the course **04-782: Energy Data Analytics** at Carnegie Mellon University (October 1, 2026). The case study focuses on evaluating the performance of a microgrid system (Nyabiheke Hall) using operational data, field records, and solar resource modeling.

The analysis addresses three core case questions:
1. **Is the system performing as expected?**
2. **Is it meeting current service needs?**
3. **Could additional services use available capacity with limited investment?**

---

## Data

This case study relies on four categories of evidence. Each source serves a distinct analytical purpose, and results are only valid when sources are aligned on **time zone, units, measurement boundary, and period coverage**.

### 1. System Monitoring Data (`data/system_data/`)

Sampled electrical measurements from the microgrid, provided via Zenodo (original System_data).

| Field | Details |
|-------|---------|
| **Source** | Zenodo — Supplementary material for Nixon, Bhargava & Gaura (2020), *Energy Performance Gap in Community-Based Solar Energy Interventions* |
| **Original Project** | EPSRC-funded Humanitarian Engineering and Energy for Displacement (HEED), EP/P029531/1 |
| **Data Period** | **July 2019 – March 2020** |
| **Sites** | Nyabiheke Hall (01 July 2019 – 31 March 2020) and Kigeme Microgrid (02 July 2019 – 31 March 2020) |
| **License** | **The Zenodo record does not display an explicit license in the available metadata. The dataset was deposited as supplementary material for an academic publication.** |
| **Attribution** | Cite Nixon, J.D., Bhargava, K., & Gaura, E. (2020) |
| **Access** | Zenodo (Coventry University repository) |
| **Time zone** | **Africa/Kigali** (all timestamps) |

#### Hall folder structure (from Zenodo deposit)

The zipped `Hall.zip` folder contains `.csv` files for CPE, socket, and system data, plus original check sheets and logbooks.

**CPE data:** `Hall_CPE_[ID]_data.csv`, where ID ranges **1–7**.

**Socket data:** `Hall_S_[ID]_data.csv`, where ID ranges **1–4**.

**System data:** `System_data` (single file).

**Checksheets:** `Hall Checksheets/` — completed check sheets, categorized by month (`[MONTH YEAR]`) between July 2019 and March 2020 where available.

**Logbooks:** `Hall Logbooks/` — completed logbooks, same monthly categorization.

A **synopsis** of check sheets and logbooks for each intervention is attached separately.

#### Microgrid folder structure (from Zenodo deposit)

The zipped `Microgrid.zip` folder contains `.csv` files for CPE, socket, and system data.

**CPE data:** `[LOCATION]_CPE_data.csv`, where LOCATION ∈ {Nursery1A, Nursery1B, Nursery1C, Nursery2A, Nursery2B, Nursery2C, Playground, Streetlight}.

**Socket data:** `[LOCATION]_sockets_data.csv`, where LOCATION ∈ {Nursery1A, Nursery1B, Nursery1C, Nursery2A, Nursery2B, Nursery2C, Playground}.

**System data:** `System_data` (single file).

**Checksheets / Logbooks:** `Microgrid Checksheets/`, `Microgrid Logbooks/` — monthly between July 2019 and March 2020 where available.

#### System data file fields (exact column names)

| Column | Description | Units |
|--------|-------------|-------|
| `Timestamp (Africa/Kigali)` | Time of data collection in POSIXct format, **Africa/Kigali** time zone | datetime |
| `Battery Monitor State of Charge (%)` | State of charge of the battery | % |
| `Battery Monitor Discharged Energy (kWh)` | Total **cumulative** energy discharged from the battery since commissioning | kWh |
| `Battery Monitor Charged Energy (kWh)` | Total **cumulative** energy charged to the battery since commissioning | kWh |
| `Solar Charger Battery Power (W)` | Power delivered to the battery via the solar charge controller | W |
| `Solar Charger PV Power (W)` | Power delivered directly to the Solar Charge Controller from the PV array | W |
| `System Overview AC Consumption (W)` | Power delivered from the inverter to the light and sockets | W |
| `System Overview Battery Power (W)` | Battery power | W |

> **Sign conventions and boundary notes:** `System Overview Battery Power (W)` follows the case convention (positive = charging, negative = discharging) — verify against the raw data before calculating. `System Overview AC Consumption (W)` includes the light and socket **meters' own consumption** (~50–75 Wh/meter/day per Nixon et al.), so it is not pure community load. Cumulative energy counters are **since commissioning** and are subject to resets.

#### CPE data file fields

| Column | Description | Units |
|--------|-------------|-------|
| `Timestamp (Africa/Kigali)` | Time of data collection, POSIXct, Africa/Kigali | datetime |
| `ID` | Unique identifier for each CPE | — |
| `LED1_P (W)` | Instantaneous power reading of LED1 | W |
| `LED2_P (W)` | Instantaneous power reading of LED2 | W |
| `LED3_P (W)` | Instantaneous power reading of LED3 | W |

#### Socket data file fields

| Column | Description | Units |
|--------|-------------|-------|
| `Timestamp (Africa/Kigali)` | Time of data collection, POSIXct, Africa/Kigali | datetime |
| `ID` | Unique identifier for each socket | — |
| `vRELAY1_LVL (W)` | Instantaneous power draw at a socket | W |

#### Known data-quality issues (must be handled before analysis)

- Irregular sampling intervals and **repeated timestamps**
- **Missing readings and gaps** — e.g., no AC output recorded 5–17 September
- **Counter resets** — cumulative energy registers restart
- **SOC jump** on 26 November, 11:41–11:42 (76.1% → 100%) — likely a battery-monitor adjustment, not stored-energy recovery
- **No battery-voltage channel** — low-voltage disconnect must be inferred indirectly
- Missing records must be distinguished from **observed zero output**

**Handling rule:** choose and document the rule *before* calculating (e.g., deduplicate timestamps, flag gaps, do not interpolate across gaps). Report usable coverage explicitly.

### 2. Solar Resource Data (`data/solar_resource/`)

A separate solar resource source is required because the system data contains no irradiance measurement.

| Source | Content | Resolution | Access |
|--------|---------|------------|--------|
| **PVGIS** (SARAH-3) | Historical irradiance + modeled PV output; performs physical PV calculation | Hourly/daily | Free API |
| **NASA POWER** | Hourly and daily solar and weather data | Hourly/daily | Free API |
| **Solcast** | Irradiance source used in the paper; historical 2019–2020 | 5–60 min (by plan) | CSV/API; limited free trial (up to 15 site-months), broader access paid |

**PVGIS configuration used for the Hall:**
- Array: **2.04 kWp** crystalline-silicon
- Geometry (assumed): tilt **10°** from horizontal, azimuth **due north**
- Mounting: freely ventilated
- System loss set to **0%**
- Includes temperature, low-light, spectral, reflection, and terrain-horizon effects
- Output = **array-side DC generation before curtailment**; local shading, soiling, and wiring losses not separately specified

> **Alignment requirement:** match monitoring dates, units, and time zones. PVGIS output and measured PV must be compared on the **same clock hours** (both in Africa/Kigali).

### 3. Paper and Equipment Documents (`docs/`)

Used for system design, ratings, and metric definitions.

| Document | Purpose |
|----------|---------|
| Nixon et al. (2021) paper + system diagrams | System architecture, meter consumption estimates (50–75 Wh/meter/day) |
| Victron inverter datasheet (24/3000 reference; Hall labeled 48/3000) | Efficiency curve, temperature derating (2,400 W @ 25°C; 2,200 W @ 40°C) |
| Victron GEL/AGM battery datasheet | Capacity vs. discharge rate (100% @ C20, 87% @ C10, 80% @ C5) |
| Victron BlueSolar 150/70–85 datasheet | Controller peak efficiency >98% (verify installed variant) |

> **Caution:** datasheet values describe component behavior under specified conditions and measurement boundaries — they are not directly daily system efficiency.

### 4. Field Records (`data/field_records/`)

Qualitative and operational evidence describing service delivery.

| Record type | Content | Analytical use |
|-------------|---------|----------------|
| Activity logs | Community activities, access arrangements | Establish expected service and demand |
| Access records | Who used the system, when | Interpret aggregate consumption |
| Reported interruptions | Outage reports with dates | Cross-check against electrical records |
| Component faults | Check sheets, equipment faults | Distinguish supply vs. equipment vs. access causes |
| Service requests | More sockets, extension cables, sound system, chairs, computers, irons | Assess feasibility of additional services |

**Lighting schedules (expected service baseline):**
- Kigeme nursery outdoor & street lights: **18:00–06:00**
- Kigeme playground: full brightness **17:30–00:00**, half brightness **00:00–05:00**
- Hall outdoor lights: **18:30–06:00**; indoor lights as needed

> Aggregate AC consumption alone **cannot** establish who had access or whether needs were met — field records are required to interpret the electrical data.

### Data Alignment Rules

1. Use **identical accepted intervals** for every energy term in a ratio.
2. Convert all powers to **W** and energies to **kWh** before combining.
3. Do not mix daily averages with instantaneous samples.
4. Flag and exclude repeated timestamps using a documented rule.
5. Report **usable coverage** alongside every result.
6. Distinguish **missing records** from **observed zero output**.

---

## Repository Contents
