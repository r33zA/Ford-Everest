# Ford Everest MY25.25 PID Testing Log

https://github.com/r33zA/Ford-Everest

Version: 2026-07-23 v0.7.24 standard packet mirrors, DPF pressure and testing cleanup  
Aligned default file: `default_everest_my25_25_v0_7_24_standard_mirror_promotions.json` / `signalsets/v3/default.json` target  
Vehicle: Ford Everest Trend MY25.25, Australian market, 2.0 L Bi-Turbo Diesel, 10-speed automatic, full-time 4WD

## Update focus

- Updated from v0.6.3 transmission temperature div16 primary pass.
- Promoted `EVEREST_DPF_FULLNESS_0610` as the preferred DPF / exhaust-filter fullness value.
- Promoted `EVEREST_DISTANCE_SINCE_DPF_REGEN_0614` as distance since last completed DPF regeneration.
- Added `EVEREST_DPF_FULLNESS_0610` as a `dpfSootLoad` connectable candidate.
- Kept a small testing-only emissions section for remaining useful survivor candidates:
  - `22F48B`
  - `22F478`
  - `22F463`
  - `220440`
- Did **not** bring across the dead v0.3/v0.3.1 exploratory clutter.
- Revalidated JSON: no duplicate signal IDs.

## Important note — DPF promotion

| Signal | PID | Formula | Decision | Reason |
| --- | --- | --- | --- | --- |
| `EVEREST_DPF_FULLNESS_0610` | `220610` | `raw / 100` | Promoted | Matched the vehicle exhaust-filter display through a full regen cycle: high/full, cleaning, and post-regen near zero. |
| `EVEREST_DISTANCE_SINCE_DPF_REGEN_0614` | `220614` | `raw / 10` km | Promoted | Counted up with driving distance, reset to 0 km when the regen completed, then immediately started counting up again. |

## Important note — retained testing-only emissions candidates

| Signal | PID | Path | Reason retained |
| --- | --- | --- | --- |
| `EVEREST_TEST_DPF_STATUS_F48B_RAW8_A` | `22F48B` | `Emissions.DPF.Testing` | Alive; may be a DPF/regen status byte. |
| `EVEREST_TEST_DPF_STATUS_F48B_RAW16_AB` | `22F48B` | `Emissions.DPF.Testing` | Raw 16-bit companion for status analysis. |
| `EVEREST_TEST_DPF_REGEN_STATUS_0440_RAW8_A` | `220440` | `Emissions.DPF.Testing` | Alive with valid zero; possible regen-status bitfield. |
| `EVEREST_TEST_EXHAUST_MULTI_EGT_F478_RAW16_AB` | `22F478` | `Emissions.Exhaust.Testing` | Alive packet-like exhaust/EGT candidate. |
| `EVEREST_TEST_EXHAUST_MULTI_EGT_F478_CANDIDATE_C` | `22F478` | `Emissions.Exhaust.Testing` | Candidate first-word temperature scaling for comparison only. |
| `EVEREST_TEST_EGRT_F463_CELSIUS` | `22F463` | `Emissions.Exhaust.Testing` | Plausible EGRT / EGR manifold temperature candidate. |
| `EVEREST_TEST_EGRT_F463_RAW16_AB` | `22F463` | `Emissions.Exhaust.Testing` | Raw companion for F463. |

## Sanity-check snapshot

| Check | Result |
| --- | ---: |
| Commands in current default.json | 69 |
| Signals in current default.json | 77 |
| Signals with suggestedMetric | 21 |
| Duplicate signal IDs | 0 |
| JSON validation | Passed |

## SuggestedMetric / connectable decisions

| Suggested metric | Signal ID | Name | Cmd |
| --- | --- | --- | --- |
| `dpfSootLoad` | `EVEREST_DPF_FULLNESS_0610` | DPF / exhaust filter fullness | `220610` |
| `transmissionFluidTemperature` | `EVEREST_TRANS_TEMP_1E1C_DIV16` | Transmission fluid temperature Everest div16 | `221E1C` |
| `transmissionGear` | `EVEREST_GEAR_ENGAGED_7E1_1E1F` | Transmission gear engaged | `221E1F` |
| `engineCoolantTemperature` | `EVEREST_COOLANT_TEMP_F405` | Coolant temperature | `22F405` |
| `engineOilTemperature` | `EVEREST_ENGINE_OIL_TEMP_F45C` | Engine oil temperature | `22F45C` |
| `engineLoad` | `EVEREST_ENGINE_LOAD_F404` | Engine load | `22F404` |
| `engineSpeed` | `EVEREST_ENGINE_SPEED_F40C` | Engine speed | `22F40C` |
| `fuelRange` | `FORD_DTE_DISP` | Distance to empty, displayed | `224195` |
| `fuelTankLevel` | `EVEREST_FUEL_REMAINING_F42F` | Fuel remaining | `22F42F` |
| `fuelTankLevel` | `GENERIC_FUEL_LEVEL_012F` | Generic fuel level | `012F` |
| `massAirFlow` | `GENERIC_MAF_0110` | Air flow rate from mass air flow sensor | `0110` |
| `odometer` | `EVEREST_ODOMETER_01A6` | Odometer | `01A6` |
| `speed` | `EVEREST_SPEED_FORD_EXTENDED_F40D` | Vehicle speed Ford extended raw | `22F40D` |
| `speed` | `EVEREST_SPEED_FORD_EXTENDED_F40D_CORRECTED` | Vehicle speed Ford extended corrected | `22F40D` |
| `starterBatteryVoltage` | `EVEREST_AUX_12V_BATTERY_VOLTAGE_402A` | Aux 12V battery voltage | `22402A` |
| `throttlePosition` | `EVEREST_ACCEL_PEDAL_032B` | Accelerator pedal position | `22032B` |

## Confirmed / working and currently understood

| Group | PID / cmd | ECU | Signal | Status | Decision / notes |
| --- | --- | --- | --- | --- | --- |
| DPF | `220610` | `7E0 7E8` | DPF / exhaust filter fullness | Works | Active; formula raw/100 percent; matched dash regen/fullness behaviour. |
| DPF | `220614` | `7E0 7E8` | Distance since DPF regen | Works | Active; formula raw/10 km; reset at regen completion. |
| Transmission | `221E1C div16` | `7E1 7E9` | Transmission fluid temperature Everest div16 | Works / chosen | Preferred current Everest-specific scaling; active transmissionFluidTemperature connectable. |
| Transmission | `221E1C 5/72` | `7E1 7E9` | Transmission fluid temperature 5/72 compare | Comparison / legacy | Retained for validation only; no suggestedMetric. |
| Transmission | `221E1F` | `7E1 7E9` | Transmission gear engaged | Works | Active as transmissionGear. |
| Emissions | `22052E` | `7E0 7E8` | EGR open percentage | Works | Active. |
| Emissions | `220569` | `7E0 7E8` | EGT pre-turbo | Works / plausible | Active. |
| Fuel / range | `22F42F` | `7E0 7E8` | Fuel remaining | Works | Active as fuelTankLevel. |
| Fuel / range | `012F` | `7E0 7E8` | Generic fuel level | Works | Backup/comparison fuelTankLevel. |
| Fuel / range | `224195` | `7E0 7E8` | Distance to empty, displayed | Works | Matched dash; active as fuelRange. |
| Movement | `01A6` | `7E0 7E8` | Odometer | Works | Active as odometer. |
| Movement | `22F40D` | `7E0 7E8` | Vehicle speed Ford extended raw/corrected | Works | Corrected signal retained as practical dash-matching speed. |
| Control | `22032B` | `7E0 7E8` | Accelerator pedal position | Works | Practical throttlePosition connectable. |
| Engine | `22F404` | `7E0 7E8` | Engine load | Works | Active as engineLoad. |
| Engine | `22F405` | `7E0 7E8` | Coolant temperature | Works | Active as engineCoolantTemperature. |
| Engine | `22F40C` | `7E0 7E8` | Engine speed | Works | Active as engineSpeed. |
| Engine | `22F45C` | `7E0 7E8` | Engine oil temperature | Works | Active as engineOilTemperature; continue sanity checking long term. |
| AdBlue | `220487` | `7E0 7E8` | AdBlue tank level | Works | Best AdBlue quantity signal. |
| Battery | `22402A` | `726 72E` | Aux 12V battery voltage | Works | Active as starterBatteryVoltage. |

## Shelved / not carried forward from v0.3.1

| Candidate | Decision |
| --- | --- |
| `220579` | Shelved for now; stayed zero and `220610` is a much stronger DPF fullness signal. |
| `22057B` | Shelved for now; stayed zero and `220610` is a much stronger DPF fullness signal. |
| `220373` | Shelved for now; stayed zero and no useful pressure behaviour confirmed yet. |
| `22F460` | Shelved for now; alive but current decode produced nonsense/high value. |
| Raw `0610` and raw `0614` validation signals | Not exposed in stable default; retained in validation notes only. |

## Testing rules going forward

### Promote only when all are true

- Live response received.
- Value is plausible for the vehicle state.
- Behaviour changes correctly during warm-up, movement, throttle/load, regen, gear changes, or another relevant real-world state.
- It is more useful than an existing signal.

### Reject or shelve quickly when any are true

- Negative response / out of range.
- Blank or stale value.
- Impossible scale.
- Looks like a config address instead of a live PID.
- Duplicates a better existing signal.

---

# v0.7.0 — Root TESTING pack consolidation

Aligned default file: `default_everest_my25_25_v0_7_0_root_testing_pack.json` / `signalsets/v3/default.json` target

## Update focus

- Built from the latest working `default.json`.
- Preserved all promoted production signals from v0.6.4.
- Established a single root `TESTING.*` hierarchy for all experimental PIDs.
- Moved existing experimental PIDs out of production-adjacent paths.
- Added new candidate groups for regen, EGT, boost/turbo, transmission/TCC, 4x4, and battery/power testing.
- Removed failed lambda/equivalence-ratio candidates `0124` and `0144`.

## Root TESTING rule — from v0.7.0 onward

All experimental items live only under root `TESTING.*`.

| Status | Location |
| --- | --- |
| Experimental / unknown | `TESTING.*` |
| Validated and trusted | Proper production category |
| Failed / dead / misleading | Removed, or noted as shelved in this log |

This prevents test candidates from being scattered under `Emissions.Testing`, `Fuel.Testing`, `Engine.Testing`, etc.

## Root TESTING structure

```text
TESTING
├── Regen
├── EGT
├── Boost
├── Transmission
├── 4x4
├── Battery
├── Fuel
└── Misc
```

## v0.7.0 sanity-check snapshot

| Check | Result |
| --- | ---: |
| Commands in current default.json | 104 |
| Signals in current default.json | 149 |
| Signals with suggestedMetric | 19 |
| Root TESTING signals | 83 |
| Non-root `*.Testing` paths remaining | 0 |
| Duplicate signal IDs | 0 |
| JSON validation | Passed |

## Production signals preserved

These remain production / trusted and were not demoted:

| Group | Signal | PID | Notes |
| --- | --- | --- | --- |
| DPF | `EVEREST_DPF_FULLNESS_0610` | `220610` | Confirmed DPF / exhaust-filter fullness, formula `raw / 100`, active as `dpfSootLoad`. |
| DPF | `EVEREST_DISTANCE_SINCE_DPF_REGEN_0614` | `220614` | Confirmed distance since completed DPF regen, formula `raw / 10 km`. |
| Transmission | `EVEREST_TRANS_TEMP_1E1C_DIV16` | `221E1C` | Preferred Everest transmission-fluid-temperature scaling. |
| Transmission | `EVEREST_TRANS_TEMP_1E1C_5_72` | `221E1C` | Retained as comparison / legacy only; no connectable. |
| Transmission | `EVEREST_GEAR_ENGAGED_7E1_1E1F` | `221E1F` | Working gear engaged value. |
| DPF / Exhaust | `EVEREST_EGT_PRE_TURBO_0569` | `220569` | Working/plausible existing EGT reference. |
| EGR | `EVEREST_EGR_OPEN_052E` | `22052E` | Working EGR open percentage. |
| Battery | `EVEREST_AUX_12V_BATTERY_VOLTAGE_402A` | `22402A` | Working aux 12V battery voltage. |
| Battery | `EVEREST_BATTERY_SOC_4028_726` | `224028` | Working battery SOC value. |
| Battery | `EVEREST_BATTERY_CURRENT_402B_726` | `22402B` | Working battery current; sign direction still needs confirmation. |
| Battery | `EVEREST_ALTERNATOR_CURRENT_0551` | `220551` | Working/plausible alternator current output. |

## v0.7.0 TESTING groups

### TESTING.Regen

Purpose: find an active regen state / phase / request / inhibit value.

| PID | Header | Purpose |
| --- | --- | --- |
| `220440` | `7E0 7E8` | Existing survivor; possible regen bitfield, previously valid zero. |
| `22F48B` | `7E0 7E8` | Existing survivor; possible DPF/regen status byte, previously `127` / `32512`. |
| `22F411` | `7E0 7E8` | Regen commanded candidate. |
| `22F412` | `7E0 7E8` | Regen inhibit candidate. |
| `22F413` | `7E0 7E8` | Regen request candidate. |
| `22F450` | `7E0 7E8` | Common Ford active regen candidate. |
| `22F451` | `7E0 7E8` | Regen request candidate. |
| `22F453` | `7E0 7E8` | DPF status bitfield candidate. |
| `22F87C` | `7E0 7E8` | Alternate DPF active candidate. |

### TESTING.EGT

Purpose: identify additional exhaust temperature sensors and improve regen thermal visibility.

| PID | Header | Purpose |
| --- | --- | --- |
| `22F478` | `7E0 7E8` | Existing survivor; packet-like multi-EGT candidate. |
| `22F463` | `7E0 7E8` | Existing survivor; possible EGRT / EGR manifold temp, low confidence. |
| `22F46A` | `7E0 7E8` | EGT11 candidate. |
| `22F46B` | `7E0 7E8` | EGT12 candidate. |
| `22F46C` | `7E0 7E8` | EGT13 / DPF inlet candidate. |
| `22F46D` | `7E0 7E8` | EGT14 / DPF outlet candidate. |

### TESTING.Boost

Purpose: identify boost, VGT, turbo-bypass, and bi-turbo behaviour.

| PID | Header | Purpose |
| --- | --- | --- |
| `220210` | `7E0 7E8` | Extended MAF candidate; compare against generic `0110`. |
| `220233` | `7E0 7E8` | BARO candidate. |
| `220247` | `7E0 7E8` | MAP / gauge boost candidate. |
| `22F470` | `7E0 7E8` | VGT commanded candidate. |
| `22F471` | `7E0 7E8` | VGT actual candidate. |
| `22F476` | `7E0 7E8` | Charge air cooler / post-intercooler temp candidate. |
| `22F479` | `7E0 7E8` | Turbo inlet pressure candidate; note possible conflict with internet alternator claims. |
| `22F47A` | `7E0 7E8` | LP turbo speed / alternate turbo value candidate. |
| `22F48D` | `7E0 7E8` | Turbo bypass candidate. |

### TESTING.Transmission

Purpose: expand torque converter lockup/slip understanding.

| PID | Header | Purpose |
| --- | --- | --- |
| `221E35` | `7E1 7E9` | TCC desired slip candidate; compare against confirmed `1E14` actual slip. |
| `221E3C` | `7E1 7E9` | TCC commanded pressure candidate. |

### TESTING.4x4

Purpose: look for transfer case, TOD clutch, prop shaft, and rear-locker state.

Tested on both likely paths:

| PID | Header(s) | Purpose |
| --- | --- | --- |
| `22C45D` | `7E2 7EA`, `7E0 7E8` | 4WD mode candidate. |
| `22C460` | `7E2 7EA`, `7E0 7E8` | Transfer clutch duty candidate. |
| `22C461` | `7E2 7EA`, `7E0 7E8` | Front prop speed candidate. |
| `22C462` | `7E2 7EA`, `7E0 7E8` | Rear prop speed candidate. |
| `22C463` | `7E2 7EA`, `7E0 7E8` | Rear locker / eLSD state candidate. |

### TESTING.Battery

Purpose: extend already-working BMS / smart-charging visibility without cluttering production.

| PID | Header | Purpose |
| --- | --- | --- |
| `224006` | `726 72E` | Battery SOH candidate. |
| `224007` | `726 72E` | Battery internal resistance candidate. |
| `22400A` | `726 72E` | Battery temperature candidate. |
| `22032E` | `7E0 7E8` | Generator desired voltage candidate. |
| `221632` | `7E0 7E8` | Generator command duty candidate. |

## Removed in v0.7.0

| Signal | PID | Reason |
| --- | --- | --- |
| `EVEREST_TEST_O2_LAMBDA_EQUIVALENCE_RATIO_0124` | `0124` | Failed / out of range on this Everest diesel. |
| `EVEREST_TEST_COMMANDED_EQUIVALENCE_RATIO_0144` | `0144` | Failed / out of range on this Everest diesel. |

## Current highest-value validation targets

| Priority | Target | Why |
| --- | --- | --- |
| 1 | Active regen state | Biggest remaining DPF win; likely in `TESTING.Regen`. |
| 2 | EGT chain | Confirms regen thermals and exhaust sensor locations. |
| 3 | Boost / VGT | Useful for towing, diagnostics, turbo health, and intercooler checks. |
| 4 | TCC desired slip / pressure | Helps understand converter lockup and transmission heat. |
| 5 | 4x4 mode / transfer clutch | Useful for off-road and drivetrain telemetry. |
| 6 | Battery SOH / temp / generator command | Useful for touring, fridge loads, and smart-charging behaviour. |

## Active regen capture checklist

During the next known active regen, capture:

- `EVEREST_DPF_FULLNESS_0610`
- `EVEREST_DISTANCE_SINCE_DPF_REGEN_0614`
- `EVEREST_EGT_PRE_TURBO_0569`
- all visible `TESTING.Regen`
- all visible `TESTING.EGT`
- vehicle speed
- RPM
- engine load
- accelerator pedal

