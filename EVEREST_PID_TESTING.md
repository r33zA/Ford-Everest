# Ford Everest MY25.25 PID Testing Log

https://github.com/r33zA/Ford-Everest

Version: 2026-05-22 v0.7.2 testing refinement  
Aligned default file: `default_everest_my25_25_v0_6_4_dpf_promoted.json` / `signalsets/v3/default.json` target  
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
| `fuelTankLevel` | `EVEREST_GENERIC_FUEL_LEVEL_012F` | Generic fuel level | `012F` |
| `massAirFlow` | `EVEREST_GENERIC_MAF_0110` | Air flow rate from mass air flow sensor | `0110` |
| `odometer` | `EVEREST_ODOMETER_01A6` | Odometer | `01A6` |
| `speed` | `EVEREST_SPEED_FORD_EXTENDED_F40D` | Vehicle speed Ford extended raw | `22F40D` |
| `speed` | `EVEREST_SPEED_FORD_EXTENDED_F40D_CORRECTED` | Vehicle speed Ford extended corrected | `22F40D` |
| `starterBatteryVoltage` | `EVEREST_AUX_12V_BATTERY_VOLTAGE_402A` | Aux 12V battery voltage | `22402A` |
| `throttlePosition` | `EVEREST_ACCEL_PEDAL_032B` | Accelerator pedal position | `22032B` |

## Confirmed / working and currently understood

| Group | PID / cmd | ECU | Signal | Status | Decision / notes |
| --- | --- | --- | --- | --- | --- |
| DPF | `220610` | `7E0→7E8` | DPF / exhaust filter fullness | Works | Active; formula raw/100 percent; matched dash regen/fullness behaviour. |
| DPF | `220614` | `7E0→7E8` | Distance since DPF regen | Works | Active; formula raw/10 km; reset at regen completion. |
| Transmission | `221E1C div16` | `7E1→7E9` | Transmission fluid temperature Everest div16 | Works / chosen | Preferred current Everest-specific scaling; active transmissionFluidTemperature connectable. |
| Transmission | `221E1C 5/72` | `7E1→7E9` | Transmission fluid temperature 5/72 compare | Comparison / legacy | Retained for validation only; no suggestedMetric. |
| Transmission | `221E1F` | `7E1→7E9` | Transmission gear engaged | Works | Active as transmissionGear. |
| Emissions | `22052E` | `7E0→7E8` | EGR open percentage | Works | Active. |
| Emissions | `220569` | `7E0→7E8` | EGT pre-turbo | Works / plausible | Active. |
| Fuel / range | `22F42F` | `7E0→7E8` | Fuel remaining | Works | Active as fuelTankLevel. |
| Fuel / range | `012F` | `7E0→7E8` | Generic fuel level | Works | Backup/comparison fuelTankLevel. |
| Fuel / range | `224195` | `7E0→7E8` | Distance to empty, displayed | Works | Matched dash; active as fuelRange. |
| Movement | `01A6` | `7E0→7E8` | Odometer | Works | Active as odometer. |
| Movement | `22F40D` | `7E0→7E8` | Vehicle speed Ford extended raw/corrected | Works | Corrected signal retained as practical dash-matching speed. |
| Control | `22032B` | `7E0→7E8` | Accelerator pedal position | Works | Practical throttlePosition connectable. |
| Engine | `22F404` | `7E0→7E8` | Engine load | Works | Active as engineLoad. |
| Engine | `22F405` | `7E0→7E8` | Coolant temperature | Works | Active as engineCoolantTemperature. |
| Engine | `22F40C` | `7E0→7E8` | Engine speed | Works | Active as engineSpeed. |
| Engine | `22F45C` | `7E0→7E8` | Engine oil temperature | Works | Active as engineOilTemperature; continue sanity checking long term. |
| AdBlue | `220487` | `7E0→7E8` | AdBlue tank level | Works | Best AdBlue quantity signal. |
| Battery | `22402A` | `726→72E` | Aux 12V battery voltage | Works | Active as starterBatteryVoltage. |

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
| `220440` | `7E0→7E8` | Existing survivor; possible regen bitfield, previously valid zero. |
| `22F48B` | `7E0→7E8` | Existing survivor; possible DPF/regen status byte, previously `127` / `32512`. |
| `22F411` | `7E0→7E8` | Regen commanded candidate. |
| `22F412` | `7E0→7E8` | Regen inhibit candidate. |
| `22F413` | `7E0→7E8` | Regen request candidate. |
| `22F450` | `7E0→7E8` | Common Ford active regen candidate. |
| `22F451` | `7E0→7E8` | Regen request candidate. |
| `22F453` | `7E0→7E8` | DPF status bitfield candidate. |
| `22F87C` | `7E0→7E8` | Alternate DPF active candidate. |

### TESTING.EGT

Purpose: identify additional exhaust temperature sensors and improve regen thermal visibility.

