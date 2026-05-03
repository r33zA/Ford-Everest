# Ford Everest MY25.25 PID Testing Log

Last updated: 2026-05-04

Update focus:
- Removed the bad active JSON entry `EVEREST_ENGINE_OIL_PRESSURE_7DF_F45C`.
- Kept `FORD_EOP` / 7DF / 22F45C as archive-only raw evidence, not an active default.json signal.
- Moved the latest responding non-speed items out of testing where they are now useful enough to keep.
- Added the newer 760 / 768 wheel-speed and lateral-movement candidates as test-only; they responded while stationary, but still need a driving test.
- Kept speed-related live comparisons grouped under `Movement.Everest.SpeedTest`.


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
- Test vehicle: Ford Everest MY25.25 Trend 2.0 L Bi-Turbo Diesel
- Test date: 2026-05-01
- Vehicle tested with live screenshots and dash cross-checks where available

---

## Status key

| Status | Meaning |
|---|---|
| Confirmed | Works and has been cross-checked or behaves exactly as expected |
| Works | Responds and value is plausible |
| Works / test | Responds, but needs more driving or comparison before promotion |
| Wrong target | Responds, but does not represent the intended value |
| Rejected | No response, out of range, stale, impossible, wrong formula, or wrong scale |
| Archive only | Responded once or more, but should not be kept in active default.json |

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
| 2026-05-01 | 221E14 | 7E1 | 7E9 | Torque converter slip speed | Works | 800.25 rpm, 428.75 rpm, 1.25 rpm | High when unlocked, low when locked | Behaviour looks correct. Formula (A*256+B)/4. |
| 2026-05-01 | 221E1C | 7E1 | 7E9 | Transmission fluid temperature | Works / likely correct | 50.4–54.3 °C | Plausible after driving | Current best formula is ((A*256+B)*5/72)-17. Div8 rejected as wrong scale. |
| 2026-05-01 | 22F40C | 7E0 | 7E8 | Engine speed / RPM | Confirmed | 802.5 rpm | Diesel idle RPM | Formula raw/4. Replaces bad 22010C RPM candidate. |
| 2026-05-01 | 22F41F | 7E0 | 7E8 | Engine run time | Confirmed | 22 min 40 sec | Raw seconds | Response 0550 = 1360 seconds. |
| 2026-05-01 | 22F404 | 7E0 | 7E8 | Engine load | Works | 4 % | Engine load % | Formula A*100/255. Good connectable candidate. |
| 2026-05-01 | 22F462 | 7E0 | 7E8 | Actual engine percent torque | Works | 7 % | Engine torque % | Formula A-125. |
| 2026-05-01 | 22F463 | 7E0 | 7E8 | Engine reference torque | Confirmed | 500 N·m | 2.0 L Bi-Turbo expected torque | Strong match to vehicle spec. |
| 2026-05-01 | 220487 | 7E0 | 7E8 | AdBlue tank level litres | Works | 17 L | Close to full tank | Ford AU tank capacity approx. 18.5 L. 17 L ≈ 92 %. Formula raw/1000. |
| 2026-05-01 | 220685 | 7E0 | 7E8 | AdBlue tank temperature | Works / plausible | 20 °C | Plausible fluid temp | Formula A-40. |
| 2026-05-01 | 22052E | 7E0 | 7E8 | EGR open percentage | Works | 21 % | Changes with engine/load | Formula A*100/255. |
| 2026-05-01 | 220569 | 7E0 | 7E8 | EGT pre-turbo | Works / plausible | 200 °C | Plausible exhaust temp | Formula raw/32. |
| 2026-05-01 | 220551 | 7E0 | 7E8 | Alternator current output | Works / plausible | 32.5–61.2 A | Plausible charging current | Formula raw/64. |
| 2026-05-01 | 22402A | 726 | 72E | Aux 12 V battery voltage | Works | 14 V | Charging voltage | Formula A/20+6. Good starter battery voltage candidate. |
| 2026-05-01 | 224028 | 726 | 72E | Aux 12 V battery SOC | Confirmed | 88–91 % | Battery SOC | Formula A. Do not use A*100/255. |
| 2026-05-01 | 22402B | 726 | 72E | Aux 12 V battery current | Works | 2–33 A | Battery current | Formula A-127. Earlier A-126 was superseded. |
| 2026-05-01 | 224027 | 726 | 72E | Aux 12 V battery age raw/scalar | Works | 150–151 | Battery age counter | Raw/scalar value appears useful. Separate from hours interpretation. |
| 2026-05-01 | 224027 | 726 | 72E | Aux 12 V battery age hours | Works / uncertain unit | 3600 hrs | Battery age estimate | Formula raw*24. Unit needs more confidence before upstream. |
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
| 2026-05-01 | 22032B | 7E0 | 7E8 | Accelerator pedal position | Works | 0–4 % | Pedal input % | Formula A*100/255. Replaces failed throttle attempts for driver input. |
| 2026-05-01 | 22F40D | 7E0 | 7E8 | Vehicle speed Ford extended | Works / best current speed | 76 km/h | Live dash speed | Updates quickly. Consistently about 4 km/h under dash. Best current OBD speed candidate. |
| 2026-05-04 | 221E1C | 7E1 | 7E9 | Transmission fluid temperature 5/72 compare | Works / keep | 46 °C | Plausible transmission temp | Latest retest still behaves plausibly. Keep current 5/72 formula as active best. |
| 2026-05-04 | 224027 | 726 | 72E | Battery age scalar | Works / keep | 153 | Raw battery age scalar | Responds consistently. Keep scalar version; hours interpretation remains lower-confidence. |
| 2026-05-04 | 224028 | 726 | 72E | Battery charge / raw SOC comparison | Works / context-only | 32 % | Battery display comparison | This appears to be the divided/alternate charge interpretation, not the confirmed starter-battery SOC. Do not replace confirmed SOC formula A. |
| 2026-05-04 | 010B | 7E0 | 7E8 | Generic MAP test | Works / low-priority | 15 | MAP absolute comparison | Responds and aligns broadly with MAP absolute. F40B remains preferred Ford-specific MAP. |
| 2026-05-04 | 220700 | 7E0 | 7E8 | Intake temperature fault | Works / diagnostic | 0 | No fault flag | Stable diagnostic flag. Keep as diagnostic/status item, not a temperature value. |

