## Ford Everest MY25.25 PID project

This repository develops and validates extended Pelican/OBDb signals for the Australian-market Ford Everest Trend MY25.25 with the 2.0 L Bi-Turbo diesel, 10-speed automatic transmission and full-time 4WD.

The active Pelican target is:

```text
signalsets/v3/default.json
```

## Current release

Current validated build: **v0.7.24 — standard packet mirrors, DPF pressure and testing cleanup**.

The pack contains 82 commands and 142 signals. Of these, 98 are production signals and 44 remain isolated under `TESTING.*`.

## Latest release highlights

- Promoted generic EGR A commanded, actual and error values from SAE PID 0169.
- Decoded Ford F46A as diesel intake-airflow command and relative position.
- Confirmed Ford F46D as a mirror of standardized 016D fuel-pressure command, actual and temperature; retained it only as raw testing companions to avoid duplicate production widgets.
- Promoted Ford F478 exhaust-gas temperatures for bank 1 sensors 1 and 2 using raw/10-40 °C.
- Decoded Ford F47A as DPF inlet and outlet pressure using raw/100 kPa.
- Decoded Ford F48B normalized regeneration trigger, average regeneration interval and average regeneration distance.
- Removed the disproved F48B byte-D active-regeneration widget, invalid /32 temperature candidates and redundant overlapping packet scouts.
- Preserved useful raw companions with accurate names and TESTING paths.

## Confirmed production highlights

- DPF fullness / soot-load estimate, normalized regeneration trigger, average regeneration interval and distance, DPF inlet/outlet pressure, and distance since completed regeneration.
- Exhaust-gas temperatures, EGR command/actual/error and intake-airflow control command/position.
- Transmission temperature, current gear, shaft speeds, TCC actual slip, desired slip and apply command.
- Engine speed, load, coolant temperature, oil temperature, manifold pressure, boost command/actual, VGT command/actual and torque signals.
- Generic MAF, MAP, barometric pressure, fuel rates, exhaust flow and fuel-rail pressure/temperature.
- Fuel level, range, odometer and vehicle speed.
- Battery voltage, charge, current, age and alternator current.

## Important DPF interpretation

`EVEREST_DPF_FULLNESS_0610` is a validated internal fullness/soot-load measure, but it is not the dashboard's exact modelled percentage. It fell through the retained automatic regeneration and agrees directionally with the display; normal-drive anchors also showed modest divergence.

F48B's former byte-D active-regeneration interpretation was incorrect. Bytes D/E are one 16-bit average-time-between-regenerations value. No reliable active-regeneration status flag has yet been identified, so active state remains an open testing question.

## High-value remaining testing work

- Secondary 220610 soot-model word.
- F48B regeneration-status bits during another confirmed burn.
- Raw Ford mirrors retained for cross-checking standardized 016D/0170 behaviour.
- Any remaining responsive packet fields whose identities are not yet strong enough for production.

## Validation rules

1. Production and TESTING remain clearly separated.
2. New candidates are promoted only after plausible behaviour and repeatable vehicle-state correlation.
3. Useful raw companions are retained for unresolved or mirrored packets.
4. Persistent negative-response, frozen, redundant, demonstrably misaligned or disproved widgets are removed.
5. Generic SAE Mode 01 signals use `GENERIC_*` IDs and live under the relevant `*.Generic` path.
6. Ford-specific confirmed signals use established `EVEREST_*` or `FORD_*` IDs.
7. Ranger definitions are reference candidates only; actual Everest responses take precedence.
8. Every release is checked for JSON validity, duplicate IDs, malformed commands, accidental production changes and TESTING path containment.

## References

- Primary upstream target: `OBDb/Ford-Everest`
- Shared-platform reference only: `OBDb/Ford-Ranger`
- SAE J1979 signal reference project: <https://github.com/OBDb/SAEJ1979>
- Pelican extended PID documentation: <https://pelican.clutch.engineering/scanning/extended-pids/>

## Privacy

Pelican database exports may contain the full VIN, and screenshots may reveal identifiable routes. Do not publish raw archives without sanitising vehicle and location identifiers.

## Contribution intent

The intent is to contribute Everest-verified signals upstream once the definitions are clean, repeatable and useful to other owners.
