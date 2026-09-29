## Ford Everest MY25.25 PID project

This repository develops and validates extended Pelican/OBDb signals for the Australian-market Ford Everest Trend MY25.25 with the 2.0 L Bi-Turbo diesel, 10-speed automatic transmission and full-time 4WD.

The active Pelican target is:

```text
signalsets/v3/default.json
```

## Current release

Current validated build: **v0.7.31 — 29 September interrupted-regeneration evidence and speed-widget cleanup**.

The pack contains 80 commands and 97 signals. Of these, 96 are production signals and exactly one remains isolated under `TESTING.*`.

## Latest release highlights

- Analysed three consecutive Pelican sessions covering approximately 38.8 km and 36,096 logged commands.
- Documented a driver-confirmed interrupted DPF regeneration. The dashboard was approximately 50% when the production `220610` model reached 47.87%, and the model ended near 48.11% after driving had to stop.
- Strengthened the sole remaining TESTING field after its secondary scalar fell from 16.84 to 5.61 and rebounded to 7.07. Its physical identity and engineering unit remain unknown.
- Confirmed that `220610` word 3 duplicated word 1 in all 449 captured packets, while word 4 remained a near-copy. Neither warrants another visible signal.
- Removed the hard-coded `+4 km/h` corrected speed signal now that Pelican supports speed-widget trimming.
- Made the unmodified `EVEREST_SPEED_FORD_EXTENDED_F40D` signal the sole `speed` connectable. Retained formulas, command frequency and command count are unchanged.
- Found no Request Out Of Range responses and no production-signal failure requiring removal.

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

The second `220610` word is also definitively soot-related. Across three completed automatic burns and the deliberately interrupted 29 September burn it has declined coherently during cleaning and rebounded afterward. In the latest drive it began declining before the primary model peaked, showing that it is not simply another fullness percentage. It remains TESTING because neither its physical identity nor engineering unit has been established. The `/100` scalar view is the sole remaining testing signal.

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