Questions to answer:

- Does any `TESTING.Regen` value change when Cleaning starts?
- Does `F48B` change from `127` / `32512`?
- Does `0440` flip from zero?
- Do any EGT candidates rise sharply above normal cruise values?
- Does `0610` drop smoothly?
- Does `0614` reset only at regen completion?

## 4x4 testing checklist

Only test in safe, appropriate conditions.

- Check which `TESTING.4x4` values respond while stationary.
- Test 2H / normal mode baseline.
- Test 4H / off-road mode if available and safe.
- Test 4L only using the correct vehicle procedure.
- Test rear locker only where suitable.
- Watch for values that change with mode selection or engagement.

Avoid drivetrain bind on dry high-grip surfaces. The car is not a science fair sacrifice.

## Boost / turbo testing checklist

Capture during:

- idle
- gentle acceleration
- steady cruise
- moderate acceleration
- hill climb if available

Useful comparisons:

- `TESTING.Boost` candidates
- generic MAP `010B`
- manifold pressure `F40B`
- MAF `0110`
- engine load `F404`
- accelerator pedal `032B`
- RPM `F40C`

## Promotion rules

A candidate can be promoted only when all are true:

- Live response received.
- Value is plausible.
- Behaviour changes correctly with real vehicle state.
- It is repeatable across more than one test.
- It is not a duplicate of a better existing signal.
- It has a clear useful label and formula.

## Rejection / shelving rules

Reject or shelve when:

- Negative response / out of range.
- Static and not meaningful.
- Impossible scale after reasonable alternate formulas.
- No correlation with vehicle state.
- Conflicts with a confirmed production PID.
- Adds UI clutter without useful insight.

## Commit message

```text
Consolidate Everest PID testing log for v0.7.0
```

## Extended description

```text
Consolidated the Ford Everest MY25.25 PID testing log into a single living reference file.

The updated log preserves the v0.6.4 DPF promotion history, including confirmed DPF fullness PID 220610 and distance since DPF regen PID 220614, while adding the v0.7.0 root TESTING structure and candidate groups.

All experimental PIDs are now documented under the root TESTING rule, with groups for regen, EGT, boost, transmission, 4x4, battery, fuel, and misc candidates. This keeps production categories reserved for validated signals and makes future promotion or rejection decisions easier to track.

The log now includes current validation priorities, active regen capture notes, 4x4 testing guidance, boost/turbo testing guidance, and explicit promotion/rejection rules.

This file should remain the single source of truth for Ford Everest MY25.25 PID testing notes going forward.
```

---

# v0.7.1 — Ranger-derived testing expansion and screenshot cleanup

Aligned default file: `default_everest_my25_25_v0_7_1_ranger_testing_expansion.json` / `signalsets/v3/default.json` target

## Update focus

- Built from the latest uploaded working Everest `default.json`.
- Used the uploaded latest Ranger `default.json` and 2024/2025 Ranger `command_support` files as test/validation sources only.
- Preserved production/stable Everest signals.
- Added Ranger-derived candidates only under root `TESTING.*`.
- Added alternate VGT 16-bit scaling candidates for `22F470` and `22F471` because raw AB moved while the current A-byte percent candidate stayed around 3%.
- Shelved obvious repeated out-of-range screenshot clutter from active JSON.
- Kept useful/weird/alive values in TESTING rather than over-cleaning.

## Screenshot validation batch — 2026-05-21

### Strong testing / keep active

| Area | PID | Evidence / decision |
| --- | --- | --- |
| EGT | `22F46A` | Responded and warmed from about 44°C to 62.3°C. Looks like a real EGT/temperature sensor. |
| EGT | `22F46D` | Responded around 140–144°C, raw around 1801–1844. Formula `raw * 0.1 - 40` fits the observed values well. |
| EGT | `22F478` | Responded around 140°C and closely tracked `F46D`; keep as possible multi-EGT/duplicate comparison. |
| Transmission | `221E35` | Desired slip responded around 2.75–4.25 rpm; very plausible for near-locked TCC behaviour. |
| Transmission | `221E3C` | Commanded pressure responded and moved with raw around 118–289; strong live TCC command/pressure candidate. |
| Boost | `22F470` | VGT commanded raw moved around 1820 while A-byte display stayed ~3%; PID looks alive, current scaling suspect. |
| Boost | `22F471` | VGT actual raw moved around 1900 while A-byte display stayed ~3%; PID looks alive, current scaling suspect. |

### Shelved from active JSON

| PID | Header | Signal IDs | Reason |
| --- | --- | --- | --- |
| `22F46B` | `7E0 7E8` | `EVEREST_TEST_EGT12_7E0_F46B_RAW16_AB, EVEREST_TEST_EGT12_7E0_F46B_CANDIDATE` | Repeated screenshot result: Negative response / out of range. Shelved to save active screenshot space. |
| `22F46C` | `7E0 7E8` | `EVEREST_TEST_EGT13_DPF_INLET_7E0_F46C_RAW16_AB, EVEREST_TEST_EGT13_DPF_INLET_7E0_F46C_CANDIDATE` | Repeated screenshot result: Negative response / out of range. Shelved to save active screenshot space. |
| `22F479` | `7E0 7E8` | `EVEREST_TEST_TURBO_INLET_PRESSURE_7E0_F479_RAW16_AB, EVEREST_TEST_TURBO_INLET_PRESSURE_7E0_F479_CANDIDATE` | Repeated screenshot result: Negative response / out of range. Shelved to save active screenshot space. |
| `22F48D` | `7E0 7E8` | `EVEREST_TEST_TURBO_BYPASS_7E0_F48D_RAW16_AB, EVEREST_TEST_TURBO_BYPASS_7E0_F48D_CANDIDATE` | Repeated screenshot result: Negative response / out of range. Shelved to save active screenshot space. |

## Ranger-derived additions

### TESTING.Tires

Added tyre-pressure candidates from Ranger `726 72E` commands:

| Position / purpose | PID | Formula |
| --- | --- | --- |
| Front left tyre pressure | `222813` | `raw / 20` PSI |
| Front right tyre pressure | `222814` | `raw / 20` PSI |
| Rear right tyre pressure | `222815` | `raw / 20` PSI |
| Rear left tyre pressure | `222816` | `raw / 20` PSI |
| Unknown TPMS-adjacent A | `222817` | raw AB + `raw / 20` PSI candidate |
| Unknown TPMS-adjacent B | `222818` | raw AB + `raw / 20` PSI candidate |

### TESTING.Transmission

Added Ranger 7E0 mirror candidates:

| PID | Candidate | Purpose |
| --- | --- | --- |
| `221E12` | Current gear map 1–10 | Compare with existing Everest 7E1 gear signals. |
| `221E1C` | Transmission temperature `raw / 16` | Compare with confirmed Everest 7E1 `1E1C div16`. |
| `221E23` | Shifter state map | Validate P/R/N/D/M against actual selected shifter position. |

### TESTING.Boost

Added:

| PID | Candidate | Purpose |
| --- | --- | --- |
| `010133` | BARO | Compare against existing `220233` BARO candidate. |
| `010170` | Ranger boost pressure packet raw AB | Raw-only scout; useful boost actual/command values are offset after support bytes in Ranger examples. |
| `22F470` | VGT commanded alternate scalings | Added `/100`, `/64`, `/32`, `/20`, `/10`. |
| `22F471` | VGT actual alternate scalings | Added `/100`, `/64`, `/32`, `/20`, `/10`. |

### TESTING.Battery

| PID | Candidate | Purpose |
| --- | --- | --- |
| `010142` | Control module voltage `raw / 1000` | Compare against aux battery voltage `22402A` and smart-alternator behaviour. |

### TESTING.Fuel

| PID | Candidate | Purpose |
| --- | --- | --- |
| `01015E` | Fuel rate `raw / 20` | Ranger-supported fuel-rate candidate. |
| `01019D` | Alternate fuel rate raw AB + `raw / 50` | Ranger-supported alternate fuel-rate packet first value. |
| `01016D` | Fuel rail packet raw AB | Raw-only scout; useful fields are offset after support bytes in Ranger examples. |

### TESTING.Regen

| PID | Candidate | Purpose |
| --- | --- | --- |
| `01019E` | Exhaust flow raw AB + `raw / 5` | Useful for load/regen correlation if Everest responds. |

### TESTING.EGT / TESTING.Misc

| PID | Candidate | Purpose |
| --- | --- | --- |
| `01016B` | EGR temperature packet raw AB | Raw-only scout for Ranger EGR temperature packet. |
| `010169` | EGR command/actual packet raw AB | Raw-only scout for Ranger EGR command/actual/error packet. |
| `010161` | Driver demand torque `A - 125` | Compare against throttle, load, boost and TCC behaviour. |
| `010162` | Actual engine torque `A - 125` | Compare against throttle, load, boost and TCC behaviour. |
| `010163` | Engine reference torque | May be static or semi-static; validate plausibility. |

## v0.7.1 validation snapshot

| Check | Result |
| --- | ---: |
| Commands in updated default.json | 121 |
| Signals in updated default.json | 176 |
| Root TESTING signals | 110 |
| Signals with suggestedMetric | 19 |
| Duplicate signal IDs | 0 |
| Malformed commands | 0 |
| Non-root test signal paths | 0 |
| JSON validation | Passed |

## Removed in v0.7.1

| PID | Reason |
| --- | --- |
| `22F46B` | Repeated screenshot result: Negative response / out of range. |
| `22F46C` | Repeated screenshot result: Negative response / out of range. |
| `22F479` | Repeated screenshot result: Negative response / out of range. |
| `22F48D` | Repeated screenshot result: Negative response / out of range. |

## Next validation targets

1. TPMS page: check whether `2813–2816` show plausible PSI values matching known tyre pressure.
2. VGT page: compare `/100`, `/64`, `/32`, `/20`, `/10` against idle, cruise and throttle/load changes.
3. Transmission page: compare Ranger `7E0` mirrors against existing Everest `7E1` gear/temp signals.
4. Battery page: compare `0142` module voltage against `402A` aux battery voltage and alternator current.
5. Regen/load page: watch `019E` exhaust flow against EGTs, engine load, DPF fullness and regen events.


---

# v0.7.2 — Live testing refinement and screenshot-space cleanup

Aligned default file: `default_everest_my25_25_v0_7_2_testing_refinement.json` / `signalsets/v3/default.json` target

## Update focus

- Built from the latest working v0.7.1 `default.json`.
- Preserved all production / stable signals unchanged.
- Analysed latest live screenshot batch covering Regen, Boost, Fuel, Misc, Battery and Tires pages.
- Removed obvious repeated negative-response / out-of-range candidates from active testing to reduce screenshot clutter.
- Kept live but weird values under root `TESTING.*` for continued validation.
- Added alternate scaling candidates for the strongest new live values:
  - `019D` fuel-rate alternate
  - `019E` exhaust-flow candidate
- Kept VGT alternate scaling candidates from v0.7.1 because screenshots confirm the raw 16-bit values are live and useful.
- No signals were promoted to production in this update.

## Latest screenshot findings

### Strong testing — keep

| Area | PID | Finding | Decision |
| --- | --- | --- | --- |
| Fuel | `01015E` | Responded with plausible fuel-rate-style values around `0.3`, `1.4`, `5.4`, `13.5`. | Keep as strong direct fuel-rate candidate. |
| Fuel | `01019D` | Raw values `3`, `29`, `62`, `155` tracked load/fuel demand; existing `raw / 50` moved with it. | Keep raw and add alternate scaling candidates. |
| Regen / exhaust flow | `01019E` | Raw values `222`, `501`, `890`, `1697`; `raw / 5` gave `44.4`, `100`, `178`, `339`. | Keep and add alternate scaling candidates. |
| Engine / torque | `010161` | Driver demand torque showed low values and high demand around `89%`. | Strong testing candidate. |
| Engine / torque | `010162` | Actual engine torque showed `0%`, `7%`, `48%`, `86%`. | Strong testing candidate. |
| Engine / torque | `010163` | Engine reference torque held around `500 N m`. | Strong testing candidate / likely reference torque. |
| Boost / VGT | `22F470` / `22F471` | Raw 16-bit values are live; original 8-bit candidate stays around fake `3%`. | Keep raw and alternate div10/div20/div32/div64/div100 candidates. |

### Alive scouts — keep

| Area | PID | Finding | Decision |
| --- | --- | --- | --- |
| Regen | `22F48B` | Stayed at raw A `127` / raw AB `32512` outside active regen. | Keep until active regen capture. |
| Boost | `010170` | Raw AB around `1805–1829`; alive but not yet decoded. | Keep raw scout. |
| Fuel | `01016D` | Raw AB around `1801–1853`; alive but not yet decoded. | Keep raw scout. |
| EGR / packet | `010169` | Raw AB around `1792–1883`; alive but not yet decoded. | Keep raw scout. |
| Misc / EGRT | `22F463` | Test EGRT decode stayed around raw `500` / `10°C`; production engine-reference-torque signal remains valid. | Keep test EGRT as low-confidence only. |

### Removed / shelved from active JSON

These repeatedly showed `Negative response. Code: Out of range` in the latest screenshots and were removed from active JSON to save screen space.

| Area | PID | Removed signals |
| --- | --- | --- |
| Fuel | `0106` | Short-term fuel trim bank 1 test. |
| Fuel | `0108` | Short-term fuel trim bank 2 test. |
| Boost | `220233` | BARO 0233 candidate and raw companion. |
| Boost | `22F476` | Charge-air-cooler outlet temp candidate and raw companion. |
| Regen | `22F411` | Regen commanded candidate and raw companion. |
| Regen | `22F453` | DPF status bitfield candidate and raw companion. |
| Battery | `224006` | Battery SOH candidate and raw companion. |
| Battery | `224007` | Battery internal resistance candidate and raw companion. |
| Battery | `22400A` | Battery temperature candidate and raw companion. |
| Battery | `22032E` | Generator desired voltage candidate and raw companion. |
| Battery | `221632` | Generator command duty candidate and raw companion. |
| Tires | `222813` | TPMS front left candidate. |
| Tires | `222814` | TPMS front right candidate. |
| Tires | `222815` | TPMS rear right candidate. |
| Tires | `222816` | TPMS rear left candidate. |
| Tires | `222817` | TPMS unknown A candidate and raw companion. |
| Tires | `222818` | TPMS unknown B candidate and raw companion. |

## Added in v0.7.2

### TESTING.Fuel

| PID | Signal | Purpose |
| --- | --- | --- |
| `01019D` | `raw / 10` | Alternate fuel-rate scaling candidate. |
| `01019D` | `raw / 20` | Alternate fuel-rate scaling candidate. |
| `01019D` | `raw / 100` | Alternate fuel-rate scaling candidate. |

Existing retained:

- `01019D` raw AB
- `01019D` `raw / 50`
- `01015E` direct fuel-rate candidate

### TESTING.Regen

| PID | Signal | Purpose |
| --- | --- | --- |
| `01019E` | `raw / 10` | Alternate exhaust-flow scaling candidate. |
| `01019E` | `raw / 20` | Alternate exhaust-flow scaling candidate. |
| `01019E` | `raw / 50` | Alternate exhaust-flow scaling candidate. |

Existing retained:

- `01019E` raw AB
- `01019E` `raw / 5`

## v0.7.2 validation snapshot

| Check | Result |
| --- | ---: |
| Commands in updated default.json | 104 |
| Signals in updated default.json | 154 |
| Root TESTING signals | 88 |
| Signals with suggestedMetric | 17 |
| Duplicate signal IDs | 0 |
| Malformed commands | 0 |
| Empty/orphan command entries | 0 |
| Non-root test signal paths | 0 |
| JSON validation | Passed |
| Production signals modified | 0 |

## Validation summary

- Commands: 104
- Signals: 154
- Testing signals: 88
- Duplicate IDs: 0
- JSON validation: Passed
- Production signals modified: 0
- Removed signals: 28
- Added signals: 6

## Added signal IDs

```text
EVEREST_TEST_FUEL_RATE_ALT_019D_DIV10_CANDIDATE
EVEREST_TEST_FUEL_RATE_ALT_019D_DIV20_CANDIDATE
EVEREST_TEST_FUEL_RATE_ALT_019D_DIV100_CANDIDATE
EVEREST_TEST_EXHAUST_FLOW_019E_DIV10_CANDIDATE
EVEREST_TEST_EXHAUST_FLOW_019E_DIV20_CANDIDATE
EVEREST_TEST_EXHAUST_FLOW_019E_DIV50_CANDIDATE
```

## Removed signal IDs

```text
EVEREST_TEST_BATTERY_SOH_726_4006_RAW16_AB
EVEREST_TEST_BATTERY_SOH_726_4006_CANDIDATE
EVEREST_TEST_BATTERY_INTERNAL_RESISTANCE_726_4007_RAW16_AB
EVEREST_TEST_BATTERY_INTERNAL_RESISTANCE_726_4007_CANDIDATE
EVEREST_TEST_BATTERY_TEMPERATURE_726_400A_RAW16_AB
EVEREST_TEST_BATTERY_TEMPERATURE_726_400A_CANDIDATE
EVEREST_TEST_SHORT_TERM_FUEL_TRIM_BANK_1_0106
EVEREST_TEST_SHORT_TERM_FUEL_TRIM_BANK_2_0108
EVEREST_TEST_BARO_7E0_0233_RAW16_AB
EVEREST_TEST_BARO_7E0_0233_CANDIDATE
EVEREST_TEST_GENERATOR_DESIRED_VOLTAGE_7E0_032E_RAW16_AB
EVEREST_TEST_GENERATOR_DESIRED_VOLTAGE_7E0_032E_CANDIDATE
EVEREST_TEST_GENERATOR_COMMAND_DUTY_7E0_1632_RAW16_AB
EVEREST_TEST_GENERATOR_COMMAND_DUTY_7E0_1632_CANDIDATE
EVEREST_TEST_REGEN_COMMANDED_7E0_F411_RAW16_AB
EVEREST_TEST_REGEN_COMMANDED_7E0_F411_CANDIDATE
EVEREST_TEST_DPF_STATUS_BITFIELD_7E0_F453_RAW16_AB
EVEREST_TEST_DPF_STATUS_BITFIELD_7E0_F453_CANDIDATE
EVEREST_TEST_CHARGE_AIR_COOLER_OUTLET_TEMP_7E0_F476_RAW16_AB
EVEREST_TEST_CHARGE_AIR_COOLER_OUTLET_TEMP_7E0_F476_CANDIDATE
EVEREST_TEST_TPMS_FL_726_2813_PSI
EVEREST_TEST_TPMS_FR_726_2814_PSI
EVEREST_TEST_TPMS_RR_726_2815_PSI
EVEREST_TEST_TPMS_RL_726_2816_PSI
EVEREST_TEST_TPMS_2817_RAW16_AB
EVEREST_TEST_TPMS_2817_DIV20_PSI_CANDIDATE
EVEREST_TEST_TPMS_2818_RAW16_AB
EVEREST_TEST_TPMS_2818_DIV20_PSI_CANDIDATE
```

