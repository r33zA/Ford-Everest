# Ford Everest MY25.25 PID Testing Log

https://github.com/r33zA/Ford-Everest

Version: 2026-05-16 v0.6.3 transmission temperature div16 primary pass  
Aligned default file: `default_everest_my25_25_v0_6_3_trans_temp_div16_primary.json` / `signalsets/v3/default.json` target  
Vehicle: Ford Everest Trend MY25.25, Australian market, 2.0 L Bi-Turbo Diesel, 10-speed automatic, full-time 4WD

## Update focus

- Updated from v0.6.2 DPF shelved + transmission temperature comparison pass.
- Promoted `EVEREST_TRANS_TEMP_1E1C_DIV16` to the preferred Everest transmission-fluid-temperature candidate under `Transmission.Everest`.
- Demoted `EVEREST_TRANS_TEMP_1E1C_5_72` to a comparison / legacy scaling value under `Transmission.Generic`.
- Removed `transmissionFluidTemperature` suggestedMetric from the 5/72 comparison value so Pelican should prefer the div16 signal as the active connectable.
- Kept the 5/72 value in the file for future validation against FORScan/factory scan data, not as the preferred dashboard value.
- Revalidated JSON: no duplicate signal IDs, no active oil-pressure signal, no active DPF soot/fullness signal.

## Important note — DPF signal status

| Signal | Decision | Reason |
| --- | --- | --- |
| `EVEREST_DPF_SOOT_055D_DIV64` | Shelved / removed from active default | It responds, but it does not match the vehicle dashboard DPF/exhaust-filter level closely enough. Keep the observation in research notes only until the underlying meaning is proven. |

## Important note — transmission-temperature scaling decision

Both transmission-temperature signals use the same TCM response `7E1 221E1C`; only the scaling differs. A Pelican drive-session comparison showed the div16 value warming and operating in a more plausible range when compared with coolant temperature, engine oil temperature, vehicle speed/load, and normal 10-speed automatic behaviour. For now, `EVEREST_TRANS_TEMP_1E1C_DIV16` is the preferred Everest candidate and `EVEREST_TRANS_TEMP_1E1C_5_72` is retained only as a comparison / legacy scaling value.

Still validate against FORScan or factory scan-tool transmission-fluid-temperature data when available. Tiny disclaimer gremlin remains under supervision.

## Sanity-check snapshot

| Check | Result |
| --- | --- |
| Commands in current default.json | 64 |
| Signals in current default.json | 68 |
| Signals with suggestedMetric | 20 |
| Remaining EVEREST_TEST_* signal IDs | 4 |
| Active oil-pressure signal | None — intentionally removed |
| Active DPF soot/fullness signal | None — shelved |
| Wheel speeds suggestedMetric | None — intentionally removed |
| JSON validation | Passed with `python3 -m json.tool` |

## Vehicle and platform notes

| Item | Value |
| --- | --- |
| Vehicle | Ford Everest Trend |
| Model year | MY25.25 |
| Generation | Everest UB / U704, Next Gen |
| Related donor platform | Ranger P703 / Next-Gen Ranger |
| Engine | 2.0 L Bi-Turbo Diesel |
| Transmission | 10-speed automatic |
| Market | Australia |
| Testing app | Pelican on iOS / CarPlay |
| OBD data source | Custom GitHub fork of `OBDb/Ford-Everest` |

## Default JSON structure rule

```text
Category
Category.Generic
Category.Testing
Category.Subgroup
Category.Subgroup.Generic
```

- Put **Everest-confirmed** items directly under the useful root category or subgroup.
- Put **generic SAE / fallback / comparison** items under `Generic`.
- Use `Testing` only while actively testing a candidate.
- Remove failed or misleading candidates from active `default.json` once rejected.
- Keep rejected or research-only clues in this log instead of polluting Pelican’s UI.

## SuggestedMetric / connectable decisions

