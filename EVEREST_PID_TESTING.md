# Ford Everest MY25.25 PID Testing Log

Vehicle:
- Ford Everest Trend
- MY25.25
- Ford Everest UB / Next Gen
- Ford Everest UB / U704
- Third-generation Everest, introduced 2022
- Australian UB series
- Platform related to Ford Ranger P703 / Next-Gen Ranger
- 2.0 L Bi-Turbo Diesel
- 10-speed automatic
- Australian market

PID donor priority:
- Ford Ranger P703 / Next-Gen Ranger data was preferred where exact Everest data was unavailable.
- Older Ranger PX/PX3 data was treated as lower-confidence and tested before use.
- Real Pelican / vehicle test results override donor data.
- Donor/imported PIDs are only promoted after they respond correctly on this vehicle.

Testing setup:
- App: Pelican on iOS / CarPlay
- OBD source: custom GitHub fork of OBDb/Ford-Everest
- Signal file: signalsets/v3/default.json
- Test date: 2026-05-01
- Vehicle tested with live screenshots and dash cross-checks where available

## Status key

| Status | Meaning |
|---|---|
| Confirmed | Works and has been cross-checked or behaves exactly as expected |
| Works | Responds and value is plausible |
| Works / test | Responds, but needs more driving or comparison before promotion |
| Wrong target | Responds, but does not represent the intended value |
| Rejected | No response, out of range, stale, impossible, wrong formula, or wrong scale |

---

## Confirmed / working