## Next validation targets

1. Fuel page:
   - Compare `015E` against `019D` div10/div20/div50/div100 during idle, cruise and moderate acceleration.
   - The best scaling should rise and fall naturally with throttle/load and return low at idle/coast.

2. Regen / exhaust flow page:
   - Compare `019E` div5/div10/div20/div50 against engine load, RPM, EGTs and DPF fullness.
   - Watch closely during an active regen.

3. VGT page:
   - Continue comparing `F470` and `F471` div20/div32/div64/div100.
   - Current best candidates remain div32/div64/div100 until throttle/load correlation proves one.

4. Torque page:
   - Compare `0161` driver demand torque and `0162` actual engine torque against accelerator pedal, engine load, boost and fuel rate.

5. Regen state:
   - Keep `F48B` until an active regen confirms whether it changes from raw A `127` / raw AB `32512`.

## Commit message

```text
Refine Everest TESTING pack from latest live captures
```

## Extended description

```text
Updated the Ford Everest MY25.25 PID testing pack from the latest live screenshot batch.

This refinement keeps live and useful Ranger-derived candidates, adds alternate scaling for fuel-rate and exhaust-flow candidates, and removes repeated negative-response items that were wasting screenshot space.

Strong retained candidates include fuel rate 015E, fuel-rate alternate 019D, exhaust flow 019E, driver demand torque 0161, actual engine torque 0162, engine reference torque 0163, VGT F470/F471 alternate scaling, and live raw scouts for 0169/016D/0170.

No production or stable signals were modified.
All experimental work remains under root TESTING.*.
```

---

# v0.7.3 — Boost + Oil Testing Expansion

Aligned default file: `default_everest_my25_25_v0_7_3_boost_oil_testing_expansion.json` / `signalsets/v3/default.json` target

## Update focus

- Built from v0.7.2 testing refinement.
- Preserved all production/stable signals unchanged.
- Split the previous large `TESTING.Boost` page into smaller screenshot-friendly groups:
  - `TESTING.Boost1` — MAP, BARO, TCBP and calculated/fixed-offset boost candidates.
  - `TESTING.Boost2` — commanded vs actual boost candidates and boost packet scouts.
  - `TESTING.Boost3` — VGT / wastegate / turbo actuator command-vs-feedback candidates.
  - `TESTING.Boost4` — deeper speculative turbo stage pressure, actuator, bypass and turbo-speed candidates.
- Added a new root `TESTING.Oil` group for engine oil pressure, oil pressure switch, virtual EOP, sensor voltage and variable oil-pump duty candidates.
- Did not reintroduce previously shelved repeated out-of-range clutter.
- Did not reinterpret `22F40F`; it remains the existing intake-air-temperature signal, not a MAP signal.
- Did not move `22F450` into oil pressure because it already exists in regen testing and external notes conflict on its meaning.

## v0.7.3 sanity-check snapshot

| Check | Result |
| --- | ---: |
| Commands in current default.json | 153 |
| Signals in current default.json | 258 |
| Root TESTING signals | 192 |
| Duplicate signal IDs | 0 |
| JSON validation | Passed |
| Production signals modified | 0 |

## Boost group split

### TESTING.Boost1 — MAP / BARO / calculated boost

Purpose: find clean absolute pressure sources and practical gauge-boost estimates.

Added / retained candidates include:

| PID | Purpose | Notes |
| --- | --- | --- |
| `010B` | Generic manifold absolute pressure gauge boost fixed offset | Uses `A - 101 kPa`; watch for 255 kPa absolute cap under load. |
| `22F40B` | F40B calculated boost fixed offset | Uses existing F40B manifold pressure formula minus fixed 14.5 psi. |
| `2216AD` | Next-Gen MAP HP / Sensor 1 | Raw + `raw / 100 kPa`; KOEO should be near BARO if correct. |
| `2216AE` | Next-Gen BARO | Raw + `raw / 100 kPa`; should stay close to atmospheric pressure. |
| `2216AF` | MAP Sensor 2 / post-intercooler / interstage candidate | Raw + `raw / 100 kPa`; compare with 16AD under load. |
| `221440` | Older Ford MAP absolute candidate | Raw + `raw * 0.25 kPa` + `raw * 0.03625 psi`. |
| `221442` | Older Ford BARO candidate | Raw + `raw * 0.25 kPa` + `raw * 0.03625 psi`. |
| `2203CA` | TCBP actual candidate | Raw + `raw * 0.25 kPa` + `raw / 100 kPa`. |
| `2203CB` | TCBP desired candidate | Raw + `raw * 0.25 kPa` + `raw / 100 kPa`. |

### TESTING.Boost2 — commanded vs actual boost

Purpose: identify whether the PCM exposes desired boost, actual boost and boost error directly.

| PID | Purpose | Notes |
| --- | --- | --- |
| `2216B0` | Panther boost desired | `raw * 0.01 - 100 kPa` gauge candidate. |
| `2216B1` | Panther boost actual | `raw * 0.01 - 100 kPa` gauge candidate. |
| `2216B2` | Panther boost error | Signed `raw * 0.01 kPa`; sign direction needs validation. |
| `221247` | MAP actual | Raw + `raw / 10 kPa`. |
| `221248` | MAP desired | Raw + `raw / 10 kPa`. |
| `220187` | Extended actual MAP scout | Raw only; published formula references B/C bytes. |
| `22018A` | Extended desired MAP scout | Raw only; published formula references B/C bytes. |
| `0170` | SAE boost-control packet scout | Possible multi-turbo commanded/measured packet; keep even if short captures look flat. |

### TESTING.Boost3 — turbo actuator control

Purpose: compare command vs feedback for VGT, HP turbo and LP wastegate behaviour.

| PID | Purpose | Notes |
| --- | --- | --- |
| `22F470` | Existing VGT commanded alternates | Kept, moved into Boost3. Previous screenshots showed raw movement with wrong original 8-bit scaling. |
| `22F471` | Existing VGT actual alternates | Kept, moved into Boost3. `div32`, `div64` and `div100` remain especially interesting. |
| `2216C0` | HP VGT desired | Raw + `A*100/255`. |
| `2216C1` | HP VGT actual | Raw + `A*100/255`. |
| `2216C2` | LP wastegate desired | Raw + `A*100/255`. |
| `2216C3` | LP wastegate actual | Raw + `A*100/255`. |
| `22116E` | Ford VGT commanded position | Raw + `A*100/255`. |

### TESTING.Boost4 — speculative turbo stage / deep actuator candidates

Purpose: broader turbo-only hunting page for inter-stage pressure, bypass valve, actuator control and turbo speed candidates.

Added candidates:

```text
221249, 22124A, 22124B, 22124C, 22124D
221260, 221261, 221262, 221263, 221264
221265, 221266, 221267, 221268, 221269, 22126A
221290, 221291
```

Most Boost4 signals are intentionally speculative. Keep anything that responds and changes with RPM/load/boost. Shelve anything repeatedly returning negative response / out of range to save screenshot space.

## TESTING.Oil — engine oil pressure investigation

Purpose: determine whether this Everest exposes true variable oil pressure, only a binary pressure switch, a PCM virtual oil-pressure estimate, or only variable oil-pump control/duty data.

Added candidates:

| PID | Purpose | Formula candidates |
| --- | --- | --- |
| `2211A6` | Next-Gen EOP actual candidate | Raw, raw kPa, raw × 0.145038 psi. |
| `2211A7` | Oil pressure sensor voltage | Raw, raw / 1000 V. |
| `220415` | Ford oil pressure candidate | A kPa, raw kPa, raw × 0.145038 psi. |
| `221439` | Oil pressure switch/status | Raw A. |
| `221438` | Virtual/calculated EOP | Raw, raw × 0.1 kPa. |
| `221431` | Oil pressure control solenoid / variable pump duty | Raw, A × 100 / 255 %. |
| `22034D` | Alternate EOP candidate | Raw, A × 4 kPa, A × 0.579 psi. |
| `221340` | EOP actual candidate | Raw, raw / 10 kPa. |
| `221341` | EOP desired / target candidate | Raw, raw / 10 kPa. |
| `221342` | EOP variant candidate | Raw, raw / 10 kPa. |
| `221350` | EOP alternate candidate | Raw, raw / 10 kPa. |
| `221351` | EOP downstream / filter delta candidate | Raw, raw / 10 kPa. |

## Oil validation notes

| State | What to expect if true pressure | What to expect if switch only |
| --- | --- | --- |
| KOEO | Near zero pressure / low voltage | 0 / not OK |
| Start engine | Pressure rises quickly | Flips to 1 / OK |
| Cold idle | Higher pressure than warm idle | Stays 1 |
| Warm idle | Lower but stable pressure | Stays 1 |
| Rev / load | Pressure should rise | Still stays 1 |

If a value instantly jumps to one fixed number and never moves with RPM, oil temperature or load, treat it as a switch/status or bad scaling rather than true pressure.

## Boost validation notes

For MAP/BARO candidates:

- KOEO: MAP should be close to BARO, roughly 95–105 kPa depending on local weather/elevation.
- Idle: diesel MAP should stay close to BARO; gauge boost should be near 0.
- Load: actual boost should rise cleanly.
- If a value flatlines near 250–255 kPa absolute, it is likely hitting the generic single-byte MAP cap.

For commanded vs actual boost:

- Desired and actual should broadly track during steady throttle.
- Brief overshoot during sudden throttle is normal.
- Persistent desired high + actual low + high actuator duty suggests possible boost leak/restriction or wrong scaling.

## Commit message

```text
Add Boost and Oil TESTING expansion for Everest MY25.25
```

## Extended description

```text
Built v0.7.3 from the v0.7.2 Ford Everest MY25.25 testing refinement.

This update expands boost investigation into smaller screenshot-friendly root TESTING groups and adds a new TESTING.Oil group.

Boost changes:
- Split TESTING.Boost into Boost1, Boost2, Boost3 and Boost4.
- Added Next-Gen MAP/BARO/TCBP candidates.
- Added commanded-vs-actual boost candidates.
- Added VGT, HP VGT and LP wastegate command/feedback candidates.
- Added speculative turbo-stage, actuator, bypass and turbo-speed candidates.

Oil changes:
- Added actual oil pressure, desired/virtual oil pressure, sensor voltage, switch/status and variable oil-pump duty candidates.
- Kept oil pressure candidates under TESTING.Oil only until live behaviour proves whether this vehicle exposes true pressure or only switch/proxy data.

No production/stable signals were modified.
All experimental work remains under root TESTING.*.
```


---

# v0.7.4 — Negative response cleanup pass

Aligned default file: `default_everest_my25_25_v0_7_4_negative_response_cleanup.json` / `signalsets/v3/default.json` target

## Update focus

- Built from v0.7.3 Boost + Oil Testing Expansion.
- Removed first-pass items reported as `Negative response. Code: Out of range`.
- Preserved production/stable signals unchanged.
- Preserved live/weird/suspect candidates that were not reported as out-of-range.
- Kept all remaining experimental work under root `TESTING.*`.

## Removed / shelved in v0.7.4

These items were removed from active testing to reduce screenshot clutter. They can be reintroduced later only if a new header, command format, or FORScan evidence suggests a better path.

| Area | Removed items |
| --- | --- |
| 4x4 `7E0` transfer-case candidates | `C45D`, `C460`, `C461`, `C462`, `C463` on `7E0 7E8` |
| Boost1 MAP/BARO/TCBP candidates | `0210`, `16AE`, `1440`, `1442`, `03CA`, `03CB` |
| Boost2 commanded/actual candidates | `0187`, `018A`, `1247`, `1248`, `16B0`, `16B1`, `16B2`, `0170/6B raw packet` |
| Boost3 actuator candidates | `116E`, `16C0`, `16C1`, `16C2`, `16C3` |
| Oil pressure candidates | `11A6`, `11A7`, `0415`, `034D`, `1340`, `1341`, `1342`, `1350`, `1351`, `1431`, `1438`, `1439` |
| Regen candidates | `F412`, `F413`, `F450`, `F87C` |
| Ranger transmission mirror candidates | `7E0 1E1C`, `7E0 1E23` |

## Important retained candidates

The cleanup does **not** reject the whole boost/oil direction. It only removes commands that came back out-of-range in this first test pass.

Retained for further testing:

- Existing production MAP/manifold pressure and air-temperature signals.
- Existing confirmed DPF and transmission production signals.
- Existing VGT `F470/F471` alternate scaling candidates unless separately rejected.
- Boost candidates not listed in the negative-response pass.
- 4x4 candidates on alternate headers not reported as out-of-range.
- Any live, static, weird, or scaling-suspect values not explicitly reported as out-of-range.

## v0.7.4 sanity-check snapshot

| Check | Result |
| --- | ---: |
| Commands in default.json | 111 |
| Signals in default.json | 172 |
| Root TESTING signals | 106 |
| Duplicate signal IDs | 0 |
| JSON validation | Passed |
| Production signals modified | 0 |
| Removed signals | 86 |
| Added signals | 0 |

## Commit message

```text
Remove first-pass negative-response TESTING candidates
```

## Extended description

```text
Cleaned the Ford Everest MY25.25 v0.7.3 testing pack after the first negative-response pass.

Removed commands and signals reported as `Negative response. Code: Out of range`, including unsupported 7E0 4x4 candidates, several boost commanded/actual candidates, oil-pressure candidates, selected regen candidates, and Ranger transmission mirror candidates.

No production/stable signals were modified. Remaining live, weird, raw, scaling-suspect, and untested candidates were preserved for further validation.
```


---

# v0.7.5 — Boost4 negative-response cleanup

Aligned default file: `default_everest_my25_25_v0_7_5_boost4_negative_response_cleanup.json` / `signalsets/v3/default.json` target

## Update focus

- Built from v0.7.4 negative-response cleanup.
- Removed additional screenshot-confirmed `Negative response. Code: Out of range` candidates from the boost testing pages.
- Most removed items were from `TESTING.Boost4`, plus remaining dead MAP/pressure items in `TESTING.Boost1`.
- Preserved all production/stable signals.
- Preserved remaining live, weird, raw, scaling-suspect, and not-yet-rejected TESTING candidates.

## Screenshot evidence summary

The following groups/items were reported or shown as repeated out-of-range and removed from active JSON to reduce screen clutter.

### Removed commands

| Command | Area | Reason |
| --- | --- | --- |
| `220247` | Boost1 / MAP boost gauge | Negative response / out of range. |
| `2216AD` | Boost1 / MAP HP sensor 1 | Negative response / out of range. |
| `2216AF` | Boost1 / MAP sensor 2 / post-intercooler pressure | Negative response / out of range. |
| `221249` | Boost4 / turbo stage LP outlet pressure | Negative response / out of range. |
| `22124A` | Boost4 / turbo stage HP inlet pressure | Negative response / out of range. |
| `22124B` | Boost4 / inter-stage pressure actual | Negative response / out of range. |
| `22124C` | Boost4 / inter-stage pressure desired | Negative response / out of range. |
| `22124D` | Boost4 / charge-air cooler outlet pressure | Negative response / out of range. |
| `221260` | Boost4 / turbo actuator scout 1 | Negative response / out of range. |
| `221261` | Boost4 / turbo actuator scout 2 | Negative response / out of range. |
| `221262` | Boost4 / turbo actuator scout 3 | Negative response / out of range. |
| `221263` | Boost4 / HP turbo wastegate commanded | Negative response / out of range. |
| `221264` | Boost4 / HP turbo wastegate actual | Negative response / out of range. |
| `221265` | Boost4 / LP turbo wastegate commanded | Negative response / out of range. |
| `221266` | Boost4 / LP turbo wastegate actual | Negative response / out of range. |
| `221267` | Boost4 / turbo bypass valve commanded | Negative response / out of range. |
| `221268` | Boost4 / turbo bypass valve actual | Negative response / out of range. |
| `221269` | Boost4 / turbo position error / deviation | Negative response / out of range. |
| `22126A` | Boost4 / turbo control status flags | Negative response / out of range. |
| `221290` | Boost4 / HP turbo speed | Negative response / out of range. |
| `221291` | Boost4 / LP turbo speed | Negative response / out of range. |

## Decision notes

- `010142` control module voltage was **not** removed in this pass because the screenshot showed no red negative-response text; it only appeared blank/unpopulated. Retest before shelving.
- This cleanup does not mean these PIDs are impossible on all Ford/Ranger/Everest variants; only that they are not responding on this MY25.25 Everest test path/header.
- Do not re-add these unless a later source gives a better header, byte path, or evidence from this vehicle.

## v0.7.5 sanity-check snapshot

| Check | Result |
| --- | ---: |
| Commands in current default.json | 90 |
| Signals in current default.json | 131 |
| Root TESTING signals | 65 |
| Duplicate signal IDs | 0 |
| JSON validation | Passed |
| Production signals modified | 0 |
| Removed commands | 21 |
| Removed signals | 41 |

## Commit message

```text
Remove additional Boost4 negative-response candidates
```