| Suggested metric | Path | Signal ID | Name | Cmd |
| --- | --- | --- | --- | --- |
| commandedLambda | Emissions.Testing | EVEREST_TEST_COMMANDED_EQUIVALENCE_RATIO_0144 | Commanded equivalence ratio test | 0144 |
| distanceSinceDTCsCleared | DTCs | EVEREST_DISTANCE_SINCE_DTC_CLEAR_F431 | Distance since DTC clear | 22F431 |
| engineCoolantTemperature | Engine | EVEREST_COOLANT_TEMP_F405 | Coolant temperature | 22F405 |
| engineLoad | Engine | EVEREST_ENGINE_LOAD_F404 | Engine load | 22F404 |
| engineOilTemperature | Engine | EVEREST_ENGINE_OIL_TEMP_F45C | Engine oil temperature | 22F45C |
| engineSpeed | Engine | EVEREST_ENGINE_SPEED_F40C | Engine speed | 22F40C |
| fuelRange | Fuel | FORD_DTE_DISP | Distance to empty, displayed | 224195 |
| fuelTankLevel | Fuel | EVEREST_FUEL_REMAINING_F42F | Fuel remaining | 22F42F |
| fuelTankLevel | Fuel.Generic | EVEREST_GENERIC_FUEL_LEVEL_012F | Generic fuel level | 012F |
| massAirFlow | Engine.Generic | EVEREST_GENERIC_MAF_0110 | Air flow rate from mass air flow sensor | 0110 |
| o2Lambda | Emissions.Testing | EVEREST_TEST_O2_LAMBDA_EQUIVALENCE_RATIO_0124 | O2 lambda equivalence ratio test | 0124 |
| odometer | Movement | EVEREST_ODOMETER_01A6 | Odometer | 01A6 |
| shortTermFuelTrim | Fuel.Testing | EVEREST_TEST_SHORT_TERM_FUEL_TRIM_BANK_1_0106 | Short term fuel trim bank 1 test | 0106 |
| shortTermFuelTrim | Fuel.Testing | EVEREST_TEST_SHORT_TERM_FUEL_TRIM_BANK_2_0108 | Short term fuel trim bank 2 test | 0108 |
| speed | Movement | EVEREST_SPEED_FORD_EXTENDED_F40D | Vehicle speed Ford extended raw | 22F40D |
| speed | Movement | EVEREST_SPEED_FORD_EXTENDED_F40D_CORRECTED | Vehicle speed Ford extended corrected | 22F40D |
| starterBatteryVoltage | Battery | EVEREST_AUX_12V_BATTERY_VOLTAGE_402A | Aux 12V battery voltage | 22402A |
| throttlePosition | Control | EVEREST_ACCEL_PEDAL_032B | Accelerator pedal position | 22032B |
| transmissionFluidTemperature | Transmission.Everest | EVEREST_TRANS_TEMP_1E1C_DIV16 | Transmission fluid temperature Everest div16 | 221E1C |
| transmissionGear | Transmission | EVEREST_GEAR_ENGAGED_7E1_1E1F | Transmission gear engaged | 221E1F |

## Confirmed / working and currently understood

| Group | PID / cmd | ECU | Signal | Status | Decision / notes |
| --- | --- | --- | --- | --- | --- |
| Fuel / range | 22F42F | 7E0→7E8 | Fuel remaining | Works | Active as fuelTankLevel |
| Fuel / range | 012F | 7E0→7E8 | Generic fuel level | Works | Backup/comparison fuelTankLevel |
| Fuel / range | 224195 | 7E0→7E8 | Distance to empty, displayed | Works | Matched dash; active as fuelRange |
| Movement | 01A6 | 7E0→7E8 | Odometer | Works | Active as odometer |
| Movement | 22F40D | 7E0→7E8 | Vehicle speed Ford extended raw | Works | Raw speed connectable |
| Movement | 22F40D + 4 | 7E0→7E8 | Vehicle speed Ford extended corrected | Works / derived | Practical dash-matching speed connectable |
| Wheel speed | 222B06/07/08/09 | 760→768 | Four wheel-speed signals | Works | Active parameters only; not suggestedMetric |
| Body motion | 222B0C / 222B11 | 760→768 | Lateral angle / lateral acceleration | Works | Active body-motion parameters |
| Control | 22032B | 7E0→7E8 | Accelerator pedal position | Works | Practical throttlePosition connectable |
| Engine | 22F404 | 7E0→7E8 | Engine load | Works | Active as engineLoad |
| Engine | 22F405 | 7E0→7E8 | Coolant temperature | Works | Active as engineCoolantTemperature |
| Engine | 22F40C | 7E0→7E8 | Engine speed | Works | Active as engineSpeed |
| Engine | 22F45C | 7E0→7E8 | Engine oil temperature | Works | Active as engineOilTemperature |
| Transmission | 221E1C div16 | 7E1→7E9 | Transmission fluid temperature Everest div16 | Works / chosen | Preferred current Everest-specific scaling; active transmissionFluidTemperature connectable |
| Transmission | 221E1C 5/72 | 7E1→7E9 | Transmission fluid temperature 5/72 compare | Comparison / legacy | Retained for validation only; no suggestedMetric |
| Transmission | 221E1F | 7E1→7E9 | Transmission gear engaged | Works | Active as transmissionGear |
| Emissions | 22052E | 7E0→7E8 | EGR open percentage | Works | Active |
| Emissions | 220569 | 7E0→7E8 | EGT pre-turbo | Works / plausible | Active |
| AdBlue | 220487 | 7E0→7E8 | AdBlue tank level | Works | Best AdBlue quantity signal |
| Battery | 22402A | 726→72E | Aux 12V battery voltage | Works | Active as starterBatteryVoltage |