| Date | PID / cmd | Header | Response | Signal | Result | Value seen | Expected | Notes |
|---|---|---:|---:|---|---|---:|---|---|
| 2026-05-01 | 22F42F | 7E0 | 7E8 | Fuel remaining | Works | 78 % | Close to dash fuel | Ford/Ranger-style fuel PID. Formula A*100/255. |
| 2026-05-01 | 012F | 7E0 | 7E8 | Generic fuel level | Works | 77–78 % | Close to dash fuel | Generic SAE fuel level. Good backup/comparison. |
| 2026-05-01 | 01A6 | 7E0 | 7E8 | Odometer | Confirmed | 25,589 km | Dash 25,588.8 km | Matches dash when rounded. Formula raw*0.1. |
| 2026-05-01 | 22F405 | 7E0 | 7E8 | Coolant temperature | Works | 47–56 °C | Rises with warm-up | Matched Pelican built-in ECT at 47 °C. Formula A-40. |
| 2026-05-01 | 22F45C | 7E0 | 7E8 | Engine oil temperature | Works | 52 °C | Warms near/after coolant | Formula A-40. |
| 2026-05-01 | 22F446 | 7E0 | 7E8 | Ambient air temperature | Works | 30 °C | Close to dash ambient | Dash showed about 29 °C. Formula A-40. |
| 2026-05-01 | 22F40F | 7E0 | 7E8 | Intake air temperature | Works | 35–36 °C | Plausible vs ambient | Formula A-40. |
| 2026-05-01 | 22051C | 7E0 | 7E8 | Intercooler air charge temperature | Works | 33 °C | Plausible vs intake/ambient | Formula A-40. |
| 2026-05-01 | 220522 | 7E0 | 7E8 | Fuel temperature | Works | 30 °C | Plausible | Formula A-40. |
| 2026-05-01 | 221E12 | 7E1 | 7E9 | Transmission gear commanded | Confirmed | 1, 9 | Should match selected/current gear | TCM header. Formula A. |
| 2026-05-01 | 221E1F | 7E1 | 7E9 | Transmission gear engaged | Confirmed | 1, 9 | Should match actual engaged gear | TCM header. Formula A. |
| 2026-05-01 | 221E14 | 7E1 | 7E9 | Torque converter slip speed | Works | 800.25 rpm, then 1.25 rpm | High when unlocked, low when locked | Behaviour looks correct. Formula (A*256+B)/4. |
| 2026-05-01 | 221E1C | 7E1 | 7E9 | Transmission fluid temperature | Works / likely correct | 54.3 °C | Plausible after driving | Current best formula is ((A*256+B)*5/72)-17. Div8 rejected as wrong scale. |
| 2026-05-01 | 22F40C | 7E0 | 7E8 | Engine speed / RPM | Confirmed | 802.5 rpm | Diesel idle RPM | Formula raw/4. Replaces bad 22010C RPM candidate. |
| 2026-05-01 | 22F41F | 7E0 | 7E8 | Engine run time | Confirmed | 22 min 40 sec | Raw seconds | Response 0550 = 1360 seconds. |
| 2026-05-01 | 22F404 | 7E0 | 7E8 | Engine load | Works | 4 % | Engine load % | Formula A*100/255. Good connectable candidate. |
| 2026-05-01 | 22F462 | 7E0 | 7E8 | Actual engine percent torque | Works | 7 % | Engine torque % | Formula A-125. |
| 2026-05-01 | 22F463 | 7E0 | 7E8 | Engine reference torque | Confirmed | 500 N·m | 2.0 L Bi-Turbo expected torque | Strong match to vehicle spec. |
| 2026-05-01 | 220487 | 7E0 | 7E8 | AdBlue tank level | Works | 17 L | Close to full tank | Ford AU tank capacity approx. 18.5 L. 17 L ≈ 92 %. Formula (A*256+B)/1000. |
| 2026-05-01 | 220685 | 7E0 | 7E8 | AdBlue tank temperature | Works / plausible | 20 °C | Plausible fluid temp | Formula A-40. |
| 2026-05-01 | 22052E | 7E0 | 7E8 | EGR open percentage | Works | 21 % | Changes with engine/load | Formula A*100/255. |
| 2026-05-01 | 220569 | 7E0 | 7E8 | EGT pre-turbo | Works / plausible | 200 °C | Plausible exhaust temp | Formula (A*256+B)/32. |
| 2026-05-01 | 220551 | 7E0 | 7E8 | Alternator current output | Works / plausible | 32.5–61.2 A | Plausible charging current | Formula (A*256+B)/64. |
| 2026-05-01 | 22402A | 726 | 72E | Aux 12 V battery voltage | Works | 14 V | Charging voltage | Formula A/20+6. Good starter battery voltage candidate. |
| 2026-05-01 | 224028 | 726 | 72E | Aux 12 V battery SOC | Confirmed | 88–91 % | Battery SOC | Formula A. Do not use A*100/255. |
| 2026-05-01 | 22402B | 726 | 72E | Aux 12 V battery current | Works | 2–33 A | Battery current | Updated formula A-127. Earlier A-126 was superseded. |
| 2026-05-01 | 224027 | 726 | 72E | Aux 12 V battery age raw/scalar | Works | 150 | Battery age counter | Same raw PID also displayed as 3600 hours via raw*24. Unit still uncertain. |
| 2026-05-01 | 22054B | 7E0 | 7E8 | Oil life remaining | Works | 47 % | Ford oil life/service display | Formula A. Compare later against FordPass/dash if available. |
| 2026-05-01 | 22F431 | 7E0 | 7E8 | Distance since DTC clear | Works | 8,338 km | Diagnostic history | Formula raw km. |
| 2026-05-01 | 22F421 | 7E0 | 7E8 | Distance with MIL | Works | 0 km | 0 km if no MIL distance | Diagnostic counter. |
| 2026-05-01 | 22F430 | 7E0 | 7E8 | Warm-ups since DTC clear | Works / saturated | 255 | Diagnostic counter | May be saturated at 8-bit maximum. |
| 2026-05-01 | 2205FC | 7E0 | 7E8 | Cylinder head temperature sensor 2 | Works | 84.6 °C | Warm engine temp | Formula signed 16-bit /64. |
| 2026-05-01 | 22F40B | 7E0 | 7E8 | Manifold absolute pressure | Works | 15 psi | Around atmospheric at idle | Appears to be absolute MAP, not boost. Formula raw*10000/68947. |
| 2026-05-01 | 220301 | 7E0 | 7E8 | MAP sensor voltage | Works | 1.2 V | 0–5 V sensor | Useful for boost/MAP cross-checking. |
| 2026-05-01 | 2203BA | 7E0 | 7E8 | Ambient air temperature sensor voltage | Works | 2.81 V | 0–5 V sensor | Secondary sensor signal. |
| 2026-05-01 | 220307 | 7E0 | 7E8 | Low pressure fuel pump commanded duty cycle | Works | 30 % | Fuel pump duty % | Formula raw*0.0030517578. |
| 2026-05-01 | 220324 | 7E0 | 7E8 | High pressure fuel rail sensor voltage | Works | 0.94 V | 0–5 V sensor | Formula raw/204.6. |
| 2026-05-01 | 2203DC | 7E0 | 7E8 | High pressure fuel pressure desired | Works / clamp issue | 3500 psi shown | Diesel rail desired pressure | Source display was clamped at 3500 psi; raw suggests higher. Use higher max for testing. |
| 2026-05-01 | 22054D | 7E0 | 7E8 | Low fuel pressure sensor voltage | Works | 2.48 V | Plausible sensor voltage | Do not confuse with rejected AdBlue percent interpretation of same PID. |
| 2026-05-01 | 220548 | 7E0 | 7E8 | Low fuel pressure actual | Works | 80 psi | Should vary with fuel system demand | Formula raw*10000/137892. |
| 2026-05-01 | 22041F | 7E0 | 7E8 | Low fuel pressure desired | Works | 65 psi | Should track target fuel pressure | Note this is 22041F, not 22F41F engine runtime. |
| 2026-05-01 | 229800 | 7E0 | 7E8 | A/C compressor pressure sensor voltage | Works | 1.44 V | 0–5 V sensor | Formula raw/1024. |
| 2026-05-01 | 220707 | 7E0 | 7E8 | Grille shutter duty cycle | Works / test | 7 % | Should vary with warm-up/cooling demand | Formula A*100/255. Needs road/warm-up validation. |
| 2026-05-01 | 22032B | 7E0 | 7E8 | Accelerator pedal position | Works | 4 % | Pedal input % | Formula A*100/255. Replaces failed throttle attempts for driver input. |