## Extended description

```text
Cleaned the Ford Everest MY25.25 v0.7.4 testing pack after additional screenshot-confirmed negative-response results.

Removed remaining unsupported MAP/boost candidates and the Boost4 speculative turbo-stage, actuator, bypass, wastegate and turbo-speed commands that returned `Negative response. Code: Out of range`.

No production/stable signals were modified. Remaining live, weird, raw, scaling-suspect and unresolved TESTING candidates were preserved.
```


---

# v0.7.6 — Torque refinement and frozen EGRT cleanup

Aligned default file: `default_everest_my25_25_v0_7_6_torque_refinement.json` / `signalsets/v3/default.json` target

## Update focus

- Built from v0.7.5 Boost4 negative-response cleanup.
- Removed the frozen `F463` EGRT testing decode and raw companion from active `TESTING.Misc`.
- Preserved the responding production/reference `F463` engine reference torque signal.
- Updated notes for the strong torque candidates `0161`, `0162`, and `0163`.
- Preserved live raw scouts `0169` and `0170` for future correlation.
- No production/stable signals were modified.

## Latest live-capture observations

### Torque candidates

| Signal | PID | Observed behaviour | Decision |
| --- | --- | --- | --- |
| `EVEREST_TEST_DRIVER_DEMAND_TORQUE_0161` | `010161` | Near 0% while coasting/foot-off; up to about 100% during hard takeoff. | Very high confidence testing candidate. |
| `EVEREST_TEST_ACTUAL_ENGINE_TORQUE_0162` | `010162` | Near 0% while coasting/foot-off; up to about 93-95% during hard takeoff. | Very high confidence testing candidate. |
| `EVEREST_TEST_ENGINE_REFERENCE_TORQUE_0163` | `010163` | Stable at 500 N m across captures. | Plausible engine reference/max torque value; keep for scaling context. |

The coasting 0% result is important evidence. It supports the interpretation that `0161` is driver/requested torque demand and `0162` is actual/delivered engine torque rather than random load-like noise.

### Live raw scouts retained

| Signal | PID | Observed behaviour | Decision |
| --- | --- | --- | --- |
| `EVEREST_TEST_RANGER_PACKET_70_RAW16_AB` | `010170` | Alive with small movement around raw 1805-1821. | Keep as boost-control packet raw scout. |
| `EVEREST_TEST_RANGER_PACKET_69_RAW16_AB` | `010169` | Alive and moving around raw 1792-1846. | Keep as EGR/air-path packet raw scout. |

### Frozen EGRT candidate removed

| Signal | PID | Observed behaviour | Decision |
| --- | --- | --- | --- |
| `EVEREST_TEST_EGRT_F463_CELSIUS` | `22F463` | Frozen at 10°C across captures. | Removed from active testing. |
| `EVEREST_TEST_EGRT_F463_RAW16_AB` | `22F463` | Frozen at raw 500 across captures. | Removed from active testing. |

The `F463` EGRT decode did not behave like a live EGR/exhaust temperature value. It stayed fixed through idle, coasting, traffic-light state, and hard takeoff. This made it low-value screenshot clutter.

## v0.7.6 sanity-check snapshot

| Check | Result |
| --- | ---: |
| Commands in current default.json | 90 |
| Signals in current default.json | 129 |
| Root TESTING signals | 63 |
| Duplicate signal IDs | 0 |
| JSON validation | Passed |
| Production signals modified | 0 |
| Removed commands | 0 |
| Removed signals | 2 |
| Added signals | 0 |

## Commit message

```text
Remove frozen EGRT testing decode and strengthen torque notes
```

## Extended description

```text
Tidied the Ford Everest MY25.25 v0.7.5 testing pack after live torque and scout captures.

Removed the frozen F463 EGRT testing decode and raw companion from active TESTING.Misc after repeated captures showed a fixed 10°C / raw 500 value across multiple vehicle states.

Updated testing notes for the 0161 driver demand torque, 0162 actual engine torque, and 0163 engine reference torque candidates. Coasting produced near-zero torque values, while hard takeoff produced near-maximum demand/actual torque, strengthening confidence in these signals.

Preserved live raw scouts 0169 and 0170 for further correlation. No production or stable signals were modified.
```


---

# v0.7.7 — Path and name housekeeping

Aligned default file: `default_everest_my25_25_v0_7_7_path_name_housekeeping.json` / `signalsets/v3/default.json` target

## Update focus

- Tidied production signal paths so Everest-specific working PIDs sit at the root of their main category where practical.
- Kept generic SAE / comparison signals under the relevant `*.Generic` path.
- Preserved the existing Emissions structure for now.
- Moved movement subcategory items into root `Movement` as requested.
- Moved odometer into `Trips`.
- Moved oil life remaining into root `Engine`.
- Polished several visible names for consistency without changing signal IDs, formulas or suggested metrics.
- Reordered commands so related PIDs sit closer together in the file and UI grouping is easier to scan.

## Path changes

| Signal | Old path | New path |
| --- | --- | --- |
| `EVEREST_MAP_SENSOR_VOLTAGE_0301` | `Engine.Boost` | `Engine` |
| `EVEREST_WASTEGATE_DUTY_CYCLE_0462` | `Engine.Boost` | `Engine` |
| `EVEREST_MANIFOLD_PRESSURE_F40B` | `Engine.Boost` | `Engine` |
| `EVEREST_OIL_LIFE_REMAINING_054B` | `Maintenance` | `Engine` |
| `GENERIC_MAP_010B` | `Engine.Boost.Generic` | `Engine.Generic` |
| `FORD_FR_VSS` | `Movement.WheelSpeed` | `Movement` |
| `FORD_FL_VSS` | `Movement.WheelSpeed` | `Movement` |
| `FORD_RR_VSS` | `Movement.WheelSpeed` | `Movement` |
| `FORD_RL_VSS` | `Movement.WheelSpeed` | `Movement` |
| `FORD_LAT_ANG` | `Movement.BodyMotion` | `Movement` |
| `FORD_LAT_G` | `Movement.BodyMotion` | `Movement` |
| `EVEREST_ODOMETER_01A6` | `Movement` | `Trips` |
| `EVEREST_TRANS_TEMP_1E1C_DIV16` | `Transmission.Everest` | `Transmission` |


## Name polish

| Signal | Old name | New name |
| --- | --- | --- |
| `EVEREST_BATTERY_AGE_SCALAR_4027` | Battery age scalar | Battery age raw counter |
| `EVEREST_BATTERY_CHARGE_4028_DIV255` | Battery charge 4028 div255 | Battery charge 4028 div255 compare |
| `EVEREST_GRILL_SHUTTER_DUTY_CYCLE_0707` | Grill shutter duty cycle | Grille shutter duty cycle |
| `EVEREST_IAT_FAULT_0700` | Intake temperature fault | Intake temperature fault status |
| `EVEREST_IAT_SENSOR_VOLTAGE_1279` | Intake temperature sensor voltage | Intake air temperature sensor voltage |
| `GENERIC_MAF_0110` | Air flow rate from mass air flow sensor | Generic mass air flow rate |
| `FORD_DTE_DISP` | Distance to empty, displayed | Distance to empty displayed |
| `EVEREST_LPFP_DUTY_0307` | Low pressure fuel pump commanded duty cycle | Low-pressure fuel pump commanded duty cycle |
| `EVEREST_HPFP_SENSOR_VOLTAGE_0324` | High pressure fuel rail sensor voltage | High-pressure fuel rail sensor voltage |
| `EVEREST_HPFP_DESIRED_03DC` | High pressure fuel pressure desired | High-pressure fuel pressure desired |
| `EVEREST_LPFP_DESIRED_041F` | Fuel pressure, low desired | Low-pressure fuel pressure desired |
| `EVEREST_LPFP_ACTUAL_0548` | Fuel pressure, low actual | Low-pressure fuel pressure actual |
| `EVEREST_LPFP_SENSOR_VOLTAGE_054D` | Fuel pressure sensor voltage, low | Low-pressure fuel pressure sensor voltage |
| `EVEREST_TRANS_TEMP_1E1C_DIV16` | Transmission fluid temperature Everest div16 | Transmission fluid temperature |
| `EVEREST_TRANS_TEMP_1E1C_5_72` | Transmission fluid temperature 5/72 compare | Transmission fluid temperature compare |
| `EVEREST_OSS_1E15_RPM` | Output shaft speed OSS 1E15 | Transmission output shaft speed |
| `EVEREST_ISS_TSS_1E16_RPM` | Input shaft speed ISS/TSS 1E16 | Transmission input shaft speed |


## Validation summary

| Check | Result |
| --- | ---: |
| Commands | 90 |
| Signals | 129 |
| Testing signals | 63 |
| Duplicate IDs | 0 |
| JSON validation | Passed |
| Non-root testing paths remaining | 0 |
| Production signal IDs changed | 0 |
| Formula changes | 0 |
| Removed signals | 0 |
| Added signals | 0 |

## Commit message

```text
Tidy Everest PID paths and visible signal names
```

## Extended description

```text
Tidied the Ford Everest MY25.25 signal pack paths and visible names after v0.7.6.

Everest-specific working PIDs were moved to the root of their main categories where requested, movement subcategory items were flattened into Movement, odometer was moved to Trips, oil life was moved to Engine, and generic SAE comparison signals were kept under the relevant Generic category.

No signal IDs, formulas, suggested metrics, production commands or TESTING formulas were changed. Existing Emissions structure was preserved.
```


---

# v0.7.8 — Generic and transmission display cleanup

Aligned default file: `default_everest_my25_25_v0_7_8_generic_transmission_cleanup.json` / `signalsets/v3/default.json` target

## Update focus

- Renamed the generic SAE MAP signal for clearer app display.
- Removed the Everest prefix from the generic MAP signal ID so it is clearly a generic OBD-II signal rather than an Everest-specific Ford enhanced PID.
- Moved the 5/72 transmission-temperature comparison signal from `Transmission.Generic` to root `Transmission`.
- Renamed the 5/72 comparison signal to sit more cleanly beside the preferred div16 transmission-fluid-temperature signal.
- No formulas, commands, suggested metrics, TESTING formulas, or production/stable logic were changed.

## Specific changes

| Signal | Previous | Updated | Reason |
| --- | --- | --- | --- |
| Generic MAP signal ID | `EVEREST_GENERIC_MAP_010B` | `GENERIC_MAP_010B` | `010B` is a generic SAE OBD-II MAP PID, not an Everest-specific Ford enhanced PID. |
| Generic MAP visible name | `Generic MAP` | `Generic manifold absolute pressure` | Clearer display in the app. |
| Transmission temp 5/72 path | `Transmission.Generic` | `Transmission` | This is an Everest transmission comparison decode, not a generic OBD item. |
| Transmission temp 5/72 visible name | `Generic transmission temperature compare 5/72` | `Transmission fluid temperature compare` | Cleaner display beside the preferred transmission temperature signal. |

## Validation summary

| Check | Result |
| --- | ---: |
| Commands | 90 |
| Signals | 129 |
| Testing signals | 63 |
| Duplicate IDs | 0 |
| JSON validation | Passed |
| Non-root testing paths | 0 |
| Formula changes | 0 |
| Signal ID changes | 1 |
| Path changes | 1 |
| Removed signals | 0 |
| Added signals | 0 |

## Commit message

```text
Clarify generic MAP and transmission compare display
```

## Extended description

```text
Tidied two display/path issues in the Ford Everest MY25.25 signal pack.

The generic SAE MAP PID 010B now uses the signal ID GENERIC_MAP_010B and visible name Generic manifold absolute pressure, making it clearer that the PID is generic OBD-II rather than Everest-specific.

The 5/72 transmission-fluid-temperature comparison signal was moved from Transmission.Generic to root Transmission and renamed Transmission fluid temperature compare so it sits beside the preferred div16 transmission temperature signal.

No formulas, commands, suggested metrics, production logic, or TESTING formulas were changed.
```


# v0.7.9 — Generic ID and engine path cleanup

Aligned default file: `default_everest_my25_25_v0_7_9_generic_id_and_engine_path_cleanup.json` / `signalsets/v3/default.json` target

## Update focus

- Clarified remaining generic SAE signal IDs so they do not appear Everest-specific in the app.
- Kept generic SAE working signals under their category `.Generic` paths.
- Moved grille shutter duty cycle from `Body` to `Engine` because it is an engine thermal / airflow management signal.
- No formulas, commands, suggested metrics, production logic or TESTING signals were changed.

## Generic SAE naming rule locked in

| Signal type | ID style | Path rule |
| --- | --- | --- |
| Generic SAE Mode 01 PID | `GENERIC_*` | `Category.Generic` |
| Everest/Ford-specific confirmed PID | `EVEREST_*` | Root category |
| Experimental / uncertain PID | `EVEREST_TEST_*` | `TESTING.*` |

## Changes made

| Item | Previous | Updated |
| --- | --- | --- |
| Generic MAF ID | `EVEREST_GENERIC_MAF_0110` | `GENERIC_MAF_0110` |
| Generic MAF path | `Engine.Generic` | unchanged |
| Generic fuel level ID | `EVEREST_GENERIC_FUEL_LEVEL_012F` | `GENERIC_FUEL_LEVEL_012F` |
| Generic fuel level path | `Fuel.Generic` | unchanged |
| Grille shutter duty path | `Body` | `Engine` |

## v0.7.9 validation summary

| Check | Result |
| --- | ---: |
| Commands | 90 |
| Signals | 129 |
| Testing signals | 63 |
| Duplicate IDs | 0 |
| JSON validation | Passed |
| Formula changes | 0 |
| Path changes | 1 |
| Signal ID changes | 2 |
| Removed signals | 0 |
| Added signals | 0 |

## Commit message

```text
Clarify generic IDs and move grille shutter to Engine
```

## Extended description

```text
Tidied the Ford Everest MY25.25 signal pack after v0.7.8.

Renamed remaining generic SAE working signal IDs so they no longer use the EVEREST_GENERIC prefix: GENERIC_MAF_0110 and GENERIC_FUEL_LEVEL_012F. These remain under their relevant Generic category paths because they are generic SAE Mode 01 PIDs, not Everest-specific Ford enhanced PIDs.

Moved grille shutter duty cycle from Body to Engine because it belongs with engine airflow and thermal-management signals.

No formulas, commands, suggested metrics, TESTING signals or production logic were changed.
```

# v0.7.10 — DPF regen validation update

Aligned default file: `default_everest_my25_25_v0_7_10_dpf_regen_validation.json` / `signalsets/v3/default.json` target

## Update focus

- Updated DPF/regen notes after a captured active DPF regeneration event in Pelican and screenshots.
- Strengthened validation descriptions for the confirmed DPF fullness and distance-since-regen production signals.
- Kept `F451` as a live regen state/request candidate after it showed value `4` during the captured regen.
- Marked `019E` exhaust-flow testing as strongly alive and noted `div20` as the current likely-best scaling candidate.
- Removed low-value regen testing clutter where the visible decode did not help during the active regen capture.

## Captured regen evidence

During the captured regen event:

- `EVEREST_DPF_FULLNESS_0610` dropped from approximately `76.7%` at session start to approximately `17.7%` after completion.
- Visible screenshots showed the DPF fullness moving through approximately `48%` and then down to approximately `19%`.
- `EVEREST_DISTANCE_SINCE_DPF_REGEN_0614` reset from approximately `125.8 km` to `0.5 km`, then immediately began counting upward again.
- This strongly confirms both `220610` and `220614` as production DPF signals.

## Regen testing decisions

| Signal | Decision | Reason |
| --- | --- | --- |
| `EVEREST_TEST_DPF_REGEN_REQUEST_7E0_F451_CANDIDATE` | Keep | Responded with value `4` during active regen; needs non-regen comparison. |
| `EVEREST_TEST_DPF_REGEN_REQUEST_7E0_F451_RAW16_AB` | Keep | Raw companion for F451. |
| `EVEREST_TEST_DPF_REGEN_STATUS_0440_RAW8_A` | Removed | Stayed `0` during captured active regen; not useful as active-regen status. |
| `EVEREST_TEST_DPF_STATUS_F48B_RAW8_A` | Removed from active JSON | Visible decode stayed fixed at `127`; database showed later packet bytes changed, but current A-byte decode is not useful. |
| `EVEREST_TEST_DPF_STATUS_F48B_RAW16_AB` | Removed from active JSON | Visible decode stayed fixed at `32512`; packet may still be useful if byte-offset decoding becomes available later. |

## Exhaust flow `019E`

`019E` moved strongly during the regen/load drive and is now a strong testing candidate.

Observed raw examples:

| Raw AB | div5 | div10 | div20 | div50 |
| ---: | ---: | ---: | ---: | ---: |
| 185 | 37.0 | 18.5 | 9.3 | 3.7 |
| 751 | 150.2 | 75.1 | 37.6 | 15.0 |
| 2588 | 517.6 | 258.8 | 129.4 | 51.8 |
| 3583 | 716.6 | 358.3 | 179.2 | 71.7 |

Current read:

- `div20` looks like the most plausible current scaling candidate.
- `div5`, `div10`, `div50`, and raw AB are kept for comparison.
- Continue comparing against RPM, load, EGT and regen state.

## v0.7.10 validation summary

| Check | Result |
| --- | ---: |
| Commands | 88 |
| Signals | 126 |
| Testing signals | 60 |
| Duplicate IDs | 0 |
| JSON validation | Passed |
| Formula changes | 0 |
| Production signal formulas modified | 0 |
| Removed commands | 2 |
| Removed signals | 3 |
| Added signals | 0 |

## Commit message

```text
Validate DPF regen behaviour and trim low-value regen tests
```

## Extended description