## Active signals currently present in `default.json`

This table is generated from the current parsed `default.json` so the log and JSON stay aligned.

| Path | Signal ID | Name | Cmd | ECU | Format | Suggested metric |
| --- | --- | --- | --- | --- | --- | --- |
| Battery | EVEREST_ALTERNATOR_CURRENT_0551 | Alternator current output | 220551 | 7E0→7E8 | len 16; val÷64; range ..250; ampere | — |
| Battery | EVEREST_AUX_12V_BATTERY_AGE_HOURS_4027 | Aux 12V battery age | 224027 | 726→72E | len 16; val×24; range ..1572840; hours | — |
| Battery | EVEREST_AUX_12V_BATTERY_VOLTAGE_402A | Aux 12V battery voltage | 22402A | 726→72E | len 8; val÷20; val+6; range ..18.75; volts | starterBatteryVoltage |
| Battery | EVEREST_BATTERY_AGE_SCALAR_4027 | Battery age scalar | 224027 | 726→72E | len 16; range ..1825; scalar | — |
| Battery | EVEREST_BATTERY_CHARGE_4028_DIV255 | Battery charge 4028 div255 | 224028 | 726→72E | len 8; val×100; val÷255; range ..100; percent | — |
| Battery | EVEREST_BATTERY_CURRENT_402B_726 | Aux 12V battery current | 22402B | 726→72E | len 8; val-127; range -127..128; ampere | — |
| Battery | EVEREST_BATTERY_SOC_4028_726 | Aux 12V battery state of charge | 224028 | 726→72E | len 8; range ..100; percent | — |
| Body | EVEREST_GRILL_SHUTTER_DUTY_CYCLE_0707 | Grill shutter duty cycle | 220707 | 7E0→7E8 | len 8; val×100; val÷255; range ..100; percent | — |
| Climate | EVEREST_AAT_SENSOR_VOLTAGE_03BA | Ambient air temperature sensor voltage | 2203BA | 7E0→7E8 | len 16; val×0.0048875855; range ..5; volts | — |
| Climate | EVEREST_AC_PRESSURE_SENSOR_VOLTAGE_9800 | A/C compressor pressure sensor voltage | 229800 | 7E0→7E8 | len 16; signed; val÷1024; range ..5; volts | — |
| Climate | EVEREST_AC_REFRIGERANT_PRESSURE_0604 | A/C refrigerant pressure | 220604 | 7E0→7E8 | len 16; signed; range -32768..32767; kilopascal | — |
| Climate | EVEREST_AMBIENT_AIR_TEMP_F446 | Ambient air temperature | 22F446 | 7E0→7E8 | len 8; val-40; range -40..215; celsius | — |
| Control | EVEREST_ACCEL_PEDAL_032B | Accelerator pedal position | 22032B | 7E0→7E8 | len 8; val×100; val÷255; range ..100; percent | throttlePosition |
| DTCs | EVEREST_DISTANCE_SINCE_DTC_CLEAR_F431 | Distance since DTC clear | 22F431 | 7E0→7E8 | len 16; range ..65535; kilometers | distanceSinceDTCsCleared |
| DTCs | EVEREST_DISTANCE_WITH_MIL_F421 | Distance with MIL | 22F421 | 7E0→7E8 | len 16; range ..65535; kilometers | — |
| DTCs | EVEREST_WARMUPS_SINCE_DTC_CLEAR_F430 | Warm-ups since DTC clear | 22F430 | 7E0→7E8 | len 8; range ..255; scalar | — |
| Emissions.AdBlue | EVEREST_ADBLUE_LEVEL_LITRES_0487 | AdBlue tank level | 220487 | 7E0→7E8 | len 16; val÷1000; range ..18.5; liters | — |
| Emissions.AdBlue | EVEREST_ADBLUE_TEMP_0685 | AdBlue tank temperature | 220685 | 7E0→7E8 | len 8; val-40; range -40..215; celsius | — |
| Emissions.EGR | EVEREST_EGR_OPEN_052E | EGR open percentage | 22052E | 7E0→7E8 | len 8; val×100; val÷255; range ..100; percent | — |
| Emissions.Exhaust | EVEREST_EGT_PRE_TURBO_0569 | Exhaust manifold EGT pre-turbo | 220569 | 7E0→7E8 | len 16; val÷32; range ..1500; celsius | — |
| Emissions.Testing | EVEREST_TEST_COMMANDED_EQUIVALENCE_RATIO_0144 | Commanded equivalence ratio test | 0144 | 7E0→7E8 | len 16; val÷32768; range ..1.9999694824; ratio | commandedLambda |
| Emissions.Testing | EVEREST_TEST_O2_LAMBDA_EQUIVALENCE_RATIO_0124 | O2 lambda equivalence ratio test | 0124 | 7E0→7E8 | len 16; val÷32768; range ..1.9999694824; ratio | o2Lambda |
| Engine | EVEREST_AIR_CHARGE_TEMP_051C | Intercooler air charge temperature | 22051C | 7E0→7E8 | len 8; val-40; range -40..200; celsius | — |
| Engine | EVEREST_COOLANT_TEMP_F405 | Coolant temperature | 22F405 | 7E0→7E8 | len 8; val-40; range -40..215; celsius | engineCoolantTemperature |
| Engine | EVEREST_CYL_HEAD_TEMP_SENSOR_2_05FC | Cylinder head temperature sensor 2 | 2205FC | 7E0→7E8 | len 16; signed; val÷64; range -512..511.984375; celsius | — |
| Engine | EVEREST_ENGINE_LOAD_F404 | Engine load | 22F404 | 7E0→7E8 | len 8; val×100; val÷255; range ..100; percent | engineLoad |
| Engine | EVEREST_ENGINE_OIL_TEMP_F45C | Engine oil temperature | 22F45C | 7E0→7E8 | len 8; val-40; range -40..215; celsius | engineOilTemperature |
| Engine | EVEREST_ENGINE_REFERENCE_TORQUE_F463 | Engine reference torque | 22F463 | 7E0→7E8 | len 16; range ..65535; newtonMeters | — |
| Engine | EVEREST_ENGINE_RUN_TIME_F41F | Engine run time | 22F41F | 7E0→7E8 | len 16; range ..65535; seconds | — |
| Engine | EVEREST_ENGINE_SPEED_F40C | Engine speed | 22F40C | 7E0→7E8 | len 16; val÷4; range ..16383.75; rpm | engineSpeed |
| Engine | EVEREST_IAT_FAULT_0700 | Intake temperature fault | 220700 | 7E0→7E8 | len 8; range ..255; scalar | — |
| Engine | EVEREST_IAT_SENSOR_VOLTAGE_1279 | Intake temperature sensor voltage | 221279 | 7E0→7E8 | len 16; val×5; val÷1023; range ..5; volts | — |
| Engine | EVEREST_INTAKE_AIR_TEMP_F40F | Intake air temperature | 22F40F | 7E0→7E8 | len 8; val-40; range -40..215; celsius | — |
| Engine | EVEREST_PCM_ACTUAL_ENGINE_PERCENT_TORQUE_F462 | Actual engine percent torque | 22F462 | 7E0→7E8 | len 8; val-125; range ..100; percent | — |
| Engine.Boost | EVEREST_MANIFOLD_PRESSURE_F40B | Manifold pressure | 22F40B | 7E0→7E8 | len 8; val×10000; val÷68947; range ..36; psi | — |
| Engine.Boost | EVEREST_MAP_SENSOR_VOLTAGE_0301 | Manifold absolute pressure sensor voltage | 220301 | 7E0→7E8 | len 16; val×0.0009765625; range ..5; volts | — |
| Engine.Boost | EVEREST_WASTEGATE_DUTY_CYCLE_0462 | Wastegate duty cycle | 220462 | 7E0→7E8 | len 16; val×100; val÷32768; range ..100; percent | — |
| Engine.Boost.Generic | EVEREST_GENERIC_MAP_010B | Generic MAP | 010B | 7E0→7E8 | len 8; range ..255; kilopascal | — |
| Engine.Generic | EVEREST_GENERIC_MAF_0110 | Air flow rate from mass air flow sensor | 0110 | 7E0→7E8 | len 16; val÷100; range ..655.35; gramsPerSecond | massAirFlow |
| Fuel | EVEREST_FUEL_REMAINING_F42F | Fuel remaining | 22F42F | 7E0→7E8 | len 8; val×100; val÷255; range ..100; percent | fuelTankLevel |
| Fuel | EVEREST_FUEL_TEMP_0522 | Fuel temperature | 220522 | 7E0→7E8 | len 8; val-40; range -40..200; celsius | — |
| Fuel | EVEREST_HPFP_DESIRED_03DC | High pressure fuel pressure desired | 2203DC | 7E0→7E8 | len 16; val×1.4503773969; range ..3500; psi | — |
| Fuel | EVEREST_HPFP_SENSOR_VOLTAGE_0324 | High pressure fuel rail sensor voltage | 220324 | 7E0→7E8 | len 16; val÷204.6; range ..5; volts | — |
| Fuel | EVEREST_LPFP_ACTUAL_0548 | Fuel pressure, low actual | 220548 | 7E0→7E8 | len 16; signed; val×10000; val÷137892; range ..200; psi | — |
| Fuel | EVEREST_LPFP_DESIRED_041F | Fuel pressure, low desired | 22041F | 7E0→7E8 | len 8; val×435; val÷1000; range ..110; psi | — |
| Fuel | EVEREST_LPFP_DUTY_0307 | Low pressure fuel pump commanded duty cycle | 220307 | 7E0→7E8 | len 16; val×0.0030517578; range ..100; percent | — |
| Fuel | EVEREST_LPFP_SENSOR_VOLTAGE_054D | Fuel pressure sensor voltage, low | 22054D | 7E0→7E8 | len 16; signed; val÷1024; range ..5; volts | — |
| Fuel | FORD_DTE_DISP | Distance to empty, displayed | 224195 | 7E0→7E8 | len 16; val÷10; range ..6553.5; kilometers | fuelRange |
| Fuel.Generic | EVEREST_GENERIC_FUEL_LEVEL_012F | Generic fuel level | 012F | 7E0→7E8 | len 8; val×100; val÷255; range ..100; percent | fuelTankLevel |
| Fuel.Testing | EVEREST_TEST_SHORT_TERM_FUEL_TRIM_BANK_1_0106 | Short term fuel trim bank 1 test | 0106 | 7E0→7E8 | len 8; val×100; val÷128; val-100; range -100..99.21875; percent | shortTermFuelTrim |
| Fuel.Testing | EVEREST_TEST_SHORT_TERM_FUEL_TRIM_BANK_2_0108 | Short term fuel trim bank 2 test | 0108 | 7E0→7E8 | len 8; val×100; val÷128; val-100; range -100..99.21875; percent | shortTermFuelTrim |
| Maintenance | EVEREST_OIL_LIFE_REMAINING_054B | Oil life remaining | 22054B | 7E0→7E8 | len 8; range ..100; percent | — |
| Movement | EVEREST_ODOMETER_01A6 | Odometer | 01A6 | 7E0→7E8 | len 32; val×0.1; range ..429496729.5; kilometers | odometer |
| Movement | EVEREST_SPEED_FORD_EXTENDED_F40D | Vehicle speed Ford extended raw | 22F40D | 7E0→7E8 | len 8; range ..255; kilometersPerHour | speed |
| Movement | EVEREST_SPEED_FORD_EXTENDED_F40D_CORRECTED | Vehicle speed Ford extended corrected | 22F40D | 7E0→7E8 | len 8; val+4; range ..259; kilometersPerHour | speed |
| Movement.BodyMotion | FORD_LAT_ANG | Lateral angle | 222B0C | 760→768 | len 8; signed; range -90..90; degrees | — |
| Movement.BodyMotion | FORD_LAT_G | Lateral acceleration | 222B11 | 760→768 | len 16; signed; val÷50; range -1..1; scalar | — |
| Movement.WheelSpeed | FORD_FL_VSS | Front left wheel speed | 222B07 | 760→768 | len 8; range ..200; kilometersPerHour | — |
| Movement.WheelSpeed | FORD_FR_VSS | Front right wheel speed | 222B06 | 760→768 | len 8; range ..200; kilometersPerHour | — |
| Movement.WheelSpeed | FORD_RL_VSS | Rear left wheel speed | 222B09 | 760→768 | len 8; range ..200; kilometersPerHour | — |
| Movement.WheelSpeed | FORD_RR_VSS | Rear right wheel speed | 222B08 | 760→768 | len 8; range ..200; kilometersPerHour | — |
| Transmission | EVEREST_GEAR_COMMANDED_7E1_1E12 | Transmission gear commanded | 221E12 | 7E1→7E9 | len 8; range ..10; scalar | — |
| Transmission | EVEREST_GEAR_ENGAGED_7E1_1E1F | Transmission gear engaged | 221E1F | 7E1→7E9 | len 8; range ..10; scalar | transmissionGear |
| Transmission | EVEREST_ISS_TSS_1E16_RPM | Input shaft speed ISS/TSS 1E16 | 221E16 | 7E1→7E9 | len 16; val÷4; range ..10000; rpm | — |
| Transmission | EVEREST_OSS_1E15_RPM | Output shaft speed OSS 1E15 | 221E15 | 7E1→7E9 | len 16; val÷4; range ..10000; rpm | — |
| Transmission | EVEREST_TCC_SLIP_1E14 | Torque converter slip speed | 221E14 | 7E1→7E9 | len 16; val÷4; range ..5500; rpm | — |
| Transmission.Everest | EVEREST_TRANS_TEMP_1E1C_DIV16 | Transmission fluid temperature Everest div16 | 221E1C | 7E1→7E9 | len 16; val÷16; range ..200; celsius | transmissionFluidTemperature |
| Transmission.Generic | EVEREST_TRANS_TEMP_1E1C_5_72 | Transmission fluid temperature 5/72 compare | 221E1C | 7E1→7E9 | len 16; val×5; val÷72; val-17; range ..200; celsius | — |

