# Ford Everest MY25.25 PID Testing Log

Last updated: 2026-05-08

Update focus:
- Added FORScan 2024+ 6G Ranger research notes as platform-related PID-hunting clues.
- Documented As-Built/config addresses that should **not** be copied into `default.json` as live PIDs.
- Added future research targets for DPF regen state, exhaust filter cleaning state, oil pressure scaling, engine hours, idle hours, IPC mirrored trans temp, and factory TPMS temperature.
- Kept `FORD_EOP` / 7DF / `22F45C` as archive-only raw evidence, not an active production signal.

Vehicle:
- Ford Everest Trend
- MY25.25
- Ford Everest UB / Next Gen
- 2.0 L Bi-Turbo Diesel
- 10-speed automatic
- Australian market

- Vehicle generation:
- Ford Everest UB / U704
- Third-generation Everest, introduced 2022
- Australian UB series
- Platform related to Ford Ranger P703 / Next-Gen Ranger

PID donor priority:
- Ford Ranger P703 / Next-Gen Ranger data was preferred where exact Everest data was unavailable.
- Older Ranger PX/PX3 data was treated as lower-confidence and tested before use.

Testing setup:
- App: Pelican on iOS / CarPlay
- OBD source: custom GitHub fork of OBDb/Ford-Everest
- Signal file: signalsets/v3/default.json
- Test date: 2026-05-01


---

## Research notes — FORScan 2024+ 6G Ranger document

Source reviewed:
- `FORScan - (2024+) 6G Ranger.pdf`
- Scope: North American 2024+ Ranger / 6G Ranger community FORScan data.
- Relevance to this Everest: useful because the Everest UB / U704 is platform-related to the Ranger P703 / Next-Gen Ranger.
- Confidence rule: treat as **research guidance only** until proven on this Australian MY25.25 Everest.

### Important caution

The FORScan PDF is mostly an **As-Built / feature configuration reference**, not a direct live OBD PID list.

Do **not** copy configuration addresses into `default.json` as live PIDs.

| Looks like | Actually is | Use in PID work |
|---|---|---|
| `720-06-01` | IPC As-Built config address | Research clue only |
| `720-07-01` | IPC As-Built config address | Research clue only |
| `7D0-10-01` | APIM As-Built config address | Research clue only |
| `7E0-162-02` | PCM As-Built config address | Research clue only |
| `760-04-03` | ABS drive-mode config address | Research clue only |

### PID-hunting clues from the FORScan document

| Research clue | Module / address in FORScan doc | What it suggests | Action for our PID log |
|---|---|---|---|
| Oil Pressure Display | IPC `720-06-01` | The cluster may have an oil-pressure display pathway. Could be real pressure, inferred pressure, or switch/scalar-style data. | Keep oil pressure as a research target. Do **not** promote `FORD_EOP` yet. |
| Oil Temperature Display | IPC `720-06-01` | Oil temp is a normal Ford display/data item. | Supports keeping working oil temp `22F45C` active. |
| Cylinder Head Temperature Display | IPC `720-06-01` | CHT is expected on related platform data. | Supports keeping `2205FC` CHT sensor 2 as useful. |
| Inlet Air Temperature Display | IPC `720-06-01` | Intake temp is expected in cluster/module data. | Supports keeping `22F40F` and related intake signals. |
| Digital Engine + Transmission Temperature Gauges | IPC `720-07-01` | Cluster can display engine/trans temps on some variants. | Supports current transmission temp work; keep `221E1C` `5/72` formula as best active candidate. |
| Engine Hour Function | IPC `720-04-01` | Lifetime engine hours may exist separately from current-trip runtime. | Future test target: engine hours / idle hours. |
| Engine Idle Hour Function | IPC `720-04-01` | Idle hours may exist as a separate service/diagnostic value. | Future test target, not active production signal. |
| Oil Life / Maintenance Monitor | APIM / IPC references, including APIM `7D0-10-01` | Oil life is a first-class Ford data item. | Supports keeping oil life `22054B`. Compare later against FordPass/dash. |
| TPMS Tyre Temperature Display | IPC `720-11-01` | Factory TPMS temperature may be displayable if vehicle/sensors support it. | Useful FORScan note. Low priority for `default.json` because current TPMS is Oricom aftermarket. |
| Manual Exhaust Filter Cleaning / regen config | PCM `7E0-162-02` | Ford exposes some exhaust-filter-cleaning strategy in PCM configuration on related vehicles. | High-value research clue for regen-active / DPF cleaning state. Do **not** edit PCM config based on this alone. |
| Drive Modes / Trail Turn Assist / Rock Crawl | ABS `760-04-01`, `760-04-03`; APIM support entries | Confirms ABS/APIM/IPC coordination for off-road features on related platform. | Supports our Trail Turn Assist result and keeps ABS movement signals interesting. |