```text
Updated the Ford Everest MY25.25 signal pack after a captured active DPF regeneration event.

The regen capture confirmed DPF fullness 220610 falling from high soot load to post-regen values, and confirmed distance-since-DPF-regen 220614 resetting from approximately 125.8 km to 0.5 km before counting upward again.

Updated descriptions for the confirmed DPF production signals, kept F451 as a live regen state/request candidate after it showed value 4 during active regen, and strengthened exhaust-flow 019E notes with div20 marked as the current likely-best scaling candidate.

Removed low-value regen testing clutter where the visible decode did not help during active regen: 0440 raw A and F48B A/AB. The F48B packet may still contain useful later bytes, but the current visible A/AB decodes stayed fixed and are not useful in the app view.

No production formulas, confirmed signal IDs, or non-regen testing formulas were changed.
```

# v0.7.11 — Current gear alt promotion and DPF wording refinement

Aligned default file: `default_everest_my25_25_v0_7_11_gear_alt_promotion.json` / `signalsets/v3/default.json` target

## Update focus

- Promoted the 7E0/221E12 current gear candidate from `TESTING.Transmission` to production `Transmission`.
- Kept the promoted gear signal labelled as an alternate because another working transmission gear indicator already exists.
- Softened the DPF fullness description to reflect that the signal is strongly DPF/soot-load related, but dash display correlation is still under observation.

## Gear promotion evidence

`EVEREST_TEST_RANGER_GEAR_7E0_1E12` was promoted after live comparison against the vehicle dash.

User validation:

- Pelican app gear matched the dash gear.
- The value changed at the same time as dash gear changes.
- Screenshot-labelled states included stopped/idle, moving, uphill load, cruising around 70–80 km/h, and hard acceleration.
- Observed values included `1`, `4`, `5`, and `8`, with earlier captures showing a broader plausible range.

Promoted signal:

| Previous ID | New ID | Path | Name |
| --- | --- | --- | --- |
| `EVEREST_TEST_RANGER_GEAR_7E0_1E12` | `EVEREST_CURRENT_GEAR_ALT_1E12` | `Transmission` | `Transmission current gear alt` |

Current read:

- Very high confidence current-gear signal.
- Kept as `_ALT` because another working gear indicator remains available.
- Formula/map unchanged.
- Header/command unchanged: `7E0 / 221E12`.

## DPF fullness wording refinement

`EVEREST_DPF_FULLNESS_0610` remains a strong production DPF fullness / soot-load signal.

Reason for wording refinement:

- Regen behaviour is validated by the captured active regen event.
- `220610` dropped strongly through the regen.
- `220614` reset at regen completion.
- However, the dash display may be rounded, smoothed, or modelled differently, so dash percentage correlation remains under observation.

No DPF formula was changed.

## v0.7.11 validation summary

| Check | Result |
| --- | ---: |
| Commands | 88 |
| Signals | 126 |
| Testing signals | 59 |
| Duplicate IDs | 0 |
| JSON validation | Passed |
| Formula changes | 0 |
| Production signal formulas modified | 0 |
| Promoted signals | 1 |
| Path changes | 1 |
| Signal ID changes | 1 |
| Removed signals | 0 |
| Added signals | 0 |

## Commit message

```text
Promote alternate current gear signal
```

## Extended description

```text
Promoted the Ford Everest MY25.25 7E0/221E12 current gear candidate from TESTING.Transmission into the production Transmission category after live validation against the vehicle dash.

The promoted gear signal matched the dash gear and changed at the same time as dash gear changes during real driving. It is labelled as an alternate current-gear signal because another working transmission gear indicator already exists.

Also refined the DPF fullness 220610 description to keep the signal production-ready while noting that dash display percentage correlation remains under observation. No DPF, transmission, testing, or production formulas were changed.
```


---

## v0.7.12 — Torque promotion and TESTING cleanup

### Update focus

- Promoted the torque trio from `TESTING.Misc` into production `Engine` after repeated labelled screenshot validation.
- Removed the `Battery` category and `TESTING.Battery` items from the active JSON.
- Removed the `TESTING.4x4` group from the active JSON.
- Consolidated sparse boost testing groups:
  - `TESTING.Boost2` moved into `TESTING.Boost1`.
  - `TESTING.Boost4` moved into `TESTING.Boost1`.
  - `TESTING.Boost3` renamed to `TESTING.VGT`.
- No formulas were changed in this update.

### Promoted torque signals

| Signal ID | Name | Path | Evidence |
| --- | --- | --- | --- |
| `EVEREST_DRIVER_DEMAND_TORQUE_0161` | Engine driver demand torque | `Engine` | Labelled screenshots showed near 0% stopped/coasting, low values during light cruise, and ~100% during hard takeoff. |
| `EVEREST_ACTUAL_ENGINE_TORQUE_0162` | Engine actual torque | `Engine` | Labelled screenshots showed near 0% stopped/coasting, low values during light cruise, and high values during hard acceleration/load. |
| `EVEREST_ENGINE_REFERENCE_TORQUE_0163` | Engine reference torque | `Engine` | Stable 500 N m value across captures; plausible reference scalar for the 2.0L Bi-Turbo diesel. |

### Screenshot evidence used

The latest labelled screenshot set included:

- `stopped.PNG` — both demand and actual torque at 0%.
- `coasting.PNG` — both demand and actual torque at 0%.
- `chilled 50km.PNG` — light-load torque values around 7–11%.
- `hard take off(1).PNG` — demand around 101% and actual around 96%.
- Additional moving/load screenshots showed plausible mid-range torque values.

This gives enough behavioural validation to promote the torque trio out of testing.

### Removed from active JSON

| Area | Removed | Reason |
| --- | --- | --- |
| `Battery` | Root battery/alternator/BMS-style signals | User requested complete Battery removal; app also only showed limited auxiliary 12V battery information. |
| `TESTING.Battery` | `0142` control module voltage test | Repeatedly blank/no useful app-visible value. |
| `TESTING.4x4` | `7E2 C45D/C460/C461/C462/C463` candidates | Repeatedly blank/out-of-range/no useful app-visible value; removed to reduce screenshot clutter. |

### Boost / VGT path cleanup

| Previous path | New path | Notes |
| --- | --- | --- |
| `TESTING.Boost1` | `TESTING.Boost1` | Kept as the main boost/MAP/BARO testing group. |
| `TESTING.Boost2` | `TESTING.Boost1` | Merged because it only contained one live raw packet item. |
| `TESTING.Boost4` | `TESTING.Boost1` | Merged to keep boost pressure/scout items together. |
| `TESTING.Boost3` | `TESTING.VGT` | Renamed because this group is really VGT commanded/actual scaling work. |

### Current notes carried forward

- `F451` remains unresolved: value `4` was observed during an active regen event and also during normal/non-regen driving, so `4` does **not** by itself mean active regeneration.
- `019E` exhaust flow remains testing-only. `div20` is still the likely-best visible scaling candidate, but units are not confirmed.
- `F46D` and `F478` remain strong EGT candidates but are not promoted yet.
- `220610` remains production but wording stays cautious: strong DPF fullness / soot-load candidate, validated by regen drop and distance reset behaviour; dash display correlation still under observation.

### Validation summary

| Check | Result |
| --- | ---: |
| Commands in current default.json | 77 |
| Signals in current default.json | 108 |
| Testing signals | 45 |
| Duplicate signal IDs | 0 |
| JSON validation | Passed |
| Production signal formulas modified | 0 |
| Promoted signals | 3 |
| Removed commands | 11 |
| Removed signals | 18 |
| Added signals | 0 |

### Commit message

```text
Promote torque signals and clean testing groups
```

### Extended description

```text
Promoted the Ford Everest MY25.25 torque trio from TESTING.Misc into the production Engine category after repeated labelled screenshot validation across stopped, coasting, light cruise, load and hard takeoff states.

The promoted signals are driver demand torque 0161, actual engine torque 0162 and engine reference torque 0163. The torque values behaved consistently with vehicle state: 0% while stopped or coasting, low values during light driving, and high values during hard acceleration/load. The reference torque remained stable at 500 N m.

Removed the Battery and TESTING.Battery sections from the active JSON, and removed the TESTING.4x4 candidates after repeated blank/out-of-range/no-useful-value results. Consolidated sparse boost testing paths by moving Boost2 and Boost4 items into Boost1, and renamed Boost3 to TESTING.VGT because those signals are VGT commanded/actual scaling work.

No formulas were changed in this update.
```


---

# v0.7.13 — Battery restoration, boost comparison cleanup and gear naming tidy

Aligned default file: `default_everest_my25_25_v0_7_13_battery_boost_gear_cleanup.json` / `signalsets/v3/default.json` target

## Update focus

- Built from the uploaded current working `default(6).json` baseline.
- Restored the previously working production `Battery` section from the 74b0e3f/reference `default-2.json` battery-rich file.
- Did **not** restore the dead `TESTING.Battery` / `0142` control-module-voltage candidate.
- Kept `TESTING.4x4` removed.
- Added unit-matching boost comparison signals so generic MAP and F40B can be compared in both kPa and psi.
- Increased F40B polling frequency from 30 seconds to 5 seconds because the latest drive showed F40B lag was likely polling-rate related.
- Kept BARO `0133` and renamed it as a reference value because the drive log showed it working at about 100–101 kPa.
- Removed the fixed F47A rpm candidate, but kept the F47A raw scout.
- Tidied current-gear names so the main and alternate gear indicators sit together cleanly in the app.
- Added raw `70` map handling for the alternate gear signal as `Not in gear / unavailable`.

## Restored production Battery signals

| Signal | PID | Path | Decision |
| --- | --- | --- | --- |
| `EVEREST_AUX_12V_BATTERY_AGE_HOURS_4027` | `224027` | `Battery` | Restored from known-good battery section. |
| `EVEREST_BATTERY_AGE_SCALAR_4027` | `224027` | `Battery` | Restored raw age counter. |
| `EVEREST_BATTERY_SOC_4028_726` | `224028` | `Battery` | Restored state-of-charge value. |
| `EVEREST_BATTERY_CHARGE_4028_DIV255` | `224028` | `Battery` | Restored comparison charge scaling. |
| `EVEREST_AUX_12V_BATTERY_VOLTAGE_402A` | `22402A` | `Battery` | Restored confirmed aux 12V battery voltage and `starterBatteryVoltage` suggested metric. |
| `EVEREST_BATTERY_CURRENT_402B_726` | `22402B` | `Battery` | Restored aux 12V battery current; sign direction still needs confirmation. |
| `EVEREST_ALTERNATOR_CURRENT_0551` | `220551` | `Battery` | Restored alternator current output. |

## Not restored

| Signal / group | Reason |
| --- | --- |
| `TESTING.Battery` | Previous cleanup intentionally removed dead/low-value battery testing clutter. |
| `EVEREST_TEST_CONTROL_MODULE_VOLTAGE_0142` | Dead/unhelpful test item; do not confuse with the working production aux battery voltage. |
| `TESTING.4x4` | Remains removed after repeated out-of-range/no-useful-value results and user direction. |

## Boost changes

| Signal | Change |
| --- | --- |
| `EVEREST_TEST_GENERIC_MAP_010B_GAUGE_KPA_FIXED` | Kept. Fast-updating generic MAP gauge boost in kPa. |
| `EVEREST_TEST_GENERIC_MAP_010B_GAUGE_PSI_FIXED` | Added. Same generic MAP fixed-offset boost estimate in psi for direct comparison against F40B. |
| `EVEREST_TEST_MANIFOLD_PRESSURE_F40B_GAUGE_PSI_FIXED` | Kept. F40B fixed-offset boost in psi. |
| `EVEREST_TEST_MANIFOLD_PRESSURE_F40B_GAUGE_KPA_FIXED` | Added. F40B fixed-offset boost in kPa for direct comparison against generic MAP. |
| `22F40B` command frequency | Changed from `30` to `5` to reduce visible lag. |
| `EVEREST_TEST_BARO_0133_KPA` | Kept and renamed to `TEST BARO reference 0133`; drive log showed expected stable 100–101 kPa. |
| `EVEREST_TEST_LP_TURBO_SPEED_OR_ALTERNATE_TURBO_VALUE_7E0_F47A_CANDIDATE` | Removed because the rpm candidate stayed fixed and looked misleading. |
| `EVEREST_TEST_LP_TURBO_SPEED_OR_ALTERNATE_TURBO_VALUE_7E0_F47A_RAW16_AB` | Kept as `TEST F47A turbo packet raw AB` low-confidence raw scout. |

## Gear naming cleanup

| Signal | Previous name | Updated name |
| --- | --- | --- |
| `EVEREST_GEAR_ENGAGED_7E1_1E1F` | `Transmission gear engaged` | `Transmission current gear` |
| `EVEREST_CURRENT_GEAR_ALT_1E12` | `Transmission current gear alt` | unchanged |

`EVEREST_CURRENT_GEAR_ALT_1E12` also now maps raw `70` as `Not in gear / unavailable`, based on stopped/end-state log evidence where raw `70` appeared and should not display as a literal gear number.

## Safety rule added

Production/root category signals must not be removed during TESTING cleanup unless the exact signal IDs are explicitly listed for removal. Test cleanup should target `TESTING.*` only unless otherwise approved.

## v0.7.13 validation snapshot

| Check | Result |
| --- | ---: |
| Commands in updated default.json | 82 |
| Signals in updated default.json | 117 |
| Root TESTING signals | 47 |
| Duplicate signal IDs | 0 |
| JSON validation | Passed |
| Production battery signals restored | 7 |
| Removed TESTING.Battery items | 0 newly removed; remains absent |
| Removed TESTING.4x4 items | 0 newly removed; remains absent |
| Removed misleading F47A rpm candidate | 1 |
| Added boost comparison signals | 2 |
| Production signal formulas changed | 0 |

## Commit message

```text
Restore Battery signals and tidy boost/gear testing
```

## Extended description

```text
Restored the production Battery section that was accidentally lost during earlier testing cleanup. The restored signals include aux 12V battery voltage, battery SOC, battery current, alternator current, battery age and the secondary 4028 div255 charge comparison. The dead TESTING.Battery 0142 control-module-voltage candidate was not restored.

Tidied boost comparison testing by adding matching psi/kPa fixed-offset boost estimates for generic 010B MAP and Ford F40B manifold pressure. F40B polling was increased from 30 seconds to 5 seconds after drive testing showed the visible delay was likely caused by polling frequency. BARO 0133 was retained and renamed as a reference value after logs showed stable 100–101 kPa behaviour.

Removed the misleading F47A rpm candidate after it stayed fixed, while retaining the raw F47A packet scout. Renamed the main transmission gear signal to Transmission current gear and kept the 7E0/221E12 promoted signal as Transmission current gear alt, including raw 70 mapped as Not in gear / unavailable.

No production formulas were changed.
```

---

# v0.7.14 — Connectable tidy

Aligned default file: `default_everest_my25_25_v0_7_14_connectable_tidy.json` / `signalsets/v3/default.json` target

## Update focus

- Tidied connectable assignments only.
- Removed the fuel-level connectable from the generic SAE fuel-level PID because the Everest-specific `22F42F` fuel remaining signal is preferred.
- Ensured the primary `7E1/221E1F` transmission current gear signal remains the `transmissionGear` connectable.
- Added the intercooler / charge-air temperature signal as a custom connectable.
- Left `GENERIC_MAF_0110` as the active `massAirFlow` connectable because there is still no confirmed Everest-specific enhanced MAF replacement.
- No formulas, commands, paths, or signal IDs were changed.

## Connectable changes

| Signal | PID | Change | Reason |
| --- | --- | --- | --- |
| `GENERIC_FUEL_LEVEL_012F` | `012F` | Removed `fuelTankLevel` suggested metric | Generic SAE fuel level is useful as a comparison, but `EVEREST_FUEL_REMAINING_F42F` is the preferred Everest-specific fuel-level connectable. |
| `EVEREST_GEAR_ENGAGED_7E1_1E1F` | `221E1F` | Ensured `transmissionGear` suggested metric remains active | This is the primary current-gear signal. |
| `EVEREST_AIR_CHARGE_TEMP_051C` | `22051C` | Added custom `intercoolerAirChargeTemperature` suggested metric | Useful charge-air / post-intercooler temperature connectable. |
| `GENERIC_MAF_0110` | `0110` | Kept `massAirFlow` suggested metric | Generic SAE MAF remains the only confirmed airflow connectable until an Everest-specific enhanced MAF is proven. |

## v0.7.14 validation snapshot

| Check | Result |
| --- | ---: |
| Commands in updated default.json | 82 |
| Signals in updated default.json | 117 |
| Root TESTING signals | 47 |
| Duplicate signal IDs | 0 |
| JSON validation | Passed |
| Formula changes | 0 |
| Command/path changes | 0 |
| Signal ID changes | 0 |

## Commit message

```text
Tidy connectable assignments
```

## Extended description

```text
Tidied connectable assignments in the Ford Everest MY25.25 signal pack. Removed the fuelTankLevel connectable from the generic SAE fuel-level PID so the Everest-specific F42F fuel remaining signal remains the preferred fuel-level source. Ensured the primary 7E1/221E1F transmission current gear signal remains the transmissionGear connectable, and added the intercooler air charge temperature signal as a custom intercoolerAirChargeTemperature connectable.

Kept GENERIC_MAF_0110 as the active massAirFlow connectable because no confirmed Everest-specific enhanced MAF replacement is currently available. No formulas, commands, paths, or signal IDs were changed.
```


---

# v0.7.15 — Pelican connectable alignment pass

Aligned default file: `default_everest_my25_25_v0_7_15_pelican_connectable_alignment.json` / `signalsets/v3/default.json` target

## Update focus

- Performed a Pelican connectable alignment pass only.
- Did **not** delete any commands or signals.
- Removed unsupported `suggestedMetric` / connectable assignments from signals that remain fully visible and usable as normal Pelican widgets.
- Kept supported/working connectables including Mass Airflow and Engine Oil Temperature.

## Connectables removed only