| PID | Header | Purpose |
| --- | --- | --- |
| `22F478` | `7E0→7E8` | Existing survivor; packet-like multi-EGT candidate. |
| `22F463` | `7E0→7E8` | Existing survivor; possible EGRT / EGR manifold temp, low confidence. |
| `22F46A` | `7E0→7E8` | EGT11 candidate. |
| `22F46B` | `7E0→7E8` | EGT12 candidate. |
| `22F46C` | `7E0→7E8` | EGT13 / DPF inlet candidate. |
| `22F46D` | `7E0→7E8` | EGT14 / DPF outlet candidate. |

### TESTING.Boost

Purpose: identify boost, VGT, turbo-bypass, and bi-turbo behaviour.

| PID | Header | Purpose |
| --- | --- | --- |
| `220210` | `7E0→7E8` | Extended MAF candidate; compare against generic `0110`. |
| `220233` | `7E0→7E8` | BARO candidate. |
| `220247` | `7E0→7E8` | MAP / gauge boost candidate. |
| `22F470` | `7E0→7E8` | VGT commanded candidate. |
| `22F471` | `7E0→7E8` | VGT actual candidate. |
| `22F476` | `7E0→7E8` | Charge air cooler / post-intercooler temp candidate. |
| `22F479` | `7E0→7E8` | Turbo inlet pressure candidate; note possible conflict with internet alternator claims. |
| `22F47A` | `7E0→7E8` | LP turbo speed / alternate turbo value candidate. |
| `22F48D` | `7E0→7E8` | Turbo bypass candidate. |

### TESTING.Transmission

Purpose: expand torque converter lockup/slip understanding.

| PID | Header | Purpose |
| --- | --- | --- |
| `221E35` | `7E1→7E9` | TCC desired slip candidate; compare against confirmed `1E14` actual slip. |
| `221E3C` | `7E1→7E9` | TCC commanded pressure candidate. |

### TESTING.4x4

Purpose: look for transfer case, TOD clutch, prop shaft, and rear-locker state.

Tested on both likely paths:

| PID | Header(s) | Purpose |
| --- | --- | --- |
| `22C45D` | `7E2→7EA`, `7E0→7E8` | 4WD mode candidate. |
| `22C460` | `7E2→7EA`, `7E0→7E8` | Transfer clutch duty candidate. |
| `22C461` | `7E2→7EA`, `7E0→7E8` | Front prop speed candidate. |
| `22C462` | `7E2→7EA`, `7E0→7E8` | Rear prop speed candidate. |
| `22C463` | `7E2→7EA`, `7E0→7E8` | Rear locker / eLSD state candidate. |

### TESTING.Battery

Purpose: extend already-working BMS / smart-charging visibility without cluttering production.

| PID | Header | Purpose |
| --- | --- | --- |
| `224006` | `726→72E` | Battery SOH candidate. |
| `224007` | `726→72E` | Battery internal resistance candidate. |
| `22400A` | `726→72E` | Battery temperature candidate. |
| `22032E` | `7E0→7E8` | Generator desired voltage candidate. |
| `221632` | `7E0→7E8` | Generator command duty candidate. |

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
| `22F46B` | `7E0→7E8` | `EVEREST_TEST_EGT12_7E0_F46B_RAW16_AB, EVEREST_TEST_EGT12_7E0_F46B_CANDIDATE` | Repeated screenshot result: Negative response / out of range. Shelved to save active screenshot space. |
| `22F46C` | `7E0→7E8` | `EVEREST_TEST_EGT13_DPF_INLET_7E0_F46C_RAW16_AB, EVEREST_TEST_EGT13_DPF_INLET_7E0_F46C_CANDIDATE` | Repeated screenshot result: Negative response / out of range. Shelved to save active screenshot space. |
| `22F479` | `7E0→7E8` | `EVEREST_TEST_TURBO_INLET_PRESSURE_7E0_F479_RAW16_AB, EVEREST_TEST_TURBO_INLET_PRESSURE_7E0_F479_CANDIDATE` | Repeated screenshot result: Negative response / out of range. Shelved to save active screenshot space. |
| `22F48D` | `7E0→7E8` | `EVEREST_TEST_TURBO_BYPASS_7E0_F48D_RAW16_AB, EVEREST_TEST_TURBO_BYPASS_7E0_F48D_CANDIDATE` | Repeated screenshot result: Negative response / out of range. Shelved to save active screenshot space. |

## Ranger-derived additions

### TESTING.Tires

Added tyre-pressure candidates from Ranger `726→72E` commands:

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
| Engine / torque | `010163` | Engine reference torque held around `500 N·m`. | Strong testing candidate / likely reference torque. |
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
| `010B` | Generic MAP gauge boost fixed offset | Uses `A - 101 kPa`; watch for 255 kPa absolute cap under load. |
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