---

## Working but needs more comparison / validation

| Date | PID / cmd | Header | Response | Signal | Result | Value seen | Expected | Notes |
|---|---|---:|---:|---|---|---:|---|---|
| 2026-05-01 | 221E1C | 7E1 | 7E9 | Transmission fluid temperature div16 | Works / demoted | 44.1–49.3 °C | Rises with warm-up | Earlier plausible, but 5/72 currently looks better after later comparison. Keep only if comparison desired. |
| 2026-05-01 | 010B | 7E0 | 7E8 | Generic MAP test | Responds / uncertain | 15 | MAP absolute | Unit/scale uncertain. F40B is now the better MAP candidate. |
| 2026-05-01 | 220604 | 7E0 | 7E8 | A/C refrigerant pressure | Works / scaling uncertain | 128 shown | A/C pressure | Responds, but raw/display scaling needs cross-check. |
| 2026-05-01 | 220700 | 7E0 | 7E8 | Intake temperature fault | Works / diagnostic | 0 | Likely 0 = no fault | Diagnostic flag/status only. |
| 2026-05-01 | 221279 | 7E0 | 7E8 | Intake temperature sensor voltage | Questionable | 0 V | Sensor voltage should normally be non-zero | Responds, but value suspicious. Test only. |
| 2026-05-01 | 22F45C | 7DF | — | Engine oil pressure raw | Works / scaling uncertain | 119 | Should vary with engine state | Responds, but unit/scaling unclear. Test raw only. |
| 2026-05-01 | 220462 | 7E0 | 7E8 | Wastegate duty cycle | Questionable | 0 % | Should vary under load if valid | Possible PID/name/formula mismatch. Do not confuse with F462 torque. |

