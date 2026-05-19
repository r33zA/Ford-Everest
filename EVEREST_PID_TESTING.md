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

## Commit message

```text
Promote Everest DPF fullness and regen distance PIDs
```

## Extended description

```text
Promoted two Ford Everest MY25.25 DPF signals from validation into the stable default signalset.

Added EVEREST_DPF_FULLNESS_0610 using PID 7E0 220610 with formula raw/100 percent. This value matched the vehicle exhaust-filter display through a complete regen cycle, including high/full state, cleaning, and post-regen near-zero state.

Added EVEREST_DISTANCE_SINCE_DPF_REGEN_0614 using PID 7E0 220614 with formula raw/10 kilometres. This value counted up with driving distance, reset to 0 km at regen completion, then immediately began counting up again.

EVEREST_DPF_FULLNESS_0610 has been added as a dpfSootLoad connectable candidate.

A small testing-only emissions section was retained for 22F48B, 22F478, 22F463, and 220440 so future DPF/regen/EGT investigation can continue without carrying over the broader v0.3 exploratory clutter.

Validation:
- JSON parses successfully.
- No duplicate signal IDs.
- Stable v0.6.3 div16 transmission-temperature baseline preserved.
```