---

## Working but needs more comparison / validation

| Date | PID / cmd | Header | Response | Signal | Result | Value seen | Expected | Notes |
|---|---|---:|---:|---|---|---:|---|---|
| 2026-05-01 | 221E1C | 7E1 | 7E9 | Transmission fluid temperature div16 | Works / demoted | 44.1–49.3 °C | Rises with warm-up | Earlier plausible, but 5/72 currently looks better after later comparison. Keep only if comparison desired. |
| 2026-05-01 | 010B | 7E0 | 7E8 | Generic MAP test | Responds / uncertain | 15 | MAP absolute | Unit/scale uncertain. F40B is now the better MAP candidate. |
| 2026-05-01 | 220604 | 7E0 | 7E8 | A/C refrigerant pressure | Works / scaling uncertain | 128 shown | A/C pressure | Responds, but raw/display scaling needs cross-check. |
| 2026-05-01 | 220700 | 7E0 | 7E8 | Intake temperature fault | Works / diagnostic | 0 | Likely 0 = no fault | Diagnostic flag/status only. |
| 2026-05-01 | 221279 | 7E0 | 7E8 | Intake temperature sensor voltage | Questionable | 0 V | Sensor voltage should normally be non-zero | Responds, but value suspicious. Test only. |
| 2026-05-01 | 22F45C | 7DF | — | Engine oil pressure raw / FORD_EOP | Archive only | 119–127 raw | Should vary with engine state | `EVEREST_ENGINE_OIL_PRESSURE_7DF_F45C` removed from active JSON. Keep only as evidence; scaling/unit not confirmed. |
| 2026-05-01 | 220462 | 7E0 | 7E8 | Wastegate duty cycle | Questionable | 0 % | Should vary under load if valid | Possible PID/name/formula mismatch. Do not confuse with F462 torque. |
| 2026-05-01 | 221E15 | 7E1 | 7E9 | Output shaft speed OSS | Works / diagnostic | 1,025.5 rpm | Changes with road speed | Useful transmission/road movement signal, but not direct dash speed without conversion. |
| 2026-05-01 | 221E16 | 7E1 | 7E9 | Input shaft speed ISS/TSS | Works / diagnostic | 705.75 rpm | Changes with drivetrain state | Useful diagnostic value, not direct vehicle speed. |
| 2026-05-01 | 010D | — | — | Vehicle speed standard OBD2 | To retest | — | Live dash speed in km/h | Keep as comparison candidate. |
| 2026-05-01 | 010D | 7E0 | 7E8 | Vehicle speed standard OBD2 forced PCM | To retest | — | Live dash speed in km/h | Earlier forced/header test was not useful. |
| 2026-05-04 | 222B07 | 760 | 768 | Front left wheel speed | Works / test | 0 km/h | Live wheel speed | Responded while stationary. Needs driving test before promotion. Formula currently raw km/h-style, clamp 0–200 km/h. |
| 2026-05-04 | 222B06 | 760 | 768 | Front right wheel speed | Works / test | 0 km/h | Live wheel speed | Responded while stationary. Needs driving test before promotion. Formula currently raw km/h-style, clamp 0–200 km/h. |
| 2026-05-04 | 222B09 | 760 | 768 | Rear left wheel speed | Works / test | 0 km/h | Live wheel speed | Responded while stationary. Needs driving test before promotion. Formula currently raw km/h-style, clamp 0–200 km/h. |
| 2026-05-04 | 222B08 | 760 | 768 | Rear right wheel speed | Works / test | 0 km/h | Live wheel speed | Responded while stationary. Needs driving test before promotion. Formula currently raw km/h-style, clamp 0–200 km/h. |
| 2026-05-04 | 222B0C | 760 | 768 | Lateral angle | Works / test | -1 deg | Vehicle lateral angle | Responded plausibly while parked. Needs movement/level-ground validation. |
| 2026-05-04 | 222B11 | 760 | 768 | Lateral acceleration | Works / test | -0.14 | Lateral acceleration | Responded plausibly while parked. Needs cornering/straight-road validation. |
| 2026-05-01 | 222858 | 7A0 | — | Wheel speed front-left ABS | To retest | — | Live wheel speed in km/h | Header may vary. |
| 2026-05-01 | 222859 | 7A0 | — | Wheel speed front-right ABS | To retest | — | Live wheel speed in km/h | Header may vary. |
| 2026-05-01 | 22285A | 7A0 | — | Wheel speed rear-left ABS | To retest | — | Live wheel speed in km/h | Header may vary. |
| 2026-05-01 | 22285B | 7A0 | — | Wheel speed rear-right ABS | To retest | — | Live wheel speed in km/h | Header may vary. |

