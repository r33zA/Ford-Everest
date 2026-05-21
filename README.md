## Ford Everest MY25.25 testing notes

### Vehicle target

This work is being tested against an Australian-market Ford Everest:

- Ford Everest Trend
- MY25.25
- 2.0 L Bi-Turbo Diesel
- 10-speed automatic
- Full-time 4WD
- Next-Gen Ranger / Everest platform

### Project status

This repository is an active Ford Everest PID testing and validation workspace.

At this stage, the available Pelican Everest `default.json` baseline is effectively empty, so this work is being built and tested directly from the Everest signal pack maintained here.

### References

Primary upstream target:

- `OBDb/Ford-Everest`

Reference only:

- `OBDb/Ford-Ranger`

The Ford Ranger repository is useful because of the shared platform, but Ranger signals are treated only as candidates. They are not assumed to work on the Everest until tested on the vehicle.

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