### What this changes in our research priorities

| Priority | Target | Why |
|---:|---|---|
| 1 | DPF regeneration active / exhaust filter cleaning state | FORScan shows a PCM manual/auto exhaust-filter-cleaning configuration clue. This may point to a related live state PID somewhere. |
| 2 | Oil pressure scaling | IPC has an oil-pressure display clue, but current raw `FORD_EOP` evidence is not enough. |
| 3 | Engine hours / idle hours | FORScan shows IPC engine-hour and idle-hour functions. |
| 4 | IPC/cluster mirrored transmission temperature | Digital trans temp display exists on related platform. Could expose another value path. |
| 5 | Factory TPMS tyre temperature | Interesting only if factory sensors/modules expose temp. Low priority due to Oricom external TPMS. |

### Current decisions based on this research

Keep active / supported:
- `22F45C` engine oil temperature.
- `221E1C` transmission fluid temperature using the current `5/72` formula.
- `22054B` oil life remaining.
- `2205FC` cylinder head temperature sensor 2.
- `22F40F` intake air temperature.

Keep unresolved / research only:
- Oil pressure.
- Regen active / exhaust filter cleaning state.
- DPF fullness matching dash.
- Engine hours / idle hours.
- Factory TPMS tyre temperature.

Do **not** add directly to `default.json`:
- IPC `720-06-01` oil pressure / oil temp display config.
- IPC `720-07-01` digital temp gauge config.
- APIM `7D0-10-01` oil life menu config.
- PCM `7E0-162-02` manual exhaust filter cleaning config.
- ABS `760-04-03` drive-mode config.

### Notes for future testing

Suggested future test buckets:

```text
Engine.Testing.OilPressure
Engine.Testing.EngineHours
Engine.Testing.IdleHours
Emissions.Testing.DPFRegenState
Emissions.Testing.ExhaustFilterCleaning
Transmission.Testing.IPCTransTempMirror
Tires.Testing.FactoryTPMSTemperature
```

Testing rule:
- Add new candidates under `Category.Testing.<identifiable PID name>`.
- Promote only after live response + plausible value + behaviour check.
- Reject quickly if response is out of range, stale, blank, wrong scale, or obviously wrong target.


## Confirmed / working

