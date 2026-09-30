## Ford Everest MY25.25 PID project

This repository develops and validates extended Pelican/OBDb signals for the Australian-market Ford Everest Trend MY25.25 with the 2.0 L Bi-Turbo diesel, 10-speed automatic transmission and full-time 4WD.

The active Pelican target is:

```text
signalsets/v3/default.json
```

## Current release

Current validated build: **v0.7.33 — lifetime engine-counter promotion and cleanup**.

The pack contains 81 commands and 99 signals. Of these, 98 are production signals and exactly one remains isolated under `TESTING.*`.

## Latest release highlights

- Promoted the SAE Mode 01 `017F` lifetime engine-run and engine-idle counters to `Engine.Generic` after five successful responses across three consecutive ignition cycles.
- Total run time advanced exactly with elapsed engine-on time in two independently sampled intervals: 168 seconds over 168.015 seconds and 1,750 seconds over 1,750.622 seconds.
- Both counters persisted across restarts, total run time remained greater than idle time, and idle increments were plausible for the observed driving.
- Removed the zero-only PTO widget. The packet support byte remained `07`, so the field exists, but it accumulated no useful value on this Everest.
- Retained the `017F` command at its low 60-second polling frequency.
- No unrelated production signal, formula, path, polling frequency or connectable was changed.

## Lifetime engine counters

The confirmed `017F` command uses `7E0` request and `7E8` response addressing and polls only once every 60 seconds. Its first data byte is a support mask followed by 32-bit cumulative counters:

| Production signal | Packet field | Display |
| --- | --- | --- |
| Lifetime engine run time | bytes B-E | raw seconds / 3600 hours |
| Lifetime engine idle time | bytes F-I | raw seconds / 3600 hours |

The five captured responses spanned approximately 1,030.96 to 1,032.31 total hours and 330.86 to 331.12 idle hours. The PTO field remained zero in every packet and is intentionally not exposed as a widget.

## Confirmed production highlights

- DPF fullness / soot-load estimate, normalized regeneration trigger, average regeneration interval and distance, DPF inlet/outlet pressure, and distance since completed regeneration.
- Exhaust-gas temperatures, EGR command/actual/error and intake-airflow control command/position.
- Transmission temperature, current gear, shaft speeds, TCC actual slip, desired slip and apply command.
- Engine speed, load, coolant temperature, oil temperature, manifold pressure, boost command/actual, VGT command/actual and torque signals.
- Generic MAF, MAP, barometric pressure, fuel rates, exhaust flow and fuel-rail pressure/temperature.
- Fuel level, range, odometer and vehicle speed.
- Battery voltage, direct state of charge, current, age and alternator current.

## Important DPF interpretation

`EVEREST_DPF_FULLNESS_0610` is a validated internal fullness/soot-load measure, but it is not always the dashboard's exact modelled percentage. During the deliberately interrupted 29 September burn, the dashboard was approximately 50% while Pelican reached 47.87% and ended near 48.11%. During the complete 31 August burn, the dashboard moved from 90% to 0% while Pelican moved from approximately 71.94% to 17.69%, reached 15.85% shortly after completion and then began rebounding. The `raw / 100` formula remains valid for the internal model.

The second `220610` word is also definitively soot-related. Across three completed automatic burns and the deliberately interrupted 29 September burn it has declined coherently during cleaning and rebounded afterward. In the latest drive it began declining before the primary model peaked, showing that it is not simply another fullness percentage. It remains TESTING because neither its physical identity nor engineering unit has been established. Its `/100` scalar view is now the sole remaining TESTING signal.

F48B's former byte-D active-regeneration interpretation was incorrect. Bytes D/E are one 16-bit average-time-between-regenerations value. Across two completed burns where F48B was polled, the normalized trigger fell in coarse steps and the average interval/distance fields recalculated together at completion, but none is a reliable live active-regeneration flag. F48B was not polled during the 31 August burn.

## Speed interpretation

`EVEREST_SPEED_FORD_EXTENDED_F40D` is the sole vehicle-speed signal and the active `speed` connectable. Its unmodified Ford value reads approximately 4 km/h below the dashboard on this Everest.

Pelican now supports trimming the OBD or GPS speed shown by its speed widget. Apply approximately `+4 km/h` in Pelican when a dashboard-matching presentation is preferred. The former hard-coded `EVEREST_SPEED_FORD_EXTENDED_F40D_CORRECTED` signal was removed to avoid maintaining two presentations of the same byte.

## Battery interpretation

The production BCM battery definitions remain strongly confirmed:

- `224027`: battery age in days, with the hours presentation derived as days × 24.
- `224028`: direct battery state of charge in percent and the active `stateOfCharge` connectable.
- `22402A`: direct BCM battery voltage using `A / 20 + 6` volts and the active `starterBatteryVoltage` connectable.
- `22402B`: battery current using `A - 127` amps; negative-current direction still needs a below-127 cross-tool capture.

The responsive DIDs `401C`, `4021` and `4026` are not FORScan `CUM_DIS_SLP`, `CUM_DIS_RUN` and `CUM_DIS_OFF`. Their former TESTING labels were disproved and removed. Do not restore them without new wire-level identification evidence.

## High-value remaining work

- Identify the engineering unit and exact meaning of the secondary `220610` soot-model word. Another ordinary regeneration is no longer the missing evidence; a named FORScan value or authoritative definition is needed.
- Capture a deliberate stationary Park/Reverse/Neutral/Drive sequence to finish interpreting the non-forward `1E1F` gear values `70`, `128` and `130` before changing their production presentation.
- Capture `402B` below raw 127 beside FORScan to finish validating negative-current direction.
- Obtain wire definitions for `BAT_CHRG_MODE`, `BAT_CUR_PRD`, `BATT_V_INF`, the real `CUM_DIS_*` counters and `VBAT_B–E`; no guessed Pelican definitions are included.
- During any future regeneration, keep `220614`, F48B and the production EGT/pressure signals actively polled so the distance reset, history recalculation and thermal behaviour are captured together.
- Continue searching for a genuine live active-regeneration state without resurrecting disproved F48B interpretations.

## Validation rules

1. Production and TESTING remain clearly separated.
2. New candidates are promoted only after plausible behaviour and repeatable vehicle-state correlation.
3. Useful raw companions are retained only while they answer a genuinely unresolved decoding question.
4. Persistent negative-response, frozen, redundant, demonstrably misaligned or disproved widgets are removed.
5. Generic SAE Mode 01 signals use `GENERIC_*` IDs and live under the relevant `*.Generic` path.
6. Ford-specific confirmed signals use established `EVEREST_*` or `FORD_*` IDs.
7. Ranger definitions are reference candidates only; actual Everest responses take precedence.
8. Every release is checked for JSON validity, duplicate IDs, malformed commands, accidental production changes and TESTING path containment.
9. Battery experiments are read-only and module-specific; do not add BMS reset/write routines or broadcast `7DF` polling.

## References

- Primary upstream target: `OBDb/Ford-Everest`
- Shared-platform reference only: `OBDb/Ford-Ranger`
- SAE J1979 signal reference project: <https://github.com/OBDb/SAEJ1979>
- Pelican extended PID documentation: <https://pelican.clutch.engineering/scanning/extended-pids/>

## Privacy

Pelican database exports may contain the full VIN, and screenshots may reveal identifiable routes. Do not publish raw archives without sanitising vehicle and location identifiers.

## Contribution intent

The intent is to contribute Everest-verified signals upstream once the definitions are clean, repeatable and useful to other owners.