---

## Responds but not useful as intended

| Date | PID / cmd | Header | Response | Signal | Result | Value seen | Expected | Notes |
|---|---|---:|---:|---|---|---:|---|---|
| 2026-05-01 | 220579 | 7E0 | 7E8 | DPF soot blockage closed loop | Wrong target | 12 % | Dash DPF fullness was 65 % | Not dash DPF full %. May be another internal DPF value. |
| 2026-05-01 | 22057B | 7E0 | 7E8 | DPF soot blockage open loop | Wrong target | 96 % | Dash DPF fullness was 65 % | Not dash DPF full %. May be another internal DPF value. |
| 2026-05-01 | 22055D | 7E0 | 7E8 | DPF soot load alternate | Wrong target | 100 % | Dash DPF showed 80 % and later full | Responds but does not reliably match dash DPF fullness. |
| 2026-05-01 | 220683 | 7E0 | 7E8 | AdBlue tank level mm | Responds, uncertain | 100 mm | Dash showed full / 9000 km range | Not useful as main AdBlue level. |
| 2026-05-01 | 220684 | 7E0 | 7E8 | AdBlue tank level percentage | Wrong meaning / scale | 33 % | Tank recently filled; dash showed full | Reject as AdBlue %. Litres PID is better. |
| 2026-05-01 | 22054D | 7E0 | 7E8 | AdBlue level percent test | Wrong meaning / scale | 4 % | Tank recently filled/full | Same PID is useful as low fuel pressure sensor voltage with different interpretation. Not AdBlue %. |
| 2026-05-01 | 224028 | 726 | 72E | Battery charge formula test | Wrong formula | 33–36 % | Battery SOC around 88–91 % | Same raw PID as SOC. Formula A*100/255 is wrong for this Everest; use A. |
| 2026-05-01 | 2203F4 | 7E0 | 7E8 | EGT post turbo old formula | Suspicious | 255 °C | Should vary | Same fixed value as other EGTs; likely wrong formula/default. |
| 2026-05-01 | 2203F5 | 7E0 | 7E8 | EGT pre DPF old formula | Suspicious | 255 °C | Should vary | Same fixed value as other EGTs; likely wrong formula/default. |
| 2026-05-01 | 2203F6 | 7E0 | 7E8 | EGT post DPF old formula | Suspicious | 255 °C | Should vary | Same fixed value as other EGTs; likely wrong formula/default. |
| 2026-05-01 | 22A210 | 7E0 | 7E8 | Vehicle speed setting VSSET | Wrong target / low priority | — | Live speed | Likely cruise/set speed, not live vehicle speed. |

