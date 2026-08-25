## Ford Everest MY25.25 PID project

This repository develops and validates extended Pelican/OBDb signals for the Australian-market Ford Everest Trend MY25.25 with the 2.0 L Bi-Turbo diesel, 10-speed automatic transmission and full-time 4WD.

The active Pelican target is:

```text
signalsets/v3/default.json
```

## Current release

Current validated build: **v0.7.27 — FORScan-correlated BCM validation and negative-response cleanup**.

The pack contains 85 commands and 137 signals. Of these, 97 are production signals and 40 remain isolated under `TESTING.*`.

## Latest release highlights

- Confirmed `401C`, `4021` and `4026` as the Everest BCM DIDs for FORScan `CUM_DIS_SLP`, `CUM_DIS_RUN` and `CUM_DIS_OFF` through exact timestamp-matched raw scalar values.
- Retained the three cumulative-discharge signals as raw-only `TESTING.BMS` widgets because their engineering conversion and unit remain unresolved.
- Corrected their polling interval from one second to 60 seconds after the test drive proved `freq: 1` produced approximately one request per second per command.
- Removed DID `4029` and DID `4090` after each returned NRC `31` Request Out Of Range for all 14 requests across approximately 17 minutes.
- Strengthened production documentation for battery age `4027`, SOC `4028`, BCM voltage `402A` and battery current `402B` using matched FORScan and Pelican evidence.
- Changed no production formula, ID, path or connectable.

## Confirmed production highlights

- DPF fullness / soot-load estimate, normalized regeneration trigger, average regeneration interval and distance, DPF inlet/outlet pressure, and distance since completed regeneration.
- Exhaust-gas temperatures, EGR command/actual/error and intake-airflow control command/position.
- Transmission temperature, current gear, shaft speeds, TCC actual slip, desired slip and apply command.
- Engine speed, load, coolant temperature, oil temperature, manifold pressure, boost command/actual, VGT command/actual and torque signals.
- Generic MAF, MAP, barometric pressure, fuel rates, exhaust flow and fuel-rail pressure/temperature.
- Fuel level, range, odometer and vehicle speed.
- Battery voltage, direct state of charge, current, age and alternator current.

## Important DPF interpretation

`EVEREST_DPF_FULLNESS_0610` is a validated internal fullness/soot-load measure, but it is not the dashboard's exact modelled percentage. During the 24 August burn, the dashboard showed 50%, 20% and 10% while Pelican showed approximately 48%, 30-32% and 24%. The difference becomes most obvious near regeneration completion; the `raw / 100` formula remains valid for the internal model.

F48B's former byte-D active-regeneration interpretation was incorrect. Bytes D/E are one 16-bit average-time-between-regenerations value. During the latest confirmed burn, the actual status byte remained zero throughout. The normalized trigger fell in coarse steps and the average interval/distance fields recalculated together after completion, but none is a reliable live active-regeneration flag.

## High-value remaining testing work

- Determine the engineering transform and unit for the confirmed raw `CUM_DIS_SLP`, `CUM_DIS_RUN` and `CUM_DIS_OFF` values without guessing from the rounded FORScan display.
- Capture `402B` below raw 127 beside FORScan to finish validating negative-current direction.
- Obtain wire definitions for `BAT_CHRG_MODE`, `BAT_CUR_PRD`, `BATT_V_INF` and `VBAT_B–E`; no unverified Pelican definitions have been added for these labels. `V_BATT_BCM` is now confirmed as DID `402A`.
- Identify the unit and exact meaning of the secondary `220610` soot-model word, which fell from 17.43 to 1.61 during the latest burn.
- Perform one more deliberate simultaneous comparison of Ford F46D/F470 mirrors against standardized `016D`/`0170` before considering removal.
- Replace or retire fixed-offset gauge-boost testing if a reliable atmospheric-reference workflow becomes available.
- Continue searching for a genuine live active-regeneration state without resurrecting disproved F48B interpretations.

## Validation rules

1. Production and TESTING remain clearly separated.
2. New candidates are promoted only after plausible behaviour and repeatable vehicle-state correlation.
3. Useful raw companions are retained for unresolved or mirrored packets.
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