| Date | PID / cmd | Header | Response | Signal | Result | Value seen | Expected | Notes |
|---|---|---:|---:|---|---|---:|---|---|
| 2026-05-01 | 22F42F | 7E0 | 7E8 | Fuel remaining | Works | 78 % | Close to dash fuel | Ford/Ranger-style fuel PID. Formula A*100/255. |
| 2026-05-01 | 012F | 7E0 | 7E8 | Generic fuel level | Works | 77–78 % | Close to dash fuel | Generic SAE fuel level. Good backup/comparison. |
| 2026-05-01 | 01A6 | 7E0 | 7E8 | Odometer | Works / pending dash confirmation | 25,567 km | Match dash odometer | Confirm exact dash odometer reading. |
| 2026-05-01 | 22F405 | 7E0 | 7E8 | Coolant temperature | Works | 47–56 °C | Rises with warm-up | Matched Pelican built-in ECT at 47 °C. Formula A-40. |
| 2026-05-01 | 22F45C | 7E0 | 7E8 | Engine oil temperature | Works | 52 °C | Warms near/after coolant | Formula A-40. |
| 2026-05-01 | 22F446 | 7E0 | 7E8 | Ambient air temperature | Works | 30 °C | Close to dash ambient | Dash showed about 29 °C. Formula A-40. |
| 2026-05-01 | 22F40F | 7E0 | 7E8 | Intake air temperature | Works | 35 °C | Plausible vs ambient | Formula A-40. |
| 2026-05-01 | 22051C | 7E0 | 7E8 | Intercooler air charge temperature | Works | 33 °C | Plausible vs intake/ambient | Formula A-40. |
| 2026-05-01 | 220522 | 7E0 | 7E8 | Fuel temperature | Works | 30 °C | Plausible | Formula A-40. |
| 2026-05-01 | 221E12 | 7E1 | 7E9 | Transmission gear commanded | Works | 1, 9 | Should match selected/current gear | TCM header. Formula A. |
| 2026-05-01 | 221E1F | 7E1 | 7E9 | Transmission gear engaged | Works | 1, 9 | Should match actual engaged gear | TCM header. Formula A. |
| 2026-05-01 | 221E14 | 7E1 | 7E9 | Torque converter slip speed | Works | 800.25 rpm, then 1.25 rpm | High when unlocked, low when locked | Behaviour looks correct. Formula (A*256+B)/4. |
| 2026-05-01 | 220487 | 7E0 | 7E8 | AdBlue tank level | Works | 17 L | Close to full tank | Ford AU tank capacity approx. 18.5 L. 17 L ≈ 92 %. Formula (A*256+B)/1000. |
| 2026-05-01 | 220685 | 7E0 | 7E8 | AdBlue tank temperature | Works / plausible | 20 °C | Plausible fluid temp | Formula A-40. |
| 2026-05-01 | 22052E | 7E0 | 7E8 | EGR open percentage | Works | 21 % | Changes with engine/load | Formula A*100/255. |
| 2026-05-01 | 220569 | 7E0 | 7E8 | EGT pre-turbo | Works / plausible | 200 °C | Plausible exhaust temp | Formula (A*256+B)/32. |
| 2026-05-01 | 220551 | 7E0 | 7E8 | Alternator current output | Works / plausible | 32.5–37.6 A | Plausible charging current | Formula (A*256+B)/64. |
| 2026-05-01 | 224028 | 726 | 72E | Battery state of charge estimated | Works / plausible | 89 % | Plausible battery SOC | BCM candidate. Formula A. |
| 2026-05-01 | 22402B | 726 | 72E | Battery current in/out | Works / plausible | 3–5 A | Plausible | Sign direction still needs confirmation. Current formula A-126. |

## Working but needs more comparison

| Date | PID / cmd | Header | Response | Signal | Result | Value seen | Expected | Notes |
|---|---|---:|---:|---|---|---:|---|---|
| 2026-05-01 | 221E1C | 7E1 | 7E9 | Transmission fluid temperature div16 | Works / likely | 44.1–49.3 °C | Rises with warm-up | Formula (A*256+B)/16. Currently seems more plausible. |
| 2026-05-01 | 221E1C | 7E1 | 7E9 | Transmission fluid temperature 5/72 compare | Works / compare | 32–37.8 °C | Rises with warm-up | Formula ((A*256+B)*5/72)-17. Keep for one more warm-drive comparison. |

## Responds but not useful as intended

| Date | PID / cmd | Header | Response | Signal | Result | Value seen | Expected | Notes |
|---|---|---:|---:|---|---|---:|---|---|
| 2026-05-01 | 220579 | 7E0 | 7E8 | DPF soot blockage closed loop | Responds, wrong target | 12 % | Dash DPF fullness was 65 % | Not dash DPF full %. May be another internal DPF value. |
| 2026-05-01 | 22057B | 7E0 | 7E8 | DPF soot blockage open loop | Responds, wrong target | 96 % | Dash DPF fullness was 65 % | Not dash DPF full %. May be another internal DPF value. |
| 2026-05-01 | 220683 | 7E0 | 7E8 | AdBlue tank level mm | Responds, uncertain | 100 mm | Dash showed full / 9000 km range | Not useful as main AdBlue level. Keep only as test note. |
| 2026-05-01 | 220684 | 7E0 | 7E8 | AdBlue tank level percentage | Wrong meaning / scale | 33 % | Tank recently filled; dash showed full | Reject as AdBlue %. Litres PID is better. |
| 2026-05-01 | 2203F4 | 7E0 | 7E8 | EGT post turbo | Suspicious | 255 °C | Should vary | Same fixed value as other EGTs; likely wrong formula or default/null. |
| 2026-05-01 | 2203F5 | 7E0 | 7E8 | EGT pre DPF | Suspicious | 255 °C | Should vary | Same fixed value as other EGTs; likely wrong formula or default/null. |
| 2026-05-01 | 2203F6 | 7E0 | 7E8 | EGT post DPF | Suspicious | 255 °C | Should vary | Same fixed value as other EGTs; likely wrong formula or default/null. |

## Rejected / no useful response