---

## Rejected / no useful response

| Date | PID / cmd | Header | Response | Signal | Result | Value seen | Expected | Notes |
|---|---|---:|---:|---|---|---|---|---|
| 2026-05-01 | 22010D | 7E0 | 7E8 | Ford vehicle speed candidate | Rejected | Stuck at 16 km/h | Live speed | Static/stale value. Not valid. |
| 2026-05-01 | 22010C | 7E0 | 7E8 | Ford RPM candidate | Rejected | 7,623 rpm | Diesel idle/drive RPM | Wrong scale or wrong PID. Working RPM is F40C. |
| 2026-05-01 | 0111 | 7E0 | 7E8 | Generic throttle position | Rejected | Negative response / out of range | Live throttle % | Drop. |
| 2026-05-01 | 220330 | 7E0 | 7E8 | Throttle position candidate | Rejected | Negative response / out of range | Throttle % | Accelerator pedal 032B works instead. |
| 2026-05-01 | 220335 | 7E0 | 7E8 | EGR duty cycle | Rejected | Negative response / out of range | EGR % | Working EGR signal is 052E. |
| 2026-05-01 | 22164F | 7E0 | 7E8 | Fuel rail pressure candidate | Rejected | Negative response / out of range | Diesel fuel rail pressure | Drop. |
| 2026-05-01 | 22016F | 7E0 | 7E8 | Fuel rail pressure candidate | Rejected | Negative response / out of range | Diesel fuel rail pressure | Drop. |
| 2026-05-01 | 221674 | 7E1 | 7E9 | Transmission temp alternate | Rejected | Negative response / out of range | Transmission temp | Drop. |
| 2026-05-01 | 221E1C | 7E1 | 7E9 | Transmission fluid temperature div8 | Rejected | 121.4–128.3 °C | Normal transmission temp | Wrong scale. Drop. |
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
| 2026-05-01 | 2203F4 | 7E0 | 7E8 | EGT 11 retest /10 | Rejected | Not enough data; data 15/20 | EGT °C | Returned 1 byte only; 16-bit /10 formula invalid. |
| 2026-05-01 | 2203F5 | 7E0 | 7E8 | EGT 12 retest /10 | Rejected | Not enough data; data 14/20 | EGT °C | Returned 1 byte only; 16-bit /10 formula invalid. |
| 2026-05-01 | 2203F6 | 7E0 | 7E8 | EGT 13 retest /10 | Rejected | Not enough data; data 14/20 | EGT °C | Returned 1 byte only; 16-bit /10 formula invalid. |
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
| 2026-05-01 | 22117D | 7E0 | 7E8 | PCM VSS sensor | Rejected | Negative response / out of range | Vehicle speed | Drop. |

---

## Speedometer candidates / results

All live speed candidates should be grouped under:

`Movement.Everest.SpeedTest`

This allows the values to be viewed together while driving.