---

## Responds but not useful as intended

| Date | PID / cmd | Header | Response | Signal | Result | Value seen | Expected | Notes |
|---|---|---:|---:|---|---|---:|---|---|
| 2026-05-01 | 220579 | 7E0 | 7E8 | DPF soot blockage closed loop | Wrong target | 12 % | Dash DPF fullness was 65 % | Not dash DPF full %. May be another internal DPF value. |
| 2026-05-01 | 22057B | 7E0 | 7E8 | DPF soot blockage open loop | Wrong target | 96 % | Dash DPF fullness was 65 % | Not dash DPF full %. May be another internal DPF value. |
| 2026-05-01 | 22055D | 7E0 | 7E8 | DPF soot load alternate | Wrong target | 100 % | Dash DPF fullness was 80 % | Responds but does not match dash DPF fullness. |
| 2026-05-01 | 220683 | 7E0 | 7E8 | AdBlue tank level mm | Responds, uncertain | 100 mm | Dash showed full / 9000 km range | Not useful as main AdBlue level. |
| 2026-05-01 | 220684 | 7E0 | 7E8 | AdBlue tank level percentage | Wrong meaning / scale | 33 % | Tank recently filled; dash showed full | Reject as AdBlue %. Litres PID is better. |
| 2026-05-01 | 22054D | 7E0 | 7E8 | AdBlue level percent test | Wrong meaning / scale | 4 % | Tank recently filled/full | Same PID is useful as low fuel pressure sensor voltage with different interpretation. Not AdBlue %. |
| 2026-05-01 | 224028 | 726 | 72E | Battery charge formula test | Wrong formula | 36 % | Battery SOC around 91 % | Same raw PID as SOC. Formula A*100/255 is wrong for this Everest; use A. |
| 2026-05-01 | 2203F4 | 7E0 | 7E8 | EGT post turbo old formula | Suspicious | 255 °C | Should vary | Same fixed value as other EGTs; likely wrong formula/default. |
| 2026-05-01 | 2203F5 | 7E0 | 7E8 | EGT pre DPF old formula | Suspicious | 255 °C | Should vary | Same fixed value as other EGTs; likely wrong formula/default. |
| 2026-05-01 | 2203F6 | 7E0 | 7E8 | EGT post DPF old formula | Suspicious | 255 °C | Should vary | Same fixed value as other EGTs; likely wrong formula/default. |

---

## Rejected / no useful response