## Shelved, rejected, removed, or archive-only candidates

| PID / cmd | ECU | Signal | Decision | Reason |
| --- | --- | --- | --- | --- |
| 22055D | 7E0→7E8 | DPF soot load 055D div64 | Shelved / removed | Responds, but does not match dashboard DPF/exhaust-filter level reliably. |
| 7DF 22F45C raw / FORD_EOP | 7DF | Engine oil pressure raw retest | Archive only / removed | Raw value exists but scale/meaning not proven. |
| 22010D | 7E0→7E8 | Ford vehicle speed candidate | Rejected | Static/stale at 16 km/h; replaced by 22F40D. |
| 010D | 7E0→7E8 | Generic vehicle speed | Rejected / backup only | No useful value in early testing; production uses Ford extended speed. |
| 22010C | 7E0→7E8 | Ford RPM candidate | Rejected | Wrong scale / impossible diesel RPM; replaced by 22F40C. |
| 221674 | 7E1→7E9 | Transmission temp alternate | Rejected | Negative response / out of range. |
| 221E1B | 7E1→7E9 | Transmission output shaft speed candidate | Rejected | Negative response / out of range; 221E15 works. |
| 22042C | 7E0→7E8 | DPF soot mass experimental | Rejected | Negative response / out of range. |
| 221440 / 221442 | 7E0→7E8 | MAP/BARO candidates | Rejected | Negative response / out of range; 22F40B / 010B retained. |
| 220415 | 7E0→7E8 | Oil pressure candidate | Rejected | Negative response / out of range. |
| 224029 | 726→72E | Battery temperature estimated | Rejected | Negative response / out of range. |
| 220683 / 220684 | 7E0→7E8 | AdBlue mm / percent candidates | Rejected as main value | Litres PID is better. |
| 2203F4 / 2203F5 / 2203F6 | 7E0→7E8 | EGT alternate candidates | Suspicious / removed | Fixed 255°C-style values; likely wrong formula/default/null. |