| Date | PID / cmd | Header | Response | Signal | Result | Value seen | Expected | Notes |
|---|---|---:|---:|---|---|---|---|---|
| 2026-05-01 | 22F40D | 7E0 | 7E8 | Vehicle speed Ford extended | Works / best current | 76 km/h | Live dash speed in km/h | Updates quickly. Consistently about 4 km/h under dashboard speed. |
| 2026-05-04 | 22F40D | 7E0 | 7E8 | Vehicle speed Ford extended | Works / active | 0 km/h parked | Stationary check | Still the active speed connectable. Earlier road test showed about 4 km/h under dash but updates quickly. |
| 2026-05-04 | 222B07 | 760 | 768 | Front left wheel speed | Works / test | 0 km/h parked | Live wheel speed in km/h | Newer ABS/body candidate responded. Needs driving test. |
| 2026-05-04 | 222B06 | 760 | 768 | Front right wheel speed | Works / test | 0 km/h parked | Live wheel speed in km/h | Newer ABS/body candidate responded. Needs driving test. |
| 2026-05-04 | 222B09 | 760 | 768 | Rear left wheel speed | Works / test | 0 km/h parked | Live wheel speed in km/h | Newer ABS/body candidate responded. Needs driving test. |
| 2026-05-04 | 222B08 | 760 | 768 | Rear right wheel speed | Works / test | 0 km/h parked | Live wheel speed in km/h | Newer ABS/body candidate responded. Needs driving test. |
| 2026-05-01 | 010D | — | — | Generic OBD vehicle speed, no header | Still to confirm | — | Live dash speed in km/h | Keep as comparison candidate. |
| 2026-05-01 | 010D | 7E0 | 7E8 | Generic OBD vehicle speed forced PCM | Retest / uncertain | — | Live dash speed in km/h | Earlier forced/header test was not useful. |
| 2026-05-01 | 221E15 | 7E1 | 7E9 | Output shaft speed OSS | Works / diagnostic | 1,025.5 rpm | Changes with road speed | Raw rpm-style value; not direct dash speed. |
| 2026-05-01 | 221E16 | 7E1 | 7E9 | Input shaft speed ISS/TSS | Works / diagnostic | 705.75 rpm | Changes with engine/transmission speed | Diagnostic only, not direct road speed. |
| 2026-05-01 | 222858 | 7A0 | — | Wheel speed front-left ABS | Retest | Blank | Live wheel speed in km/h | Header may vary. No useful value seen yet. |
| 2026-05-01 | 222859 | 7A0 | — | Wheel speed front-right ABS | Retest | Blank | Live wheel speed in km/h | Header may vary. No useful value seen yet. |
| 2026-05-01 | 22285A | 7A0 | — | Wheel speed rear-left ABS | Retest | Blank | Live wheel speed in km/h | Header may vary. No useful value seen yet. |
| 2026-05-01 | 22285B | 7A0 | — | Wheel speed rear-right ABS | Retest | Blank | Live wheel speed in km/h | Header may vary. No useful value seen yet. |
| 2026-05-01 | 22A210 | 7E0 | 7E8 | Vehicle speed setting VSSET | Low priority / wrong target | — | Cruise/set speed, not live speed | Test only if needed. |

### Current speed finding

Best current speed PID:

| PID / cmd | Header | Formula | Behaviour |
|---|---:|---|---|
| 22F40D | 7E0 | A | Updates quickly and tracks road speed |

Known issue:
- Reads about 4 km/h below dashboard speed.
- This may be closer to true/GPS speed while the dash applies normal speedometer over-read.
- Needs GPS comparison before upstreaming as dash-speed.

---

## Still unresolved / wanted

| Target | Status | Notes |
|---|---|---|
| Dash-matching vehicle speed | Partly found | 22F40D works and updates quickly but reads about 4 km/h under dash. Ultimate9 Codebreaker proved a working OBD speed source exists. |
| DPF full % matching dash | Not found | Dash showed 65 %, 80 %, and full during tests. 0579, 057B, 055D and 042C did not reliably match. |
| True boost pressure | Partly found | MAP absolute pressure F40B works. Need a working BARO or calculated boost strategy. |
| Fuel rail pressure actual | Partly found | Desired pressure, low-pressure actual, low-pressure desired, and sensor voltages respond. Actual high-pressure rail pressure still needs a clean confirmed pressure PID. |
| Oil pressure | Not solved / archive only | 7DF / F45C raw values 119–127 were seen, but scaling/unit is not confirmed. `EVEREST_ENGINE_OIL_PRESSURE_7DF_F45C` should stay removed from active default.json. |
| DPF regeneration state | Not found | Need status/regen-active PID if available. |
| Battery age unit | Unclear | Raw 150–151 and hours-style 3600 hrs both derived from 224027. Need confirmation of true meaning. |

