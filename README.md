# Tesla Battery Degradation Calculator

A browser-based tool that estimates Tesla battery pack capacity from data you can read directly off your vehicle's own displays — no OBD-II adapter, app subscription, or third-party hardware required.

**[Live Tool →](https://brian-pollard.github.io/tesla-battery-degradation-calculator/)**

---

## What It Does

Enter the numbers you see on your Tesla's energy display and BMS range display after a charge cycle. The calculator produces three capacity estimates and a color-coded battery stack breakdown showing how your pack's energy is distributed across drive, standby, phantom drain, and degradation.

Results include a **Tesla warranty pass/fail** indicator based on the 70% minimum threshold (8 years / 150,000 miles).

---

## How to Use It

1. After a full charge (or any charge ending at a known SOC%), note:
   - **End rated miles** and **end SOC%** — what the car shows when charging completes
   - **kWh used** — from the vehicle's Energy app ("Since last charge")
   - **Start SOC%** — what the car showed when you plugged in
2. Select your vehicle model from the dropdown
3. Click **Calculate**

Optional inputs (Drive SOC and Parked SOC) enable a third "drive-adjusted" estimate and a more detailed battery stack breakdown.

---

## Three Calculation Methods

| Method | Formula | Notes |
|--------|---------|-------|
| **BMS Display** | `(End Miles ÷ End SOC%) × 100 ÷ Original Range × Nominal kWh` | Tesla's internal model — tends to overstate real delivered capacity; shown for reference |
| **Observed** *(primary)* | `kWh Used ÷ SOC Window` | Most conservative; no manual split of drive vs. park losses; best for warranty evaluation |
| **Drive-Adjusted** *(optional)* | `kWh Used ÷ Drive Fraction ÷ SOC Window` | Requires optional Drive/Park SOC inputs; typically 3–5 kWh higher than Observed |

The **Observed** method is the recommended primary metric. It uses only values directly readable from the car and makes the fewest assumptions.

**Reliability by SOC window size:**
- ≥ 70% SOC window → large qualifying cycle, most reliable
- 50–70% SOC window → moderate reliability, usable
- < 50% SOC window → too small, not computed

---

## Supported Vehicles

| Vehicle | Nominal Pack | Original Range |
|---------|-------------|----------------|
| Model 3 Performance 2018–2022 | 75 kWh | 310 mi |
| Model 3 Long Range AWD 2017–2022 | 75 kWh | 353 mi |
| Model 3 Long Range RWD 2019–2020 | 75 kWh | 322 mi |
| Model 3 Standard Range+ 2019–2021 | 54 kWh | 240 mi |
| Model 3 RWD 2023+ | 60 kWh | 272 mi |
| Model Y Long Range 2020+ | 75 kWh | 330 mi |
| Model Y Performance 2020+ | 75 kWh | 303 mi |
| Model S 85 2012–2015 | 85 kWh | 265 mi |
| Model S 100D 2016–2019 | 100 kWh | 335 mi |
| Model S Long Range Plus 2020+ | 100 kWh | 405 mi |
| Model S Plaid 2021+ | 100 kWh | 396 mi |
| Model X 100D 2016–2018 | 100 kWh | 295 mi |
| Model X Long Range 2019+ | 100 kWh | 371 mi |
| Custom / Manual entry | — | — |

---

## Key Caveats

- BMS SOC is reported in whole percentages — ±1 pp rounding introduces ~±0.5–1 kWh uncertainty
- The Observed method assumes Tesla's kWh display captures all energy since last charge (driving + phantom drain); this is best-available understanding, not officially confirmed by Tesla
- Temperature, driving style, and trip length affect **efficiency**, not **capacity** — these methods measure delivered capacity
- A single cycle is less meaningful than an average across many large qualifying cycles

---

## Implementation

Single self-contained HTML file — no build step, no external JS dependencies, no server required. Open `docs/index.html` directly in any browser.
