## Ford Everest MY25.25 PID project

This repository develops and validates extended Pelican/OBDb signals for the Australian-market Ford Everest Trend MY25.25 with the 2.0 L Bi-Turbo diesel, 10-speed automatic transmission and full-time 4WD.

The active Pelican target is:

```text
signalsets/v3/default.json
```

## Current release

Current validated build: **v0.7.22 — confirmed regeneration and packet-state breakthrough**.

The pack currently contains 82 commands and 132 signals. Experimental work remains isolated under `TESTING.*`; production sections contain signals supported by repeated vehicle evidence.

## Confirmed production highlights

- DPF fullness / soot-load estimate and distance since completed regeneration.
- Transmission temperature, current gear, shaft speeds, TCC actual slip and desired slip.
- Engine speed, load, coolant temperature, oil temperature, manifold pressure and torque signals.
- Generic MAF, MAP and fuel rate.
- Fuel level, range, odometer and vehicle speed.
- Battery voltage, charge, current, age and alternator current.
- EGR opening and established exhaust-temperature reference.

## Latest regeneration result

The v0.7.22 evidence captured a complete automatic DPF regeneration:

- Production DPF soot/fullness fell from 73.53% to 25.90%.
- Dashboard fullness independently declined to 15% late in the burn.
- Distance since regeneration reset from 213.3 km to 0 km.
- EGR remained closed during the burn and reopened afterward.
- F48B byte D was 1 during active regeneration and 0 during normal and completed states.

F48B byte D is the strongest active-regeneration candidate discovered so far, but remains in TESTING until repeated in another confirmed burn.

## High-value testing work

- F48B active/status bytes and phase signatures.
- Secondary 220610 soot-model word.
- 019D paired fuel-rate words and possible regeneration fuelling divergence.
- 019E exhaust-flow scaling.
- Shifted F46D and F478 exhaust-temperature packet fields.
- Shifted 0169 EGR, 016D fuel-rail and 0170 boost/turbo packet pairs.
- F470/F471/F47A turbo and VGT packet decoding.
- TCC apply-command scaling.

## Validation rules

1. Production and TESTING remain clearly separated.
2. New candidates are promoted only after plausible behaviour and repeatable vehicle-state correlation.
3. Raw companions are retained for unresolved but responsive packets.
4. Persistent negative-response, frozen, redundant or demonstrably misaligned widgets are removed.
5. Generic SAE Mode 01 signals use `GENERIC_*` IDs and live under the relevant `*.Generic` path.
6. Ranger definitions are reference candidates only; actual Everest responses take precedence.
7. Every release is checked for JSON validity, duplicate IDs, malformed commands and TESTING path containment.

## References

- Primary upstream target: `OBDb/Ford-Everest`
- Shared-platform reference only: `OBDb/Ford-Ranger`
- Pelican extended PID documentation: <https://pelican.clutch.engineering/scanning/extended-pids/>

## Privacy

Pelican database exports may contain the full VIN. Do not publish raw databases without sanitising vehicle identifiers.

### Testing approach

Current working rules:

1. Keep confirmed and experimental signals clearly separated.
2. Put uncertain, speculative, or formula-testing signals under `TESTING.*`.
3. Add or edit PIDs in small batches where possible.
4. Preserve raw companion signals for formula investigation.
5. Validate values against real vehicle behaviour, dashboard values, or repeated drive captures.
6. Remove obvious repeated negative-response items from active testing to reduce screen clutter.
7. Only contribute verified or well-evidenced signals upstream.

### Current validation focus

Current testing focus includes:

- DPF / exhaust-filter fullness and regeneration behaviour
- Exhaust gas temperature sensors
- Transmission temperature, gear and torque converter behaviour
- Battery, BMS and smart alternator telemetry
- Boost, VGT and turbo-related signals
- 4WD / transfer case telemetry
- TPMS and Ranger-derived comparison candidates

### Contribution intent

Contributions are welcome in the upstream OBDb repositories.

The intent is to contribute useful Everest-verified signals back upstream once enough evidence has been collected and the signal definitions are clean enough to be helpful to others.
