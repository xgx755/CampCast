# CampCast — Chapel Hill Parks & Recreation Summer Camp Demand Tool

A browser-based forecasting dashboard for **Chapel Hill Parks & Recreation** that predicts weekly attendance for summer day camp sessions and converts those forecasts into staffing and space allocation recommendations — with no historical registration data required.

## What it does

CampCast uses a **Demand Index** methodology to estimate expected enrollment for each camp session/week. Because this is a Year 1 tool with no historical actuals, every forecast is transparently driven by configurable assumptions (participation rate, market share, index weights) rather than a trained model.

### Core outputs per session
- Predicted final attendance (0–30)
- Confidence level (Low / Med / High)
- Staff required (1 staff per 15 kids, rounded up)
- Capacity risk flag (Underfilled / On Track / Likely Waitlist)
- "Add a session?" recommendation

### Scenario modeling
Three demand scenarios — **Conservative, Expected, High** — vary the Base Participation Rate (BPR) and Market Share (MS) assumptions so staff can plan for a range of outcomes.

| Scenario     | BPR  | Market Share |
|--------------|------|--------------|
| Conservative | 5%   | 30%          |
| Expected     | 8%   | 40%          |
| High         | 12%  | 55%          |

---

## How the forecast works

Each session receives a **Demand Index (0–100)** built from five sub-scores:

| Driver         | Weight | What it measures |
|----------------|--------|-----------------|
| Age Market     | 40%    | Weighted youth population in site catchment matching the session's age band |
| Calendar       | 25%    | Boost for Week 1 / peak weeks; penalty for holiday weeks and last week of summer |
| Competition    | 25%    | Penalty for competing camps running the same week, type, and age band |
| Price          | 5%     | Relative fee vs. median; penalizes outlier pricing |
| Schedule       | 5%     | Boost for extended care availability and longer hours |

Predicted attendance is then calculated as:

```
PotentialDemand    = CatchmentYouthForAgeBand × BPR × MarketShare
AdjustedDemand     = PotentialDemand × (DemandIndex / 50)   # 50 = neutral
PredictedAttendance = MIN(capacity, ROUND(AdjustedDemand))
```

---

## Dashboard pages

### Page 1 — Summer Overview
High-level KPI cards, a predicted attendance chart by week, a site × week fill-rate heatmap, and distribution charts by status, camp type, and age band.

### Page 2 — Session Planner
Filterable and sortable action table showing every session with predicted attendance, fill %, staff required, status, and "Add Session?" flags. Filters by camp type, age band, site, week, and status.

### Page 3 — Demand Drivers
Per-session breakdown of the five Demand Index components, the computed index score, and a competitor list + competitor-by-week chart for context.

### Page 4 — Registration Pacing
Simulates the March–June registration window. Blends actual registrations-to-date (40%) with the index forecast (60%) to produce an updated projected final attendance. Flags sessions needing attention (underfilled or likely waitlist).

---

## Data model (dummy data included)

The MVP ships with simulated Chapel Hill data for Summer 2026:

- **8 calendar weeks** (June 15 – Aug 7, 2026) with holiday/first/last flags
- **4 sites**: Homestead Park, Hargraves Center, Community Center, Meadowmont
- **4 demographic catchment zones** with youth population by age band
- **6 competitors** with type, age band, week availability, and distance
- **~80 camp sessions** across 5 types (Sports, Arts, STEM, General, Adventure) and 3 age bands (6–8, 9–12, 13–15)

To use real data, replace the JavaScript data arrays at the top of `CampCast_ChapelHill_MVP.html` with live values from your registration system, Census/ACS demographics, and competitor research.

---

## Usage

Open `CampCast_ChapelHill_MVP.html` in any modern browser. No server, no build step, no dependencies to install — it's fully self-contained.

---

## Year 1 caveats

- Forecasts are assumption-driven, not model-trained. Treat them as informed estimates, not predictions.
- Confidence levels reflect Demand Index strength, not statistical precision.
- After the first season runs, actual attendance data can be used to calibrate BPR, MS, and index weights per camp type.

---

*Built for Chapel Hill Parks & Recreation · Summer 2026 MVP*