| Date | PID / cmd | Header | Response | Signal | Result | Value seen | Expected | Notes |
|---|---|---:|---:|---|---|---|---|---|
| 2026-05-01 | 22010D | 7E0 | 7E8 | Ford vehicle speed candidate | Rejected | Stuck at 16 km/h | Live speed | Static/stale value. Not valid. |
| 2026-05-01 | 010D | 7E0 | 7E8 | Generic vehicle speed forced 7E0 | Rejected / no useful value | No value shown | Live speed | Earlier test not useful. Retest no-header and F40D still planned. |
| 2026-05-01 | 22010C | 7E0 | 7E8 | Ford RPM candidate | Rejected | 7,623 rpm | Diesel idle/drive RPM | Wrong scale or wrong PID. Working RPM is F40C. |
| 2026-05-01 | 0111 | 7E0 | 7E8 | Generic throttle position | Rejected | Negative response / out of range | Live throttle % | Drop. |
| 2026-05-01 | 220330 | 7E0 | 7E8 | Throttle position candidate | Rejected | Negative response / out of range | Throttle % | Accelerator pedal 032B works instead. |
| 2026-05-01 | 220335 | 7E0 | 7E8 | EGR duty cycle | Rejected | Negative response / out of range | EGR % | Working EGR signal is 052E. |
| 2026-05-01 | 22164F | 7E0 | 7E8 | Fuel rail pressure candidate | Rejected | Negative response / out of range | Diesel fuel rail pressure | Drop. |
| 2026-05-01 | 22016F | 7E0 | 7E8 | Fuel rail pressure candidate | Rejected | Negative response / out of range | Diesel fuel rail pressure | Drop. |
| 2026-05-01 | 221674 | 7E1 | 7E9 | Transmission temp alternate | Rejected | Negative response / out of range | Transmission temp | Drop. |
| 2026-05-01 | 221E1C | 7E1 | 7E9 | Transmission fluid temperature div8 | Rejected | 128.3 °C | Normal transmission temp | Wrong scale. Drop. |
| 2026-05-01 | 225A7A | 7E0 | 7E8 | PCM gear candidate | Rejected | Negative response / out of range | Current gear | TCM gear PIDs work instead. |
| 2026-05-01 | 221E1B | 7E1 | 7E9 | Transmission output shaft speed | Rejected | Negative response / out of range | Shaft speed | Drop. |
| 2026-05-01 | 221E0B | 7E1 | 7E9 | Output shaft speed RPM test | Rejected | Negative response / out of range | Output shaft speed | Drop. |
| 2026-05-01 | 22042C | 7E0 | 7E8 | DPF soot mass experimental | Rejected | Negative response / out of range | DPF soot/fullness | Drop. |
| 2026-05-01 | 22042C | 7E0 | 7E8 | DPF soot retest div100 | Rejected | Negative response / out of range | Dash DPF fullness | Drop. |
| 2026-05-01 | 221440 | 7E0 | 7E8 | MAP candidate | Rejected | Negative response / out of range | MAP / boost | Drop. |
| 2026-05-01 | 221442 | 7E0 | 7E8 | BARO candidate | Rejected | Negative response / out of range | Barometric pressure | Drop. |
| 2026-05-01 | 220333 | 7E0 | 7E8 | Barometric pressure candidate | Rejected | 0 | Around 100 kPa | Invalid value. |
| 2026-05-01 | 2203CA | 7E0 | 7E8 | Intake air temp 2 | Rejected | Negative response / out of range | Intake temp | Drop. |
| 2026-05-01 | 22050D | 7E0 | 7E8 | Intercooler temp candidate | Rejected | Negative response / out of range | Air charge temp | Working intercooler temp is 051C. |
| 2026-05-01 | 220415 | 7E0 | 7E8 | Oil pressure candidate | Rejected | Negative response / out of range | Oil pressure | Drop. |
| 2026-05-01 | 221315 | 7E0 | 7E8 | Engine oil pressure candidate | Rejected | Negative response / out of range | Oil pressure | Drop. |
| 2026-05-01 | 221310 | 7E0 | 7E8 | Engine oil temperature 1310 | Rejected | Negative response / out of range | Oil temp | Working oil temp is F45C. |
| 2026-05-01 | 2203E0 | 7E0 | 7E8 | Torque at clutch candidate | Rejected | Negative response / out of range | Torque | Drop. |
| 2026-05-01 | 2203E5 | 7E0 | 7E8 | VGT actuator position | Rejected | Negative response / out of range | VGT % | Drop. |
| 2026-05-01 | 224029 | 726 | 72E | Battery temperature estimated | Rejected | Negative response / out of range | Battery temp | Drop. |
| 2026-05-01 | 224028 | 7E0 | 7E8 | Battery SOC test 7E0 | Rejected | Negative response / out of range | Battery SOC | Working SOC is 726 / 4028. |
| 2026-05-01 | 224029 | 7E0 | 7E8 | Battery current test 7E0 | Rejected | Negative response / out of range | Battery current | Working current is 726 / 402B. |
| 2026-05-01 | 22402B | 7E0 | 7E8 | Battery temperature test 7E0 | Rejected | Negative response / out of range | Battery temp | Drop. |
| 2026-05-01 | 22097C | 7E0 | 7E8 | Alternator voltage desired | Rejected | Negative response / out of range | Voltage | Drop. |
| 2026-05-01 | 221172 | 7E0 | 7E8 | Module supply voltage | Rejected | Negative response / out of range | Voltage | Drop. |
| 2026-05-01 | 2203F4 | 7E0 | 7E8 | EGT 11 retest /10 | Rejected | Not enough data; data 15 | EGT °C | Returned 1 byte only; 16-bit /10 formula invalid. |
| 2026-05-01 | 2203F5 | 7E0 | 7E8 | EGT 12 retest /10 | Rejected | Not enough data; data 14 | EGT °C | Returned 1 byte only; 16-bit /10 formula invalid. |
| 2026-05-01 | 2203F6 | 7E0 | 7E8 | EGT 13 retest /10 | Rejected | Not enough data; data 14 | EGT °C | Returned 1 byte only; 16-bit /10 formula invalid. |
| 2026-05-01 | 220483 | 7E0 | 7E8 | Turbo boost pressure candidate | Rejected | Not enough data; data 05 | Boost/MAP | Returned 1 byte only; 16-bit formula invalid. |
| 2026-05-01 | 22116E | 720 | 728 | Fuel level litres IPC test | Rejected / no value | Blank | Fuel litres | Drop unless later response appears. |
| 2026-05-01 | 22116E | 720 | 728 | Fuel tank percent IPC test | Rejected / no value | Blank | Fuel % | Working fuel is F42F / generic 012F. |
| 2026-05-01 | 22DD01 | 720 | 728 | Odometer IPC DD01 | Rejected / no value | Blank | Dash odometer | Working odometer is 01A6. |
| 2026-05-01 | 22DD04 | 720 | 728 | IPC display speed | Rejected / no value | Blank | Dash speed | Drop unless later value appears. |
| 2026-05-01 | 22DD05 | 720 | 728 | IPC corrected speed | Rejected / no value | Blank | Dash speed | Drop unless later value appears. |
| 2026-05-01 | 223301 | 760 | 768 | ABS average wheel speed | Rejected | Negative response / out of range | Vehicle speed | Drop. |
| 2026-05-01 | 224801 | 760 | 768 | ABS vehicle speed | Rejected | Negative response / out of range | Vehicle speed | Drop. |
| 2026-05-01 | 224B01 | 760 | 768 | Wheel speed LF | Rejected | Negative response / out of range | Wheel speed | Drop. |
| 2026-05-01 | 224B02 | 760 | 768 | Wheel speed RF | Rejected | Negative response / out of range | Wheel speed | Drop. |
| 2026-05-01 | 224B03 | 760 | 768 | Wheel speed LR | Rejected | Negative response / out of range | Wheel speed | Drop. |
| 2026-05-01 | 224B04 | 760 | 768 | Wheel speed RR | Rejected | Negative response / out of range | Wheel speed | Drop. |
| 2026-05-01 | 221317 | 7E0 | 7E8 | PCM VSS alt | Rejected | Negative response / out of range | Vehicle speed | Drop. |
| 2026-05-01 | 22117E | 7E0 | 7E8 | PCM VSS high resolution | Rejected | Negative response / out of range | Vehicle speed | Drop. |
| 2026-05-01 | 22117C | 7E0 | 7E8 | PCM VSS internal | Rejected | Negative response / out of range | Vehicle speed | Drop. |