| Signal ID | Signal name | Removed suggestedMetric | Reason |
| --- | --- | --- | --- |
| `EVEREST_DPF_FULLNESS_0610` | DPF / exhaust filter fullness | `dpfSootLoad` | Does not appear as a Pelican connectable; signal remains visible under Emissions.DPF. |
| `EVEREST_AIR_CHARGE_TEMP_051C` | Intercooler air charge temperature | `intercoolerAirChargeTemperature` | Does not appear as a Pelican connectable; signal remains visible under Engine. |
| `EVEREST_GEAR_ENGAGED_7E1_1E1F` | Transmission current gear | `transmissionGear` | Does not appear as a Pelican connectable; signal remains visible under Transmission. |

## Connectables retained

| Signal ID | Signal name | suggestedMetric | Reason |
| --- | --- | --- | --- |
| `EVEREST_ENGINE_OIL_TEMP_F45C` | Engine oil temperature | `engineOilTemperature` | Pelican supports engine oil temperature connectables and this one appears correctly. |
| `GENERIC_MAF_0110` | Generic mass air flow rate | `massAirFlow` | Pelican supports Mass Airflow connectables; keep the working generic SAE MAF until an Everest-specific enhanced MAF is confirmed. |
| `EVEREST_TRANS_TEMP_1E1C_DIV16` | Transmission fluid temperature | `transmissionFluidTemperature` | Retained pending continued Pelican behaviour checks. |

## Important rule added

Visible PID signals and Pelican connectables are different things. Unsupported connectable assignments can be removed without removing the signal itself. Any PID in the file can still be shown as a visible widget in Pelican.

## Validation snapshot

| Check | Result |
| --- | ---: |
| Commands | 82 |
| Signals | 117 |
| Root TESTING signals | 47 |
| Duplicate signal IDs | 0 |
| JSON validation | Passed |
| Commands removed | 0 |
| Signals removed | 0 |
| Formulas changed | 0 |
| Paths changed | 0 |
| Signal IDs changed | 0 |
| Connectable assignments removed | 3 |

## Commit message

```text
Align Pelican connectable assignments
```

## Extended description

```text
Performed a Pelican connectable alignment pass without deleting any commands or signals.

Removed unsupported suggestedMetric assignments from DPF fullness, intercooler air charge temperature, and transmission current gear because they do not appear as Pelican connectables. The signals remain fully visible and usable as normal Pelican widgets.

Kept supported connectables including engine oil temperature and generic mass air flow rate. No formulas, paths, signal IDs, commands, or testing signals were changed.
```

---

# v0.7.16 — Pelican schema alignment and packet byte-offset scouts

Aligned default file: `default_everest_my25_25_v0_7_16_pelican_schema_alignment.json` / `signalsets/v3/default.json` target

## Update focus

- Carefully aligned the active file with the Pelican extended PID schema documentation.
- Did **not** delete any commands or signals.
- Did **not** hide existing visible signals.
- Did **not** change production formulas.
- Added Pelican `fmt.bix` byte-offset test decodes for known packet-style responses where later bytes may contain useful data.
- Aligned electric-current units from `ampere` to Pelican's documented `amps` unit.
- Aligned lateral acceleration from generic `scalar` to Pelican's documented `gravity` unit.
- Added missing `unit: scalar` to the promoted alternate current-gear map so every signal format has a unit field.

## Pelican schema notes applied

Pelican's extended PID schema supports:

- top-level `commands`
- optional `signalGroups`
- command properties such as `hdr`, `rax`, `freq`, `tmo`, `fcm1`
- signal properties such as `id`, `path`, `fmt`, `name`, `description`, `hidden`, and `suggestedMetric`
- format properties including `len`, `max`, `unit`, `bix`, `sign`, `min`, `add`, `mul`, `div`, `nullmin`, and `nullmax`

The important new one for this project is `fmt.bix`, which allows decoding values that start later in a packet response instead of only reading the first A/B bytes.

## Added byte-offset TESTING scouts

These were added because earlier Pelican database captures showed that some packet responses had later bytes changing while the first visible A/B decode looked static or incomplete.

### TESTING.Regen

| PID | Added signal | Purpose |
| --- | --- | --- |
| `220610` | `EVEREST_TEST_DPF_FULLNESS_0610_CD_RAW16` | Raw second 16-bit word using `bix: 16`. |
| `220610` | `EVEREST_TEST_DPF_FULLNESS_0610_CD_DIV100` | Second 16-bit word using `raw / 100`; compare with dash DPF display and regen behaviour. |
| `22F48B` | `EVEREST_TEST_DPF_STATUS_F48B_BYTE_C_RAW8` | Byte C raw scout. |
| `22F48B` | `EVEREST_TEST_DPF_STATUS_F48B_CD_RAW16` | Byte C/D raw scout. |
| `22F48B` | `EVEREST_TEST_DPF_STATUS_F48B_BYTE_E_RAW8` | Byte E raw scout. |
| `22F48B` | `EVEREST_TEST_DPF_STATUS_F48B_EF_RAW16` | Byte E/F raw scout. |
| `22F48B` | `EVEREST_TEST_DPF_STATUS_F48B_BYTE_G_RAW8` | Byte G raw scout. |

### TESTING.Boost1

| PID | Added signal | Purpose |
| --- | --- | --- |
| `010170` | `EVEREST_TEST_RANGER_PACKET_70_CD_RAW16` | Byte C/D raw scout for the SAE/Ranger boost-control packet. |
| `010170` | `EVEREST_TEST_RANGER_PACKET_70_EF_RAW16` | Byte E/F raw scout for the SAE/Ranger boost-control packet. |
| `22F47A` | `EVEREST_TEST_LP_TURBO_SPEED_OR_ALTERNATE_TURBO_VALUE_7E0_F47A_CD_RAW16` | Byte C/D raw scout for the F47A turbo packet. |
| `22F47A` | `EVEREST_TEST_LP_TURBO_SPEED_OR_ALTERNATE_TURBO_VALUE_7E0_F47A_EF_RAW16` | Byte E/F raw scout for the F47A turbo packet. |

### TESTING.Fuel

| PID | Added signal | Purpose |
| --- | --- | --- |
| `01016D` | `EVEREST_TEST_RANGER_PACKET_6D_CD_RAW16` | Byte C/D raw scout for the fuel-rail packet. |
| `01016D` | `EVEREST_TEST_RANGER_PACKET_6D_EF_RAW16` | Byte E/F raw scout for the fuel-rail packet. |

### TESTING.Misc

| PID | Added signal | Purpose |
| --- | --- | --- |
| `010169` | `EVEREST_TEST_RANGER_PACKET_69_CD_RAW16` | Byte C/D raw scout for the EGR command/actual packet. |
| `010169` | `EVEREST_TEST_RANGER_PACKET_69_EF_RAW16` | Byte E/F raw scout for the EGR command/actual packet. |

## Unit alignment

| Signal | Previous unit | New unit | Reason |
| --- | --- | --- | --- |
| `EVEREST_BATTERY_CURRENT_402B_726` | `ampere` | `amps` | Pelican documents electric current as `amps`. |
| `EVEREST_ALTERNATOR_CURRENT_0551` | `ampere` | `amps` | Pelican documents electric current as `amps`. |
| `FORD_LAT_G` | `scalar` | `gravity` | Pelican has a dedicated acceleration unit for g-force. |
| `EVEREST_CURRENT_GEAR_ALT_1E12` | missing unit | `scalar` | Pelican format requires a unit; this is a scalar/enumerated gear display. |

## Things deliberately not changed

- No existing commands were removed.
- No existing signals were removed.
- No existing visible signals were hidden.
- No production formulas were changed.
- No new `nullmin` or `nullmax` was added yet; sentinel handling for TCC `1023` and gear raw `70` still needs more confirmation.
- No `tmo` or `fcm1` values were added yet; keep those for commands that prove they need longer timeout or multi-frame handling.
- No `signalGroups` were added yet; optional future tidy-up only.

## Validation snapshot

| Check | Result |
| --- | ---: |
| Commands | 83 |
| Signals | 132 |
| Root TESTING signals | 62 |
| Duplicate signal IDs | 0 |
| JSON validation | Passed |
| Commands removed | 0 |
| Signals removed | 0 |
| Existing formulas changed | 0 |
| New byte-offset scouts added | 15 |
| Unit alignment changes | 4 |

## Commit message

```text
Align Everest PID pack with Pelican schema
```

## Extended description

```text
Aligned the Ford Everest MY25.25 signal pack more closely with Pelican's extended PID schema without deleting any commands or signals.

Added fmt.bix byte-offset TESTING scouts for packet-style responses including DPF 0610, DPF status F48B, SAE boost packet 0170, EGR packet 0169, fuel-rail packet 016D, and F47A turbo packet. These preserve existing first-word decodes while allowing later bytes in multi-field responses to be inspected in Pelican.

Aligned documented units by changing battery and alternator current from ampere to amps, changing lateral acceleration to gravity, and adding a scalar unit to the promoted alternate current gear map.

No production formulas, paths, signal IDs, existing commands, or existing signals were removed. Existing visible PIDs remain visible.
```

---

# v0.7.17 — Dedicated BIX packet resurrection groups

Aligned default file: `default_everest_my25_25_v0_7_17_bix_group_resurrection_pack.json` / `signalsets/v3/default.json` target

## Update focus

- Built from v0.7.16 Pelican schema alignment.
- Did **not** delete any commands, signals, formulas, paths, or IDs.
- Did **not** restore any PID that previously returned `Negative response. Code: Out of range`.
- Reorganised the packet / byte-offset scouts into dedicated screenshot-friendly BIX groups.
- Kept normal TESTING pages cleaner by separating first-word formula testing from multi-byte packet exploration.

## BIX grouping rule

BIX groups are only for PIDs that previously responded with data but were hard to interpret because the first A/B decode was frozen, misleading, low-value, or incomplete.

| Previous result | v0.7.17 decision |
| --- | --- |
| Responded with packet data but A/B was weak or incomplete | Keep / test in a `_BIX` group |
| Responded and first-word value is already useful | Leave in the normal TESTING group |
| Repeated `Negative response. Code: Out of range` | Do not resurrect |

## Dedicated BIX groups

### `TESTING.Regen_BIX`

Purpose: DPF / regen packet fields where useful values may live after the first word.

| PID | Signals |
| --- | --- |
| `220610` | raw AB, byte C/D raw, byte C/D `/100` |
| `22F48B` | byte C, byte C/D, byte E, byte E/F, byte G |

Notes:

- `220610` remains the production DPF fullness / soot-load candidate using first word `raw / 100`.
- Extra `220610` C/D fields are for comparison only, especially because dash display correlation is still being monitored.
- `22F48B` was not re-added because it was out-of-range; it was re-added because previous raw database evidence showed later bytes changed during regen while A/AB stayed visually unhelpful.

### `TESTING.Boost_BIX`

Purpose: SAE / Ranger boost-control packet byte-offset scouting.

| PID | Signals |
| --- | --- |
| `010170` | raw AB, byte C/D raw, byte E/F raw |

Notes:

- `0170` previously responded and moved slightly in A/B, but may contain useful commanded/actual boost fields later in the packet.
- Keep as raw/scout only until behaviour is understood against MAP, BARO, VGT, RPM and load.

### `TESTING.EGR_BIX`

Purpose: EGR command / actual packet byte-offset scouting.

| PID | Signals |
| --- | --- |
| `010169` | raw AB, byte C/D raw, byte E/F raw |

Notes:

- `0169` is alive and changes, but first-word interpretation is not decoded.
- Compare against EGR open percentage `22052E`, RPM, load, fuel rate, exhaust flow and regen state.

### `TESTING.Fuel_BIX`

Purpose: fuel rail / fuel-system packet byte-offset scouting.

| PID | Signals |
| --- | --- |
| `01016D` | raw AB, byte C/D raw, byte E/F raw |

Notes:

- `016D` previously responded as a packet-style fuel-rail candidate.
- Keep as raw/scout only until a useful byte/scale relationship is proven against known fuel pressure and load values.

### `TESTING.Turbo_BIX`

Purpose: turbo packet byte-offset scouting where first-word display was misleading.

| PID | Signals |
| --- | --- |
| `22F47A` | raw AB, byte C/D raw, byte E/F raw |

Notes:

- Earlier F47A rpm-style decode stayed fixed and was not trustworthy as turbo speed.
- The packet is retained only as raw byte-offset scouting; do not interpret it as turbo speed until later bytes show plausible behaviour.

## Not resurrected

The following categories remain out of the active JSON unless a new header/module or strong new evidence appears:

- PIDs repeatedly returning `Negative response. Code: Out of range`.
- Dead TPMS candidates `222813–222818`.
- Dead 7E2 / 4x4 candidates.
- Dead oil-pressure candidates.
- Dead boost/oil 2216xx candidates.
- Dead EGT candidates `22F46B` / `22F46C`.
- Dead turbo candidates `22F479` / `22F48D`.
- Dead regen / charge-air candidates such as `22F411`, `22F453`, `22F476`.

## v0.7.17 validation snapshot

| Check | Result |
| --- | ---: |
| Commands in updated default.json | 83 |
| Signals in updated default.json | 132 |
| Root TESTING signals | 62 |
| Duplicate signal IDs | 0 |
| JSON validation | Passed |
| Commands removed | 0 |
| Signals removed | 0 |
| Existing formulas changed | 0 |
| BIX scout signals moved into dedicated groups | 20 |

## Commit message

```text
Group packet byte-offset scouts into dedicated BIX testing pages
```

## Extended description

```text
Built v0.7.17 from the Pelican-aligned v0.7.16 Ford Everest MY25.25 signal pack.

This update keeps all existing commands and signals, but reorganises packet-style byte-offset scouts into dedicated TESTING.*_BIX groups. The BIX groups cover DPF/regen packet fields, boost packet 0170, EGR packet 0169, fuel rail packet 016D and turbo packet F47A.

Only PIDs that previously responded with data but had weak, frozen, misleading or incomplete first-word decodes were included. PIDs that previously returned Negative response / out of range were not resurrected.

No production formulas, signal IDs, command definitions or visible non-testing signals were removed.
```

---

# v0.7.18 — Shifted packet scout pack

Aligned default file: `default_everest_my25_25_v0_7_18_shifted_packet_scout_pack.json` / `signalsets/v3/default.json` target

## Update focus

- Built directly from the uploaded v0.7.17 working JSON.
- Preserved all production commands, signals, formulas, paths, IDs and connectables.
- Preserved all existing raw and BIX companion signals.
- Added one-byte-shifted packet scouts after the latest drive showed a stable leading support/status byte in several packet-style responses.
- Added individual EGR packet byte scouts and percentage comparisons.
- Strengthened testing notes for TCC desired slip and commanded pressure.
- Did not promote any new signals.
- Did not remove any commands or signals.

## Latest-drive findings incorporated

### DPF fullness `220610`

The production `raw / 100` signal matched the dashboard during normal driving:

| Dashboard display | Logged `220610` |
| ---: | ---: |
| 85% | about 85.1% |
| 86% | about 85.6–85.8% |

This supports the dashboard rounding the underlying hundredths-of-a-percent value. The production signal remains unchanged.

The second 16-bit packet word moved independently at roughly 6.9–10.6 when scaled `/100`. It remains useful but unidentified and stays under `TESTING.Regen_BIX`.

### Packet prefix / shifted-field finding

Latest packets for Mode 01 `0170`, `0169` and `016D` began with a stable `0x07` byte. Existing AB/CD/EF scouts therefore combine the prefix with the first useful data byte.

v0.7.18 adds shifted fields beginning at `bix: 8` and later offsets while preserving the existing scouts.

### Boost / VGT

`0170` and `F470` produced very similar multi-field packets at matching vehicle states. This strongly suggests related boost/VGT command data, but engineering units remain unresolved.

Added shifted raw scouts for:

- `010170`
- `22F470`
- `22F471`

The new `TESTING.VGT_BIX` group separates shifted VGT packet work from the existing alternate-scaling widgets in `TESTING.VGT`.

### EGR packet `0169`

Added individual shifted byte B, C and D raw/percent scouts plus BC/CD 16-bit companions.

Observed byte values produced plausible percentage-style ranges, but commanded/actual/error identities remain unresolved.

### Fuel packet `016D`

Added shifted BC and DE 16-bit fields plus byte F raw.

The two shifted 16-bit values rose strongly with load and closely tracked one another, making them strong requested-versus-actual fuel-pressure candidates. Formula and units remain unresolved.

### Turbo packet `F47A`

Added correctly shifted BC, DE and FG fields.

Latest data showed live paired values in DE and FG, but they do not yet support a turbo-speed interpretation. All new names deliberately use turbo packet/control wording rather than RPM.

### TCC `1E35` and `1E3C`

Latest drive strongly supported:

- `1E35 raw/4` as desired TCC slip.
- `1023 rpm` as a likely open/unlocked converter sentinel.
- `1E3C` as a live TCC apply-pressure or command value.

Descriptions were strengthened, but both signals remain under `TESTING.Transmission`. The `kilopascal` unit for `1E3C` remains provisional.

### MAP comparison

Generic MAP `010B` and Ford `F40B` tracked as near-duplicate 8-bit MAP sources and both reached the apparent 255 kPa absolute ceiling. No production changes were made.

## New packet scouts

| Group | PID | Added scouts |
| --- | --- | ---: |
| `TESTING.Boost_BIX` | `010170` | 4 |
| `TESTING.EGR_BIX` | `010169` | 8 |
| `TESTING.Fuel_BIX` | `01016D` | 3 |
| `TESTING.VGT_BIX` | `22F470` | 4 |
| `TESTING.VGT_BIX` | `22F471` | 4 |
| `TESTING.Turbo_BIX` | `22F47A` | 3 |
| **Total** |  | **26** |

## Things deliberately not changed

- No production signals or formulas changed.
- No production paths changed.
- No commands or signals were removed.
- Existing raw companion signals remain available.
- No unsupported or previously dead PIDs were restored.
- No new `suggestedMetric` values were added or removed.
- No `hidden`, `nullmin`, `nullmax`, `tmo`, `fcm1` or `signalGroups` fields were added.
- TCC `1023` was not hidden or nulled because the sentinel interpretation still needs repeat validation.
- No fuel-pressure, EGR, VGT or turbo packet field was promoted or assigned final engineering units.

