# Ford Everest — Pelican PID Project

I use Pelican daily and started this project to build better support for my Ford Everest. Several months of driving, diagnostic-log analysis, dashboard comparisons and FORScan cross-checks have produced useful PID definitions, with the main goal of helping other Everest owners get more from Pelican.

## Vehicle platform and validation

The Everest is manufactured in [Thailand](https://corporate.ford.com/operations/locations/global-plants/) and is closely related to the next-generation Ranger through [Ford's T6 platform family](https://media.ford.com/content/dam/fordmedia/img/ASEAN/11/24/Bio/Ian_Foston_Bio%20FINAL.pdf). This connection makes Ranger PID definitions useful research references, although each candidate still needs Everest-specific validation.

Validated vehicle: Australian-market Ford Everest Trend MY25.25, 2.0 L Bi-Turbo diesel, 10-speed automatic and full-time 4WD.

The project is intended to benefit Everest owners across markets. Australia identifies the vehicle used for validation; it does not define a compatibility restriction. Individual PID support can vary with powertrain, module software and calibration.

**v0.7.35:** 81 commands, 98 retained signals and no active TESTING widgets. Decoding, IDs, paths, polling settings and connectables are unchanged from v0.7.34; two signal descriptions have been clarified.

Development remains ongoing. There are no current testing items; future evidence may lead to further validation, refinements or new candidates.

## Coverage

- Engine operation, temperatures, torque, airflow, boost and engine run/idle counters.
- Transmission temperature, gear, shaft speeds and torque-converter behaviour.
- DPF fullness, regeneration history, exhaust temperatures and filter pressures.
- Fuel information, vehicle speed, odometer and 12 V battery measurements.

## Files

- [default.json](signalsets/v3/default.json): the complete signal pack at `signalsets/v3/default.json`.
- [EVEREST_PID_DEVELOPMENT_LOG.md](EVEREST_PID_DEVELOPMENT_LOG.md): detailed evidence, decoding decisions, limitations and release history.

The DPF fullness signal represents Ford's internal model and can differ from the dashboard percentage. Further interpretation details are recorded in the development log.

## References

- Primary upstream target: [OBDb/Ford-Everest](https://github.com/OBDb/Ford-Everest).
- Shared-platform reference: [OBDb/Ford-Ranger](https://github.com/OBDb/Ford-Ranger).
- SAE J1979 signal definitions: [OBDb/SAEJ1979](https://github.com/OBDb/SAEJ1979).
- [Pelican extended PID documentation](https://pelican.clutch.engineering/scanning/extended-pids/).

## Privacy

Pelican database exports may contain the full VIN, and screenshots may reveal identifiable routes. Sanitise vehicle and location identifiers before publishing raw captures.

## Contribution intent

The intended home for useful definitions is [OBDb/Ford-Everest](https://github.com/OBDb/Ford-Everest), to help expand Pelican's Everest support.