---

## Speedometer candidates still to test

All live speed candidates should be grouped under:

`Movement.Everest.SpeedTest`

This allows the values to be viewed together while driving.

| Date | PID / cmd | Header | Response | Signal | Result | Value seen | Expected | Notes |
|---|---|---:|---:|---|---|---|---|---|
| 2026-05-01 | 010D | — | — | Generic OBD vehicle speed, no header | To test | — | Live dash speed in km/h | Best first retest. |
| 2026-05-01 | 010D | 7E0 | 7E8 | Generic OBD vehicle speed forced PCM | Retest | — | Live dash speed in km/h | Earlier forced/header test was not useful; no-header version may behave differently. |
| 2026-05-01 | 22F40D | 7E0 | 7E8 | Ford extended vehicle speed | To test | — | Live dash speed in km/h | Promising Mode 22 Ford speed candidate. |
| 2026-05-01 | 221E15 | 7E1 | 7E9 | Output shaft speed OSS | To test | — | Changes with road speed | Raw rpm-style value; not direct dash speed. |
| 2026-05-01 | 221E16 | 7E1 | 7E9 | Input shaft speed ISS/TSS | To test | — | Changes with engine/transmission speed | Diagnostic only, not direct road speed. |
| 2026-05-01 | 222858 | 7A0 | — | Wheel speed front-left ABS | To test | — | Live wheel speed in km/h | Header may vary. |
| 2026-05-01 | 222859 | 7A0 | — | Wheel speed front-right ABS | To test | — | Live wheel speed in km/h | Header may vary. |
| 2026-05-01 | 22285A | 7A0 | — | Wheel speed rear-left ABS | To test | — | Live wheel speed in km/h | Header may vary. |
| 2026-05-01 | 22285B | 7A0 | — | Wheel speed rear-right ABS | To test | — | Live wheel speed in km/h | Header may vary. |
| 2026-05-01 | 22A210 | 7E0 | 7E8 | Vehicle speed setting VSSET | Low priority | — | Cruise/set speed, not live speed | Test only if needed. |