## Still unresolved / research queue

| Priority | Target | Status | Notes |
| --- | --- | --- | --- |
| 1 | DPF regeneration active / exhaust filter cleaning state | Not found | High-value target. Need a live state PID, not a misleading fullness proxy. |
| 2 | DPF fullness matching dash | Not solved | `22055D div64` is shelved because it does not match the dashboard level reliably. |
| 3 | Oil pressure | Not solved | Keep as research only. Do not promote FORD_EOP / 7DF raw evidence without scale proof. |
| 4 | Engine hours / idle hours | Not solved | Engine run time 22F41F is current run time, not lifetime hours. |
| 5 | True boost pressure | Partly found | MAP absolute works. Need clean BARO or calculated boost strategy. |
| 6 | Actual high-pressure fuel rail pressure | Partly found | Desired rail pressure and sensor voltage respond; true actual high-pressure rail still needs confirmation. |

## Testing rules going forward

### Promote only when all three are true

- Live response received.
- Value is plausible for the vehicle state.
- Behaviour changes correctly during warm-up, movement, throttle/load, regen, gear changes, or another relevant real-world state.

### Reject quickly when any are true

- Negative response / out of range.
- Blank or stale value.
- Impossible scale.
- Looks like a config address instead of a live PID.
- Duplicates a better existing signal.