## v0.7.18 validation snapshot

| Check | Result |
| --- | ---: |
| Commands in updated default.json | 83 |
| Signals in updated default.json | 158 |
| Root TESTING signals | 88 |
| Duplicate signal IDs | 0 |
| Malformed commands | 0 |
| Non-root testing paths | 0 |
| JSON validation | Passed |
| Commands removed | 0 |
| Signals removed | 0 |
| Production signals modified | 0 |
| New shifted packet scouts | 26 |
| Testing descriptions updated | 2 |
| Formula changes to existing signals | 0 |
| Connectable changes | 0 |

## Commit message

```text
Add shifted packet scouts for Everest PID v0.7.18
```

## Extended description

```text
Built Ford Everest MY25.25 PID pack v0.7.18 directly from the uploaded v0.7.17 source-of-truth files.

The latest drive showed that several packet-style responses begin with a stable support/status byte, causing the existing AB/CD/EF views to be offset from the strongest live fields. This update preserves every existing command and signal while adding one-byte-shifted BIX scouts for boost packet 0170, EGR packet 0169, fuel packet 016D, VGT packets F470/F471 and turbo packet F47A.

Individual byte and percentage scouts were added for the EGR packet, and shifted requested-versus-actual candidates were added for the fuel packet. A dedicated TESTING.VGT_BIX group now holds shifted VGT packet fields while the existing TESTING.VGT scaling comparisons remain untouched.

Testing descriptions for TCC desired slip and commanded pressure were strengthened after the drive showed a coherent transition from the likely 1023 rpm open-converter sentinel to low desired slip as pressure applied.

No production signals, formulas, paths, IDs, commands or connectables were changed or removed. No testing signal was promoted.
```

---

# v0.7.19 — Ranger validation candidates

Aligned default file: `default_everest_my25_25_v0_7_19_ranger_validation_candidates.json` / `signalsets/v3/default.json` target

## Update focus

- Built directly from the complete v0.7.18 shifted packet scout pack.
- Preserved all production and existing TESTING commands and signals.
- Added a Ranger-derived shifter-position candidate using `221E23`.
- Added generic SAE Mode 01 PID `0123` raw and scaled fuel-rail-pressure comparisons.
- Added a `7E0 7E8` transmission-temperature mirror using `221E1C raw/16`.
- Did not restore failed Everest TPMS, `0142`, `0144`, catalyst-temperature or ambient-temperature candidates.
- No signals were promoted.
- No commands or signals were removed.

## Added candidates

### TESTING.Transmission — shifter position `221E23`

Added:

- `EVEREST_TEST_SHIFTER_POSITION_7E0_1E23`

Ranger-derived map:

| Raw | Position |
| ---: | --- |
| 10 | Manual |
| 46 | Drive |
| 50 | Neutral |
| 60 | Reverse |
| 70 | Park |

Purpose:

- Validate selected shifter position independently from current transmission gear.
- Compare against `EVEREST_CURRENT_GEAR_ALT_1E12`.
- Check every position while stationary and using normal safe operating procedures.

### TESTING.Fuel — generic fuel rail pressure `0123`

Added:

- `GENERIC_TEST_FUEL_RAIL_PRESSURE_0123_RAW`
- `GENERIC_TEST_FUEL_RAIL_PRESSURE_0123_KPA`

Formula:

```text
raw * 10 kPa
```

Purpose:

- Compare generic actual rail-pressure behaviour against shifted `016D` BC and DE packet fields.
- Help determine whether `016D` contains requested-versus-actual fuel rail pressure.
- Keep both raw and scaled companions until the relationship is confirmed.

### TESTING.Transmission — `7E0` transmission-temperature mirror

Added:

- `EVEREST_TEST_TRANS_TEMP_7E0_1E1C_DIV16`

Formula:

```text
raw / 16 °C
```

Purpose:

- Compare the Ranger-supported `7E0 7E8` response with the working Everest `7E1 7E9` production transmission-fluid-temperature signal.
- Check whether the values, update rate and availability match.
- This mirror does not replace or modify the production signal.

## Deliberately not added

- Ranger TPMS `2813–2816`: previously rejected on the Everest with negative/out-of-range responses.
- Generic control-module voltage `0142`: previously rejected; production `22402A` is stronger.
- Generic equivalence ratio `0144`: previously rejected on this diesel.
- Generic catalyst temperatures `013C` and `013D`: lower priority than current Ford-specific EGT signals.
- Generic ambient temperature `0146`: unnecessary because the Everest-specific ambient-temperature signal already works.

## v0.7.19 validation snapshot

| Check | Result |
| --- | ---: |
| Commands | 86 |
| Signals | 162 |
| Testing signals | 92 |
| Duplicate signal IDs | 0 |
| Malformed commands | 0 |
| Non-root testing paths | 0 |
| JSON validation | Passed |
| Production signals modified | 0 |
| Removed signals | 0 |
| Added signals | 4 |
| Added commands | 3 |
| Existing formula changes | 0 |
| Connectable changes | 0 |

## Commit message

```text
Add Ranger validation candidates for Everest PID v0.7.19
```

## Extended description

```text
Built Ford Everest MY25.25 PID pack v0.7.19 directly from the complete v0.7.18 source files.

Added a Ranger-derived shifter-position candidate using PID 221E23 with Park, Reverse, Neutral, Drive and Manual mappings. This provides a selected-shifter-state comparison independent from the existing current-gear signals.

Added generic SAE Mode 01 PID 0123 as raw and raw*10 kPa fuel-rail-pressure comparisons. These signals are intended to help identify the shifted 016D BC and DE packet fields as requested-versus-actual rail pressure.

Added a 7E0 7E8 mirror of PID 221E1C using raw/16 °C for comparison against the working Everest 7E1 7E9 transmission-fluid-temperature signal.

No production signals, formulas, paths, IDs, commands or connectables were modified or removed. Failed Everest TPMS, 0142 and 0144 candidates were not restored.
```

---

# v0.7.20 — Negative-response cleanup

Aligned default file: `default_everest_my25_25_v0_7_20_negative_response_cleanup.json` / `signalsets/v3/default.json` target

## Update focus

- Built directly from the uploaded v0.7.19 working JSON and testing log.
- Removed the two Ranger-derived commands that returned persistent `Negative response. Code: Out of range`.
- Kept the working `7E0 7E8 221E1C` transmission-temperature mirror.
- Preserved all production signals and all other existing TESTING signals.
- No new commands or signals were added.
- No connectables were changed.

## Session negative-response review

The latest Pelican session returned NRC `0x31` (`Request Out Of Range`) for the following candidates:

| PID | ECU path | Negative responses | Total responses | Decision |
| --- | --- | ---: | ---: | --- |
| `221E23` | `7E0 7E8` | 4 | 4 | Removed; persistent failure |
| `0123` | `7E0 7E8` | 2 | 2 | Removed; persistent failure |
| `221E1C` | `7E0 7E8` | 4 | 1045 | Kept; transient failure only |

## Removed commands

### `7E0 7E8 221E23`

Removed signal:

- `EVEREST_TEST_SHIFTER_POSITION_7E0_1E23`

Reason:

- Returned `7F 22 31` for every request in the latest session.
- The Ranger shifter-position definition is not supported through this Everest PCM path.
- Keeping it would add screenshot clutter without useful data.

### `7E0 7E8 0123`

Removed signals:

- `GENERIC_TEST_FUEL_RAIL_PRESSURE_0123_RAW`
- `GENERIC_TEST_FUEL_RAIL_PRESSURE_0123_KPA`

Reason:

- Returned `7F 01 31` for every request in the latest session.
- The shifted `016D` packet fields remain the stronger fuel-pressure investigation route.

## Retained command

### `7E0 7E8 221E1C`

Retained signal:

- `EVEREST_TEST_TRANS_TEMP_7E0_1E1C_DIV16`

Reason:

- Approximately 99.6% of requests returned valid data.
- Only 4 transient out-of-range responses occurred from 1045 requests.
- This remains useful for comparing value, update rate and availability against the working `7E1 7E9` production transmission-temperature signal.

The signal description was updated to record this evidence.

## Deliberately unchanged

- No production commands or signals changed.
- No production formulas changed.
- No production paths changed.
- No connectables changed.
- No existing shifted packet scouts were removed.
- No previously shelved TPMS, `0142`, `0144`, catalyst-temperature or other failed candidates were restored.
- No new signals were promoted.

## v0.7.20 validation snapshot

| Check | Result |
| --- | ---: |
| Commands | 84 |
| Signals | 159 |
| Testing signals | 89 |
| Commands removed | 2 |
| Signals removed | 3 |
| Signals added | 0 |
| Duplicate signal IDs | 0 |
| Malformed commands | 0 |
| Non-root TESTING paths | 0 |
| Production signals modified | 0 |
| Testing descriptions updated | 1 |
| JSON validation | Passed |
| Connectable changes | 0 |

## Commit message

```text
Remove unsupported Ranger candidates for Everest PID v0.7.20
```

## Extended description

```text
Built Ford Everest MY25.25 PID pack v0.7.20 directly from the uploaded v0.7.19 source-of-truth files.

Removed Ranger-derived PID 221E23 shifter position after the latest Pelican session returned NRC 0x31 Request Out Of Range for every request.

Removed generic Mode 01 PID 0123 fuel rail pressure after every request also returned NRC 0x31. The shifted 016D packet fields remain active as the stronger fuel-pressure investigation path.

Retained the Ranger-derived 7E0 7E8 PID 221E1C transmission-temperature mirror because approximately 99.6% of its requests returned valid data. Its testing description now records the small number of transient negative responses and the reason it remains useful.

No production signals, formulas, paths, IDs, commands or connectables were modified. No new signals were added or promoted.
```

---

# v0.7.21 — Validation-drive TESTING cleanup and promotions

Aligned default file: `default_everest_my25_25_v0_7_21_testing_evidence_cleanup.json` / `signalsets/v3/default.json` target

## Update focus

- Analysed 48,811 Pelican database records and 42 supporting screenshots from a roughly 98-minute mixed-speed validation drive.
- Kept all unrelated production signals unchanged.
- Promoted generic fuel rate `015E raw/20` into `Fuel.Generic` after plausible live behaviour and near-perfect correlation with the independent `019D` fuel-rate packet.
- Promoted TCC desired slip `221E35 raw/4` into `Transmission` after repeated open/unlocked sentinel and locked-state validation.
- Removed the failed `7E0 7E8 221E1C` transmission-temperature mirror after responder-aware analysis proved that all five mirror requests returned NRC `0x31`.
- Removed `F451` because value `4` was observed both during active regeneration and throughout normal/non-regen driving and therefore does not identify active regen.
- Pruned rejected exhaust-flow, fuel-rate, VGT and packet-alignment variants while retaining useful raw companions and correctly aligned live fields.
- Replaced the misleading combined F471 word scouts with individual byte-B, byte-C and final-state scouts.

## Important responder-aware correction

The previous v0.7.20 log treated the `7E0` transmission-temperature mirror as approximately 99.6% responsive. That figure combined two commands sharing the same text `221E1C`:

| Route | Valid | NRC 0x31 | Decision |
| --- | ---: | ---: | --- |
| Production `7E1 7E9 221E1C` | 2,848 | 0 | Preserve unchanged |
| TESTING mirror `7E0 7E8 221E1C` | 0 | 5 | Removed |

## Promotions

| Signal | New path | Evidence |
| --- | --- | --- |
| `GENERIC_FUEL_RATE_015E` | `Fuel.Generic` | 20/20 valid; plausible 12.7-19.7 L/h-style drive values; correlation with 019D `r=0.99999`. |
| `EVEREST_TCC_DESIRED_SLIP_1E35` | `Transmission` | Earlier open/unlocked 1023 rpm sentinel followed by 0-5 rpm near lock; latest drive 22/22 valid at 0-5 rpm during highway operation. |

The TCC raw companion remains under `TESTING.Transmission`. The `1E3C` candidate remains testing-only and was relabelled as an apply-command scalar because kilopascal scaling is not proven.

## Removed testing clutter

- Complete failed commands/signals: `7E0 221E1C` mirror and `22F451`.
- Exhaust flow: removed `019E /5`, `/10` and `/50`; retained raw and the best `/20` candidate.
- Fuel-rate alternate: removed all decoded `019D` divisor variants; retained raw only.
- VGT: removed misaligned F470/F471 percent and divisor candidates.
- F470: retained shifted BC, DE and final-state word; removed all-zero shifted FG.
- F471: replaced combined/misaligned shifted fields with individual byte B, byte C and final-state byte F.
- F47A: retained the raw companion plus shifted DE and FG; removed overlapping CD/EF and all-zero shifted BC.
- F48B: retained individual C/E/G bytes; removed redundant CD/EF words whose low bytes stayed zero.
- 0170 and 016D: retained raw companions and useful shifted fields; removed overlapping unshifted CD/EF fields and the all-zero 0170 shifted FG field.
- Boost comparison: removed duplicate F40B calculated-gauge widgets; retained generic 010B gauge calculations and both production absolute-MAP signals.

## Retained high-value TESTING work

- `220610` secondary CD word and /100 view.
- `019E` raw and /20 candidate.
- `1E3C` TCC apply-command raw/scalar.
- `0170` shifted BC, DE and final state.
- `F470` shifted BC, DE and final state.
- `F471` individual B/C paired bytes and final state.
- `F47A` shifted DE and FG.
- `016D` shifted BC/DE pair and byte F.
- `0169` EGR packet scouts pending a better-sampled drive.
- `F48B` C/E/G bytes pending a confirmed active regeneration.

## Validation-drive evidence

| Candidate | Evidence | Result |
| --- | --- | --- |
| `019E` vs generic MAF | 79 paired samples, `r=0.959`; /20 median approximately 0.92× MAF | Keep raw + /20 only |
| F470 shifted BC/DE | 70 responses, `r=0.983` | Keep paired shifted fields |
| F471 bytes B/C | 70 responses, `r=0.993`, median difference 0.5 | Use individual byte scouts |
| F47A shifted DE/FG | 71 responses, `r=0.976` | Keep both shifted fields |
| 016D shifted BC/DE | 5 responses, `r=0.9991`, median difference 10 | Strong requested/actual candidate; keep testing |
| F48B | 28 responses, only three non-regen states | Keep individual bytes pending regen |

## Privacy note

The Pelican database contains the full VIN. Keep the database private or sanitise it before public sharing. No VIN or other full vehicle identifier is reproduced in this log.

## Commit message

```text
Clean and promote validated Everest signals for v0.7.21
```

## Extended description

```text
Built Ford Everest MY25.25 PID pack v0.7.21 directly from the supplied v0.7.20 JSON and testing log.

Promoted confirmed generic fuel rate 015E and TCC desired slip 1E35, corrected the responder-specific assessment of the failed 7E0 transmission-temperature mirror, removed the non-discriminating F451 regen candidate, and pruned misleading or redundant TESTING scaling variants.

Preserved all unrelated production signals, stable formulas, connectables and battery signals. Retained raw companions for useful unresolved packet families and replaced combined F471 word decoding with correctly aligned individual byte scouts.
```

---

# v0.7.22 — Confirmed regeneration and packet-state breakthrough

Aligned default file: `default_everest_my25_25_v0_7_22_regen_packet_breakthrough.json` / `signalsets/v3/default.json` target

## Update focus

- Analysed 29,216 Pelican responses and 80 timestamped screenshots from a roughly 59-minute drive.
- Captured a confirmed automatic DPF regeneration from sustained onset through completion.
- Identified F48B byte D as the strongest active-regeneration state candidate to date.
- Corrected three misaligned EGT/exhaust packet decodes and replaced them with shifted packet scouts.
- Restored the second 019D word after it diverged specifically during active regeneration.
- Preserved all established production signals and formulas.

## Confirmed regeneration timeline

| Time | Evidence |
| --- | --- |
| 08:24:09 | 220610 reached 73.53%; sustained decline began shortly afterward. |
| 08:31:54 | Vehicle stationary; 220610 at 52.06% and secondary word at 4.12. |
| 08:32-08:42 | Elevated-flow stationary burn continued. |
| 08:42:43 | F48B changed from active-burn to post-burn state. |
| 08:43:16 | Production EGR reopened from 0% to approximately 34%. |
| 08:43:24 | Distance since DPF regen reset from 213.3 km to 0 km. |
| 08:43:38 | 220610 reached 25.90%. |

Overall 220610 reduction was 47.63 percentage points across approximately 19.5 minutes, averaging 2.45 percentage points per minute.

## F48B active-regeneration evidence

| State | C | D | E | G |
| --- | ---: | ---: | ---: | ---: |
| Previous normal driving | 243-244 | 0 | 232-233 | 131 |
| Active regeneration | 218→166 | 1 | 1 | 139 |
| Immediately post-regeneration | 121→112 | 0 | 180 | 107 |

Byte D returned to zero approximately 41 seconds before the distance-since-regen reset. It is added as both a raw scout and an `offon` testing candidate. It remains under `TESTING.Regen_BIX` pending confirmation in one more regeneration.

Byte C correlated strongly with production DPF fullness during the active burn (`r=0.994`) and is more likely another scaled soot/load model than a simple status value.

## DPF model clarification

- Production `EVEREST_DPF_FULLNESS_0610` remains valid and unchanged at raw/100.
- It tracked the dashboard closely early in the burn but diverged in the final phase; the dashboard reached 15% while 220610 was still approximately 28%.
- The secondary CD word fell from approximately 10-11 before regen to 0.11 immediately before completion.
- CD /100 is therefore retained as an unknown scalar rather than incorrectly labelled percent.
- Distance since regen reset to 0 km, independently confirming completion.

## 019D fuel packet

The two 019D words were normally identical. During active regeneration one captured response became:

```text
00 2C | 00 45
44    | 69
```

The second CD word is restored as a raw scout. It may expose base versus total fuel rate, requested versus actual rate, or regeneration-related additional fuelling. No engineering scaling is assigned.

## EGT / exhaust packet corrections

### F46A