---

## Current recommended default.json status

Keep in active file:
- Confirmed / working signals
- Current best transmission temperature formula: 1E1C 5/72
- Ford extended vehicle speed F40D as best current SpeedTest signal
- Useful test-only signals that respond
- Current useful diagnostic counters
- Newer 760 / 768 wheel-speed and lateral-movement candidates grouped under Movement.Everest.SpeedTest while testing

Remove from active file:
- All rejected PIDs
- Wrong-scale AdBlue percentage PIDs
- Wrong-scale battery charge formula
- Static Ford speed PID 22010D
- Bad Ford RPM PID 22010C
- DPF 0579 / 057B / 055D / 042C as dash fullness candidates
- Failed old ABS / IPC / PCM speed candidates
- Failed 7E0 electrical comparison candidates
- Failed throttle candidate 0111 / 0330
- Failed VGT, clutch torque, and old EGT retest candidates

Keep as test-only:
- 010D speed comparisons
- ABS wheel speed 2858/2859/285A/285B candidates
- OSS / ISS rpm values
- MAP / boost-related candidates
- Raw oil pressure as archive evidence only, not active default.json
- Battery age interpretation candidates
- Grille shutter duty cycle
- A/C refrigerant pressure
- Intake diagnostic flags

---

## Latest layout decision — 2026-05-04

Use the default OBDb-style category layout for normal/working signals. Avoid leaving everything under `Test` once a PID has either been confirmed or rejected.

Recommended placement rules:

| Signal type | Recommended category path | Notes |
|---|---|---|
| Working production-style signals | Normal category, e.g. `Engine`, `Fuel`, `Transmission`, `Battery`, `Movement` | Keep names readable and stable. |
| Live speed comparison signals | `Movement.Everest.SpeedTest` | Keep together so Pelican can show movement/speed values on one page. |
| New unconfirmed candidates | `Category.Testing.<identifiable PID name>` | OK while testing, but promote or remove after results. |
| Rejected signals | Not in active `default.json` | Keep only in this markdown log. |
| Archive-only raw signals | Not in active `default.json` unless intentionally retesting | Current example: 7DF / 22F45C oil-pressure raw. |

Current no-active-testing assumption:
- There are no broad old testing batches left to keep by default.
- Only the new 760 / 768 wheel-speed and lateral-movement candidates still need a proper driving validation.
- Oil-pressure raw is not considered active testing unless deliberately re-added later with a new scale hypothesis.

---

## Pull request / upstream notes

Strong candidates for future upstream contribution:
- Fuel remaining F42F
- Generic fuel level 012F
- Odometer 01A6
- Coolant temperature F405
- Engine oil temperature F45C
- Ambient temperature F446
- Intake air temperature F40F
- Intercooler air charge temperature 051C
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
- Engine load F404
- Actual engine percent torque F462
- Engine reference torque F463
- Oil life remaining 054B
- Distance since DTC clear F431
- Distance with MIL F421
- Warm-ups since DTC clear F430
- Cylinder head temperature sensor 2 05FC
- Manifold absolute pressure F40B
- MAP sensor voltage 0301
- Accelerator pedal position 032B
- Lateral angle 2B0C after movement/level validation
- Lateral acceleration 2B11 after movement/level validation
- Wheel speeds 2B06/2B07/2B08/2B09 after driving validation

Possible upstream candidate after GPS/dash validation:
- Vehicle speed Ford extended F40D

Keep out of upstream production JSON until solved:
- Dash-matching vehicle speed
- DPF fullness percentage
- True boost pressure
- Actual high-pressure rail pressure
- Oil pressure scaling
- Battery age interpretation
- ABS / wheel-speed values until confirmed while driving
- Old DPF 0579 / 057B / 055D dash-fullness interpretations
- Wrong-scale AdBlue percentage interpretations