## Current default.json recommendation

Keep active:

- Confirmed working signals in the current v0.6.2 layout.
- Raw and corrected Ford extended vehicle speed.
- Ford ABS wheel speeds and body-motion signals, but **not** as `suggestedMetric`.
- Engine oil temperature, engine load, coolant temperature, RPM, DTE, fuel remaining, fuel rate, odometer, transmission temp comparison signals, transmission gear, and starter battery voltage connectables.

Keep research-only:

- DPF soot/fullness/regeneration state.
- Oil pressure.
- Engine hours / idle hours.
- True boost pressure calculation.
- Actual high-pressure fuel rail pressure.

Keep removed:

- `EVEREST_DPF_SOOT_055D_DIV64` from active default until its meaning is proven.
- Failed `EVEREST_TEST_*` wheel-speed/body-motion guesses.
- `EVEREST_TEST_ENGINE_OIL_PRESSURE_7D-F_F45C_RAW`.
- Static Ford speed candidate `22010D`.
- Bad Ford RPM candidate `22010C`.

## Change log

### 2026-05-14 — v0.6.2 DPF shelved + transmission temp comparison pass

- Removed `EVEREST_DPF_SOOT_055D_DIV64` from active `default.json`.
- Added `EVEREST_TRANS_TEMP_1E1C_DIV16` back under `Transmission.Generic`.
- Added `transmissionFluidTemperature` suggestedMetric to both the 5/72 and div16 transmission-temperature entries.
- Updated active signal table and sanity-check counts from parsed JSON.