- Removed the misaligned raw*0.1-40 °C candidate.
- Preserved the raw companion.
- Response begins with a stable 0x03 support byte; exact byte meanings remain unresolved.

### F46D

- Removed the misaligned raw*0.1-40 °C candidate.
- Added shifted BC and DE raw scouts.
- Added shifted BC/DE raw/32 temperature comparisons.
- BC and DE correlated at `r=0.9995` and produced approximately 201-226 °C under /32.
- Added byte F as a state/reference scout.

### F478

- Removed the misaligned raw*0.1-40 °C candidate.
- Added shifted BC and DE raw scouts plus /32 comparisons.
- BC produced approximately 122-128 °C and DE approximately 202-207 °C under /32.
- Shifted FG duplicated DE in every sample and was deliberately not added.

## Other useful confirmation

- Production EGR was 0% during active regeneration and reopened to approximately 34% after completion.
- `019E /20` rose to approximately 30-33 during the elevated-flow stationary burn and remains the best exhaust-flow scaling candidate.
- F470/F471 final states changed 13 seconds before the F48B completion transition; descriptions now record this correlation without treating them as confirmed regen flags.
- TCC desired slip again showed the 1023 rpm open/unlocked sentinel while stationary, supporting its v0.7.21 production promotion.

## Removed signals

- `EVEREST_TEST_EGT11_7E0_F46A_CANDIDATE`
- `EVEREST_TEST_EGT14_DPF_OUTLET_7E0_F46D_CANDIDATE`
- `EVEREST_TEST_EXHAUST_MULTI_EGT_F478_CANDIDATE_C`

All three were alive but decoded from a word containing the packet support byte and were therefore invalid as temperature widgets. Their raw companions remain.

## Added signals

- F48B byte D raw and active/on-off candidate.
- 019D second/CD raw word.
- F46D shifted BC/DE raw, shifted BC/DE /32 and byte F.
- F478 shifted BC/DE raw and shifted BC/DE /32.

## Privacy note

The source Pelican database contains the full VIN. Keep it private or sanitise it before public sharing. No VIN or full vehicle identifier is reproduced here.

## Commit message

```text
Add confirmed regen packet scouts for Everest PID v0.7.22
```

## Extended description

```text
Built Ford Everest MY25.25 PID pack v0.7.22 directly from the supplied v0.7.21 default.json, testing log and README.

Recorded a confirmed automatic DPF regeneration, added the high-confidence F48B byte-D active-regeneration candidate, restored the second 019D word after regen-specific divergence, and replaced three misaligned EGT candidates with correctly shifted packet scouts.

Preserved all established production formulas, paths, IDs, connectables and battery signals. No new regen or EGT signal was promoted prematurely; the strongest new candidates remain clearly separated under TESTING pending repeat confirmation.
```

---

# v0.7.23 — Decoded packet promotions and normal-drive cleanup

Aligned default file: `default_everest_my25_25_v0_7_23_decoded_packet_promotions.json` / `signalsets/v3/default.json` target

## Update focus

- Analysed 16,875 Pelican responses and 40 supporting screenshots from a complete 34.5-minute, 19.4 km normal/non-regeneration drive.
- Used the normal drive as the control comparison for the earlier confirmed active regeneration.
- Matched the retained 019D, 016D, 0170 and 019E packets to their defined SAE J1979 layouts and engineering scales.
- Promoted decoded production signals while preserving their raw testing companions.
- Corrected F470/F471 descriptions after normal driving proved their state transitions are ordinary turbo open/closed-loop behaviour rather than regeneration-specific events.
- Promoted the confirmed BARO response and TCC apply-command scalar.

## Complete-drive summary

| Evidence | Result |
| --- | ---: |
| Pelican responses | 16,875 |
| Distinct commands | 58 |
| Session duration | Approximately 34.5 minutes |
| Odometer movement | 19.4 km |
| DPF fullness | 44.70% to 48.06% |
| Coolant temperature | 35-98 °C |
| Engine oil temperature | 19-99 °C |
| Active regeneration | No |
| Persistent negative responses from retained TESTING commands | 0 |

The DPF value rose normally throughout the drive. This was therefore a useful non-regeneration control session rather than another burn.

## Promotions and decoded production signals

### SAE 019D dual fuel rate

- Added alternate engine fuel rate: raw/50 grams per second.
- Added vehicle fuel rate: shifted CD raw/50 grams per second.
- All 15 normal-drive samples had identical AB/CD words.
- The earlier active-regeneration divergence of AB=44 and CD=69 is now understood as two defined, independently useful rates rather than packet noise.
- Both raw companions remain under `TESTING.Fuel`.

### SAE 016D fuel-pressure control

- Added commanded fuel rail pressure A: shifted BC raw*10 kPa.
- Added actual fuel rail pressure A: shifted DE raw*10 kPa.
- Added fuel rail temperature A: byte F raw-40 °C.
- Across 27 combined samples, commanded and actual pressure correlated at r=0.99958 with a median absolute difference of approximately 0.25 MPa.
- Latest examples ranged from approximately 30 MPa at light/idle operation to approximately 203 MPa under load.
- Observed temperature bytes decode to a plausible 15-33 °C.
- All raw companions remain under `TESTING.Fuel_BIX`.

### SAE 0170 boost-pressure control

- Added commanded boost pressure A: shifted BC raw/32 kPa.
- Added actual boost pressure A: shifted DE raw/32 kPa.
- Added boost-pressure A open/closed-loop/fault status from the low two bits of byte J.
- Actual pressure tracked generic MAP in time-near comparisons while providing 16-bit range beyond the 255 kPa PID 010B ceiling.
- Values 5, 6 and 10 in the retained final raw word are combinations of control-status bits, not regeneration states.
- Raw packet companions remain under `TESTING.Boost_BIX`.

### Ford F471 VGT control

- Added commanded VGT position: byte B raw*100/255 percent.
- Added actual VGT position: byte C raw*100/255 percent.
- Added VGT open/closed-loop/fault status from the low two bits of byte F.
- Across 81 combined responses, the command and actual bytes moved coherently with plausible control transients.
- Raw companions remain under `TESTING.VGT` and `TESTING.VGT_BIX`.

### SAE 019E engine exhaust flow

- Replaced the testing raw/20 scalar with the defined raw/5 kilograms-per-hour production signal.
- The previous /20 display looked close to MAF because it approximated a mass-flow unit conversion; it was not the defined engineering scale.
- The raw companion remains under `TESTING.Regen`.

### TCC apply command

- Promoted 1E3C as a production `Transmission` scalar after repeated coherent transitions with desired slip and converter lockup.
- The exact hydraulic pressure scale remains unknown, so no pressure unit is assigned.
- The raw companion remains under `TESTING.Transmission`.

### Generic BARO

- Replaced `EVEREST_TEST_BARO_0133_KPA` with production `GENERIC_BAROMETRIC_PRESSURE_0133`.
- The database contained 16/16 valid responses at 100 kPa even though the former testing widget displayed blank.
- The command, response and formula were valid; the blank was a Pelican display/widget-association issue rather than an unsupported PID.
- Poll interval changed from 10 to 5 seconds to populate the fresh widget more promptly.

## Corrected testing interpretation

- F470 is now described as a Ford boost-control mirror of standardized 0170, not a VGT-percentage packet.
- F470 states 5, 6 and 10 occurred during normal driving and are not regeneration-specific.
- F471 final states 1 and 2 are ordinary open-loop and closed-loop VGT control.
- F47A remains alive but undecoded; no formula or production promotion was assigned.
- F48B and shifted EGT candidates were not polled in this drive and remain unchanged.

## Odometer confirmation

The production 01A6 raw/10 odometer increased from 28,807.0 km to 28,826.4 km over the 19.4 km session. Its description is updated from a working candidate to a confirmed incremental odometer.

## Privacy note

The source Pelican database contains the full VIN and the screenshots contain identifiable route information. Keep the archive private or sanitise it before public sharing. No VIN, registration or full vehicle identifier is reproduced here.

## Validation summary

| Check | Result |
| --- | ---: |
| Commands | 82 |
| Signals | 143 |
| Testing signals | 57 |
| Duplicate signal IDs | 0 |
| Malformed commands | 0 |
| Non-root TESTING paths | 0 |
| JSON validation | Passed |
| Commands added | 0 |
| Commands removed | 0 |
| Signal IDs added | 14 |
| Signal IDs removed/replaced | 3 |
| Net signal change | +11 |
| Production signals modified | Odometer description only; no existing production formula, path or ID changed |
| Removed signals | 3 superseded TESTING IDs, each replaced by a production equivalent |
| Added signals | 14 production signal IDs |
| Path changes | BARO to Engine.Generic; TCC scalar to Transmission; exhaust flow to Fuel.Generic |
| Formula changes | New decoded formulas only; no existing formula changed |
| Existing production formulas changed | 0 |
| Connectable changes | 0 |

## Removed or replaced signal IDs

- `EVEREST_TEST_EXHAUST_FLOW_019E_DIV20_CANDIDATE` — replaced by correctly scaled production exhaust flow.
- `EVEREST_TEST_TCC_COMMANDED_PRESSURE_7E1_1E3C_CANDIDATE` — replaced by production TCC apply-command scalar without unproven pressure wording.
- `EVEREST_TEST_BARO_0133_KPA` — replaced by confirmed generic production BARO.

## Commit message

```text
Promote decoded Everest packet signals for v0.7.23
```

## Extended description

```text
Built Ford Everest MY25.25 PID pack v0.7.23 directly from the supplied v0.7.22 default.json and testing log.

Promoted decoded SAE 019D fuel rates, 016D fuel-rail command/actual/temperature, 0170 boost command/actual/status, 019E engine exhaust flow, Ford F471 VGT command/actual/status, the TCC apply-command scalar and generic barometric pressure.

Preserved all raw packet companions, established production signals, battery items and connectables. Corrected F470/F471 state descriptions after the normal drive proved their transitions are ordinary turbo-control states rather than regeneration-specific indicators. Replaced only three superseded testing IDs and made no existing production formula changes.
```

---

# v0.7.24 — Standard packet mirrors, DPF pressure and testing cleanup

Aligned default file: `default_everest_my25_25_v0_7_24_standard_mirror_promotions.json` / `signalsets/v3/default.json` target

## Update focus

- Analysed the complete latest Pelican drive: 39,628 database rows, 29,657 positive payloads, approximately 83 minutes, 24.1 km and 96 supporting screenshots.
- Compared the latest normal/non-regeneration session with the previously retained confirmed active-regeneration capture.
- Proved that Ford DIDs F46A, F46D, F478, F47A and F48B use the corresponding SAE Mode 01 packet layouts 6A, 6D, 78, 7A and 8B.
- Promoted 12 evidence-backed production signals.
- Removed invalid temperature scalings, overlapping packet scouts and the disproved F48B byte-D active-regeneration candidate.
- Preserved useful raw companions under accurate TESTING paths and names.

## Complete-drive summary

| Evidence | Result |
| --- | ---: |
| Pelican database rows | 39,628 |
| Positive payloads | 29,657 |
| Session duration | Approximately 83 minutes |
| Odometer movement | 24.1 km |
| DPF fullness | 48.07% to 57.39% |
| DPF direction | 95 increases, 0 decreases |
| Active regeneration | No |
| Retained custom-PID failures | 0 |
| Supporting screenshots | 96 |

The DPF value rose throughout the retained period, so this was a strong normal-driving control session rather than another confirmed burn.

## Promotions and decoded production signals

### SAE 0169 EGR command, actual and error

- Added commanded EGR A: byte B raw*100/255 percent.
- Added actual EGR A: byte C raw*100/255 percent.
- Added EGR A error: byte D raw*100/128-100 percent.
- Latest command/actual correlation was r=0.9702.
- Reported error agreed with error calculated from commanded and actual EGR with a median residual of 0.38 percentage points and maximum residual of 0.67.
- Retained only the raw packet and individual B/C/D companions; redundant percentage and overlapping word scouts were removed.

### Ford F46A diesel intake-airflow control

- Added commanded intake-airflow control A: byte B raw*100/255 percent.
- Added relative intake-airflow position A: byte C raw*100/255 percent.
- Latest command/position correlation was r=0.9962 across 23 samples.
- The earlier regeneration samples also moved coherently.
- Removed the misleading EGT identity and replaced it with aligned raw B/C companions.

### Ford F46D fuel-pressure mirror

- Confirmed F46D mirrors the decoded SAE 016D fuel-pressure-control packet:
  - shifted BC = commanded rail pressure, raw*10 kPa;
  - shifted DE = actual rail pressure, raw*10 kPa;
  - byte F = fuel rail temperature, raw-40 °C.
- Latest command/actual correlation was r=0.9981 and retained active-session correlation was r=0.99949.
- No duplicate production widgets were added because the standardized 016D production signals already expose the same quantities.
- Invalid /32 exhaust-temperature candidates were removed and the four raw mirror companions moved to `TESTING.Fuel_BIX`.

### Ford F478 exhaust temperatures

- Added bank 1 sensor 1: shifted BC raw/10-40 °C.
- Added bank 1 sensor 2: shifted DE raw/10-40 °C.
- Latest normal-drive ranges were 125.6-317.0 °C for sensor 1 and 125.6-350.1 °C for sensor 2.
- During the retained active regeneration, sensor 2 reached approximately 606-623 °C.
- The sensor-3 word exactly duplicated sensor 2 in every retained sample, so no redundant third widget was added.
- Invalid /32 candidates were removed.

### Ford F47A DPF pressures

- Added DPF bank 1 inlet pressure: shifted DE raw/100 kPa.
- Added DPF bank 1 outlet pressure: shifted FG raw/100 kPa.
- Latest normal-driving ranges were 1.0-9.1 kPa inlet and 0.1-2.3 kPa outlet.
- During the retained active regeneration, inlet reached 44.0 kPa and outlet reached 12.9 kPa.
- The calculated pressure drop remained positive and coherent with load.
- Removed the frozen/misleading turbo raw-AB interpretation and relabelled the two live raw companions under `TESTING.Regen_BIX`.

### Ford F48B diesel aftertreatment data

- Added normalized regeneration trigger: byte C raw*100/255 percent.
- Added average regeneration interval: bytes D/E in minutes.
- Added average regeneration distance: bytes F/G in kilometres.
- Latest normal-driving values were 87.06-95.29%, 203-214 minutes and 117-120 km.
- During the retained active session, trigger fell from 85.49% to 43.92%.
- The former byte-D `0/1` candidate was false: byte D was the high byte of the 16-bit D/E average-time word. It and the isolated E/G byte widgets were removed.
- The actual regeneration-status byte remains zero in both captured active and normal sessions, so no active-regeneration state was promoted.

## DPF dashboard comparison

The production 220610 internal fullness estimate remains valid but is not the dashboard's exact modelled value. Latest reference screenshots showed:

- 52.90% in 220610 when the dashboard displayed 55%.
- 57.39% in 220610 when the dashboard displayed 65%.

The production description now records this normal-driving divergence while retaining the earlier full-regeneration validation.

## Existing promotions reconfirmed

- F471 VGT commanded/actual position: latest correlation r=0.999686.
- 016D rail-pressure command/actual: latest correlation r=0.99251 over approximately 24.72-220.18 MPa.
- 019E exhaust flow versus generic MAF: latest correlation r=0.9627 with median converted ratio 0.987.
- TCC desired slip and apply command remained coherent in the supporting screenshots, including the 1023 rpm open-converter sentinel.

## Retained unresolved work

- 220610 second word remains a useful secondary soot-model candidate but is not ready for production.
- F48B did not reveal a working active-regeneration status flag; another confirmed burn remains valuable.
- F470 remains a testing mirror of the standardized 0170 boost-control packet.
- Raw mirror companions remain available where they still provide equivalence or diagnostic value.

## Privacy note

The Pelican database contains the full VIN and the screenshots contain identifiable route information. Keep the archive private or sanitise it before public sharing. No VIN, registration or full vehicle identifier is reproduced here.

## Validation summary

| Check | Result |
| --- | ---: |
| Commands | 82 |
| Signals | 142 |
| Testing signals | 44 |
| Production signals | 98 |
| Duplicate signal IDs | 0 |
| Malformed commands | 0 |
| Malformed signals | 0 |
| Non-root TESTING paths | 0 |
| JSON validation | Passed |
| Commands added | 0 |
| Commands removed | 0 |
| Signal IDs added | 22 |
| Signal IDs removed/replaced | 23 |
| Net signal change | -1 |
| New production signals | 12 |
| Existing production formulas changed | 0 |
| Existing production description changes | DPF fullness only |
| Connectable changes | 0 |

## Removed or replaced testing interpretations

- 0169 duplicate percentage and overlapping-word scouts were replaced by exact production EGR command/actual/error fields.
- F46A's false EGT identity was replaced by aligned intake-airflow raw companions and production signals.
- F46D's false exhaust-temperature identity and /32 candidates were replaced by fuel-pressure mirror companions.
- F478 /32 temperature candidates were removed in favour of the correct raw/10-40 °C production decode.
- F47A turbo wording and frozen first word were removed in favour of DPF inlet/outlet pressure.
- F48B byte D active-regeneration, byte E and byte G interpretations were removed in favour of complete D/E and F/G words.

## Commit message

```text
Promote decoded standard packet mirrors for Everest PID v0.7.24
```

## Extended description

```text
Built Ford Everest MY25.25 PID pack v0.7.24 directly from the supplied v0.7.23 default.json, testing log and README.

Promoted decoded EGR command/actual/error, diesel intake-airflow command/position, exhaust-gas temperatures, DPF inlet/outlet pressure, normalized regeneration trigger, average regeneration interval and average regeneration distance.

Corrected Ford F46A, F46D, F478, F47A and F48B after complete-drive and retained-regeneration evidence proved they mirror standardized SAE packet layouts. Removed invalid /32 temperature candidates, redundant overlapping scouts and the disproved F48B byte-D active-regeneration widget while preserving useful raw companions under accurate TESTING paths.

No commands, battery items, connectables or existing production formulas were changed.
```