| Date | PID / cmd | Header | Response | Signal | Result | Value seen | Expected | Notes |
|---|---|---:|---:|---|---|---|---|---|
| 2026-05-01 | 22010D | 7E0 | 7E8 | Ford vehicle speed candidate | Rejected | Stuck at 16 km/h | Live speed | Static/stale value. Not valid. |
| 2026-05-01 | 010D | 7E0 | 7E8 | Generic vehicle speed | Rejected / no useful value | No value shown | Live speed | Not useful in this test. |
| 2026-05-01 | 22010C | 7E0 | 7E8 | Ford RPM candidate | Rejected | 7,623 rpm | Diesel idle/drive RPM | Wrong scale or wrong PID. |
| 2026-05-01 | 0111 | 7E0 | 7E8 | Generic throttle position | Rejected | Negative response / out of range | Live throttle % | Drop. |
| 2026-05-01 | 22164F | 7E0 | 7E8 | Fuel rail pressure candidate | Rejected | Negative response / out of range | Diesel fuel rail pressure | Drop. |
| 2026-05-01 | 221674 | 7E1 | 7E9 | Transmission temp alternate | Rejected | Negative response / out of range | Transmission temp | Drop. |
| 2026-05-01 | 225A7A | 7E0 | 7E8 | PCM gear candidate | Rejected | Negative response / out of range | Current gear | TCM gear PIDs work instead. |
| 2026-05-01 | 221E1B | 7E1 | 7E9 | Transmission output shaft speed | Rejected | Negative response / out of range | Shaft speed | Drop. |
| 2026-05-01 | 22042C | 7E0 | 7E8 | DPF soot mass experimental | Rejected | Negative response / out of range | DPF soot/fullness | Drop. |
| 2026-05-01 | 221440 | 7E0 | 7E8 | MAP candidate | Rejected | Negative response / out of range | MAP / boost | Drop. |
| 2026-05-01 | 221442 | 7E0 | 7E8 | BARO candidate | Rejected | Negative response / out of range | Barometric pressure | Drop. |
| 2026-05-01 | 2203CA | 7E0 | 7E8 | Intake air temp 2 | Rejected | Negative response / out of range | Intake temp | Drop. |
| 2026-05-01 | 220415 | 7E0 | 7E8 | Oil pressure candidate | Rejected | Negative response / out of range | Oil pressure | Drop. |
| 2026-05-01 | 224029 | 726 | 72E | Battery temperature estimated | Rejected | Negative response / out of range | Battery temp | Drop. |

## Still unresolved / wanted

| Target | Status | Notes |
|---|---|---|
| Dash-matching vehicle speed | Partly found | `22F40D` works and updates quickly, but reads about 4 km/h under dash. GPS comparison still needed. |
| DPF full % matching dash | Not found | Dash fullness still not matched. `0579`, `057B`, `055D`, and `042C` are not reliable dash-fullness PIDs. |
| DPF regeneration state | Not found | New high-priority research target after FORScan exhaust-filter-cleaning clue. |
| Exhaust filter cleaning state | Research target | FORScan PCM `7E0-162-02` suggests manual/auto exhaust-filter-cleaning strategy exists as config on related platform. Need live PID, not config address. |
| True boost pressure | Partly found | MAP absolute `22F40B` works. Need clean BARO or calculated boost method. |
| Fuel rail pressure actual | Partly found | Desired rail pressure and sensor voltage respond. Actual high-pressure rail value still needs confirmation. |
| Oil pressure | Not solved / archive only | IPC oil-pressure display clue exists, but `FORD_EOP` / 7DF / `22F45C` raw values are not scaled/confirmed. Keep out of active production JSON. |
| Engine hours / idle hours | Research target | FORScan IPC engine-hour and idle-hour function clues make this worth a future test bucket. |
| Battery age unit | Unclear | Raw/scalar is useful; hours interpretation remains lower-confidence. |
| Factory TPMS tyre temperature | Research target / low priority | FORScan IPC tyre-temp display clue exists, but Oricom aftermarket TPMS will not feed Ford factory module data. |

## Current recommended default.json status

Keep in active test file:
- Confirmed / working signals
- Transmission temp comparison pair
- Generic RPM / MAP / engine load / fuel pressure test candidates only if still desired

Remove from active test file:
- All rejected PIDs
- AdBlue % and AdBlue mm
- Static Ford speed PID
- Bad Ford RPM PID
- DPF 0579 / 057B as dash fullness candidates
