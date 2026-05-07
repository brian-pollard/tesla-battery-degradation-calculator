# Tesla Battery Degradation Calculator — Context

This document captures the calculation methodology and design decisions for the calculator so future AI sessions can maintain or extend it without re-deriving the logic.

---

## What the Tool Does

Estimates a Tesla battery's usable capacity from data the driver reads off the vehicle's own displays — no OBD-II adapter or third-party hardware needed. Produces three estimates and shows a color-coded battery stack breakdown.

---

## Three Calculation Methods

### 1. BMS Display (reference)
**Formula:** `(End Rated Miles ÷ End SOC%) × 100 ÷ Original Range × Nominal kWh`

Converts the BMS-reported rated range to an implied capacity. Uses Tesla's internal model — tends to overstate real delivered capacity. Used as a reference, not the primary metric.

### 2. Observed / Primary (conservative, preferred)
**Formula:** `kWh Used ÷ SOC Window`

- **SOC Window** = `(Previous End SOC% − Current Start SOC%) ÷ 100`
- kWh Used = Tesla Energy app "Since last charge" — includes ALL energy (driving + phantom drain)
- Most conservative because it does NOT attempt to strip out parked losses
- Directly measurable with no subjective splits
- Best method for warranty evaluation

**Reliability thresholds:**
- SOC window ≥ 70% → large qualifying cycle, most reliable
- SOC window 50–70% → moderate reliability, usable
- SOC window < 50% → too small, not computed

### 3. Drive-Adjusted (optional, higher estimate)
**Formula:** `kWh Used ÷ Drive Fraction ÷ SOC Window`
- Drive Fraction = Drive SOC Consumed ÷ (Drive SOC + Parked SOC)
- Requires optional Drive SOC and Parked SOC inputs
- Assumes kWh display is drive-only — but Tesla's kWh includes parked losses, so this double-counts park energy
- Consistently 3–5 kWh higher than Observed; use for sensitivity analysis only

---

## BMS Range Calculation
`BMS Full Range = (End Rated Miles ÷ End SOC%) × 100`

Converts rated miles at a given SOC% to an implied 100% range using linear interpolation — the standard interpretation of Tesla's BMS display.

---

## Warranty Logic

Tesla's battery warranty (most models): **70% of original capacity for 8 years / 150,000 miles**
- Warranty threshold = `Nominal Pack × 0.70`
- 75 kWh pack → 52.5 kWh threshold
- Tool shows pass (green) / warn (yellow, within 5% above threshold) / fail (red)

---

## Battery Stack Visual

Color-coded breakdown of the full nominal pack, normalized to a 100% SOC cycle:

| Color | Component | Source |
|-------|-----------|--------|
| Green | Drive | kWh Used (simplified) or Drive SOC × (kWh/DriveSOC) |
| Blue | Park / Standby | Parked SOC × (kWh/DriveSOC) — requires optional inputs |
| Yellow | Phantom | SOC reconciliation gap (Drive+Park vs. Observed window) |
| Gray | Uncharged | `(1 − SOC Window) × full-pack estimate` |
| Red | Degradation | `Nominal Pack − sum of all above` |

Without optional Drive/Park inputs, Drive + Park are combined as "Energy Used."

---

## Vehicle Database

Supported models with nominal pack size and original EPA range:

| ID | Vehicle | Pack (kWh) | Range (mi) |
|----|---------|-----------|-----------|
| m3p_2018 | Model 3 Performance 2018–2022 | 75 | 310 |
| m3lr_awd | Model 3 Long Range AWD 2017–2022 | 75 | 353 |
| m3lr_rwd | Model 3 Long Range RWD 2019–2020 | 75 | 322 |
| m3sr | Model 3 Standard Range+ 2019–2021 | 54 | 240 |
| m3rwd23 | Model 3 RWD 2023+ | 60 | 272 |
| mylr | Model Y Long Range 2020+ | 75 | 330 |
| myp | Model Y Performance 2020+ | 75 | 303 |
| ms85 | Model S 85 2012–2015 | 85 | 265 |
| ms100 | Model S 100D 2016–2019 | 100 | 335 |
| mslrp | Model S Long Range Plus 2020+ | 100 | 405 |
| msplaid | Model S Plaid 2021+ | 100 | 396 |
| mx100 | Model X 100D 2016–2018 | 100 | 295 |
| mxlrp | Model X Long Range 2019+ | 100 | 371 |
| custom | Custom / Manual entry | — | — |

---

## Key Caveats

1. BMS SOC is reported in whole percentages — inherent ±1 pp rounding introduces ~±0.5–1 kWh uncertainty at 70–90% windows.
2. The Observed method assumes Tesla's kWh display captures ALL energy since last charge (driving + phantom drain). This is best-available understanding, not officially confirmed by Tesla.
3. Temperature, driving style, and trip length affect efficiency — not capacity. These methods measure delivered capacity, not efficiency.
4. A single cycle result is less meaningful than an average across many qualifying large cycles.

---

## Design Notes

- Dark theme: navy/slate color scheme, Tesla red accent (`#e31937`)
- Tabs: Calculator | How It Works
- Results animate in on calculate click
- Stack bars animate width on render
- Responsive: grid collapses to single column below 700px
- No external JS dependencies — pure vanilla JS
- Google Analytics: `G-FLMLRDRW7Z` (same property as brianjpollard.com)
- Header: matches BJP Website brand header style (navy navbar, Brian J. Pollard brand)
- Portfolio link: `https://brianjpollard.com/portfolio.html`