---

## Still unresolved / wanted

| Target | Status | Notes |
|---|---|---|
| Dash-matching vehicle speed | Not found | Ultimate9 Codebreaker proved a working OBD speed source exists. New speed candidates added for retest. |
| DPF full % matching dash | Not found | Dash showed 65–80 % full during tests; 0579, 057B, 055D and 042C did not match. |
| True boost pressure | Partly found | MAP absolute pressure F40B works. Need a working BARO or calculated boost strategy. |
| Fuel rail pressure actual | Partly found | Desired pressure and sensor voltage respond. Actual high-pressure rail pressure still needs a clean confirmed pressure PID. |
| Oil pressure | Raw only | 7DF / F45C responds with raw 119, but scaling/unit not confirmed. |

---

## Current recommended default.json status

Keep in active file:
- Confirmed / working signals
- Current best transmission temperature formula: 1E1C 5/72
- Useful test-only signals that respond
- New speedometer candidates grouped under Movement.Everest.SpeedTest

Remove from active file:
- All rejected PIDs
- Wrong-scale AdBlue percentage PIDs
- Wrong-scale battery charge formula
- Static Ford speed PID
- Bad Ford RPM PID
- DPF 0579 / 057B / 055D / 042C as dash fullness candidates
- Failed old ABS / IPC / PCM speed candidates
- Failed 7E0 electrical comparison candidates

---

## Pull request / upstream notes

Strong candidates for future upstream contribution:
- Fuel remaining F42F
- Odometer 01A6
- Coolant temperature F405
- Engine oil temperature F45C
- Ambient temperature F446
- Intake air temperature F40F
- Fuel temperature 0522
- Transmission gear commanded 1E12
- Transmission gear engaged 1E1F
- Transmission fluid temperature 1E1C using 5/72 formula
- Torque converter slip 1E14
- AdBlue level litres 0487
- AdBlue tank temperature 0685
- EGR open 052E
- Alternator current 0551
- Aux 12 V battery voltage 402A
- Aux 12 V battery SOC 4028
- Aux 12 V battery current 402B using A-127
- Engine RPM F40C
- Engine run time F41F
- Engine reference torque F463
- Oil life remaining 054B

Keep out of upstream production JSON until solved:
- Dash-matching vehicle speed
- DPF fullness percentage
- True boost pressure
- Actual high-pressure rail pressure
- Oil pressure scaling
