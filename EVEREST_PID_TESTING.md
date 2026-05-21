# Ford Everest MY25.25 PID Testing Log

https://github.com/r33zA/Ford-Everest

Version: 2026-05-17 v0.6.4 DPF fullness promoted  
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

# v0.7.1 — Testing cleanup and VGT scaling pass

Aligned default file: `default_everest_my25_25_v0_7_1_testing_cleanup.json`

## Update focus

- Built from latest uploaded `default(2).json`.
- Removed repeatedly out-of-range test PIDs:
  - `22F46B`
  - `22F46C`
  - `22F479`
  - `22F48D`
- Added alternate 16-bit scaling candidates for VGT:
  - `EVEREST_TEST_VGT_COMMANDED_7E0_F470_DIV32_768`
  - `EVEREST_TEST_VGT_ACTUAL_7E0_F471_DIV32_768`
- Preserved raw AB companions for `F470` and `F471`.
- Preserved production signals unchanged.
- Root `TESTING.*` rule preserved.

## Decisions from v0.7.0 screenshots

| PID | Result | Decision |
| --- | --- | --- |
| `22F46B` | Negative response / out of range | Removed |
| `22F46C` | Negative response / out of range | Removed |
| `22F479` | Negative response / out of range | Removed |
| `22F48D` | Negative response / out of range | Removed |
| `22F470` | Responding, raw around 1800, old scaling around 3% | Keep; add div32.768 scaling |
| `22F471` | Responding, raw around 1800–1950, old scaling around 3% | Keep; add div32.768 scaling |
| `22F46A` | Responding with plausible EGT values | Keep |
| `22F46D` | Responding with plausible EGT values | Keep |
| `22F478` | Tracks close to F46D | Keep |
| `221E35` | Responding with plausible desired TCC slip | Keep |
| `221E3C` | Responding with plausible TCC pressure/command values | Keep |

## Sanity-check snapshot

| Check | Result |
| --- | ---: |
| Commands | 100 |
| Signals | 143 |
| Signals with suggestedMetric | 19 |
| Root TESTING signals | 77 |
| Removed commands | 4 |
| Duplicate signal IDs | 0 |
| JSON validation | Passed |

## Commit message

```text
Clean up failed test PIDs and refine VGT scaling
```

## Extended description

```text
Updated the Ford Everest MY25.25 v0.7 testing signalset after live Pelican screenshot validation.

Removed test PIDs that repeatedly returned negative response / out of range:
- 22F46B
- 22F46C
- 22F479
- 22F48D

Kept the promising live candidates:
- 22F46A
- 22F46D
- 22F478
- 22F470
- 22F471
- 221E35
- 221E3C

Added alternate 16-bit div32.768 VGT scaling candidates for 22F470 and 22F471. The previous 8-bit style scaling showed around 3%, while the raw AB values around 1800–1950 suggest raw/32.768 may produce a more realistic VGT commanded/actual percentage range.

Production signals remain unchanged. Root TESTING.* structure is preserved.

Validation:
- JSON parses successfully.
- No duplicate signal IDs.
- No non-root testing paths.
```

