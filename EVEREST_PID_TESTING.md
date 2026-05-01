# Ford Everest MY25.25 PID Testing Log

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
| Dash-matching vehicle speed | Not found | Need ABS / BCM / IPC candidate. |
| RPM | Not confirmed | Generic 010C still needs specific confirmation. Ford 22010C rejected. |
| DPF full % matching dash | Not found | Dash showed 65 % full; 0579 and 057B did not match. |
| Boost / MAP | Not found | 1440 and 1442 rejected. Generic 010B still needs test. |
| Fuel rail pressure | Not found | 164F rejected. Need better diesel rail pressure PID. |
| Oil pressure | Not found | 0415 rejected. |
| Throttle / pedal position | Not found | Generic 0111 rejected. Diesel may need pedal-specific PID instead. |

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
