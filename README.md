## Ford Everest MY25.25 PID project

This repository develops and validates extended Pelican/OBDb signals for the Australian-market Ford Everest Trend MY25.25 with the 2.0 L Bi-Turbo diesel, 10-speed automatic transmission and full-time 4WD.

The active Pelican target is:

```text
signalsets/v3/default.json
```

## Current release

Current validated build: **v0.7.25 — confirmed regeneration evidence and prefix-scout cleanup**.

The pack contains 82 commands and 135 signals. Of these, 98 are production signals and 37 remain isolated under `TESTING.*`.

## Latest release highlights

- Recorded a complete 24 August automatic DPF regeneration from a `220610` peak of 79.21% to a 23.51% minimum.
- Confirmed that `220610` is a strong internal soot/fullness model but not the dashboard's exact displayed percentage, especially near regeneration completion.
- Identified approximately 11:30:54 as the completion boundary, when F48B average interval and distance recalculated together from 344 to 330 minutes and 186 to 178 km.
- Reconfirmed F478 exhaust temperatures up to 643.2 °C and positive, load-coherent F47A DPF inlet/outlet pressure through the burn.
- Reconfirmed regeneration-specific divergence between the two production `019D` fuel-rate fields.
- Removed seven obsolete prefix-dominated first-word TESTING scouts while retaining correctly aligned raw companions and unresolved fields.
- Changed no production formula, command, path, connectable or battery signal.

## Confirmed production highlights

- DPF fullness / soot-load estimate, normalized regeneration trigger, average regeneration interval and distance, DPF inlet/outlet pressure, and distance since completed regeneration.
- Exhaust-gas temperatures, EGR command/actual/error and intake-airflow control command/position.
- Transmission temperature, current gear, shaft speeds, TCC actual slip, desired slip and apply command.
- Engine speed, load, coolant temperature, oil temperature, manifold pressure, boost command/actual, VGT command/actual and torque signals.
- Generic MAF, MAP, barometric pressure, fuel rates, exhaust flow and fuel-rail pressure/temperature.
- Fuel level, range, odometer and vehicle speed.
- Battery voltage, charge, current, age and alternator current.

## Important DPF interpretation

`EVEREST_DPF_FULLNESS_0610` is a validated internal fullness/soot-load measure, but it is not the dashboard's exact modelled percentage. During the 24 August burn, the dashboard showed 50%, 20% and 10% while Pelican showed approximately 48%, 30-32% and 24%. The difference becomes most obvious near regeneration completion; the `raw / 100` formula remains valid for the internal model.

F48B's former byte-D active-regeneration interpretation was incorrect. Bytes D/E are one 16-bit average-time-between-regenerations value. During the latest confirmed burn, the actual status byte remained zero throughout. The normalized trigger fell in coarse steps and the average interval/distance fields recalculated together after completion, but none is a reliable live active-regeneration flag.

## High-value remaining testing work

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

## References

- Primary upstream target: `OBDb/Ford-Everest`
- Shared-platform reference only: `OBDb/Ford-Ranger`
- SAE J1979 signal reference project: <https://github.com/OBDb/SAEJ1979>
- Pelican extended PID documentation: <https://pelican.clutch.engineering/scanning/extended-pids/>

## Privacy

Pelican database exports may contain the full VIN, and screenshots may reveal identifiable routes. Do not publish raw archives without sanitising vehicle and location identifiers.

## Contribution intent

The intent is to contribute Everest-verified signals upstream once the definitions are clean, repeatable and useful to other owners.