## v0.6.3 transmission-temperature decision log

| Item | Decision |
| --- | --- |
| Preferred transmission temperature | `EVEREST_TRANS_TEMP_1E1C_DIV16` |
| Comparison / legacy scaling | `EVEREST_TRANS_TEMP_1E1C_5_72` |
| Shared source response | `7E1 221E1C` |
| Reason | Pelican drive-session comparison made div16 look more plausible against coolant, oil temperature, speed/load, and expected 10-speed transmission warm-up behaviour. |
| Remaining validation | Compare against FORScan or factory scan-tool TFT when available. |

## Commit message

```text
Promote div16 transmission temperature scaling
```

## Extended description

```text
Promotes EVEREST_TRANS_TEMP_1E1C_DIV16 to the preferred Everest transmission-fluid-temperature candidate after Pelican drive-session comparison.

Both active transmission-temperature signals use the same 7E1 221E1C TCM response, but the div16 scaling produced a more plausible warm-up curve and operating range when compared with coolant temperature, engine oil temperature, speed/load, and expected 10-speed automatic behaviour.

The older 5/72 -17 scaling is retained as a comparison / legacy value under Transmission.Generic, but its transmissionFluidTemperature suggestedMetric has been removed so Pelican should prefer the div16 signal as the active connectable.

DPF soot/fullness remains shelved, oil pressure remains removed, and the JSON has been revalidated with no duplicate signal IDs.
```
