# Ford Everest MY25.25 PID Testing Log

https://github.com/r33zA/Ford-Everest

Version: 2026-05-08 v0.6.1 diesel connectables pass
Aligned default file: `default_everest_my25_25_v0_6_1_diesel_connectables.json` / `signalsets/v3/default.json` target
Vehicle: Ford Everest Trend MY25.25, Australian market, 2.0 L Bi-Turbo Diesel, 10-speed automatic, full-time 4WD

## Update focus

- Updated from uploaded v0.6.0 connectables layout.
- Added diesel-relevant connectable/test coverage:
  - generic SAE MAF `0110` as `massAirFlow`
  - short-term fuel-trim test entries `0106` and `0108` as `shortTermFuelTrim`
  - wide-range O2/lambda equivalence-ratio test `0124` as `o2Lambda`
  - commanded equivalence-ratio test `0144` as `commandedLambda`
  - confirmed accelerator pedal `22032B` as the practical `throttlePosition` connectable
- Left EV/hybrid-style state-of-health entries (`SOCE` / `SOCR`) out intentionally; they are not useful Everest diesel health signals.
- Preserved confirmed Everest items: corrected/raw speed, DTE, wheel speeds, body motion, transmission temperature, gear, engine oil temperature, DPF soot, battery voltage, fuel remaining, odometer, and engine load.
- Revalidated JSON: no duplicate signal IDs, no active oil-pressure signal, no `EVEREST_TEST_ENGINE_OIL_PRESSURE_7D-F_F45C_RAW`.

## Sanity-check snapshot

| Check | Result |
| --- | --- |
| Commands in current default.json | 65 |
| Signals in current default.json | 68 |
| Signals with suggestedMetric | 20 |
| Remaining EVEREST_TEST_* signal IDs | 4 |
| Active oil-pressure signal | None — intentionally removed |
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

Current preferred layout:

```text
Category
Category.Generic
Category.Testing
Category.Subgroup
Category.Subgroup.Generic
```

Practical rule:

- Put **Everest-confirmed** items directly under the useful root category or subgroup.
- Put **generic SAE / fallback** items under `Generic`.
- Use `Testing` only while actively testing a candidate.
- Remove failed candidates from active `default.json` once rejected.
- Keep rejected or research-only clues in this log instead of polluting Pelican’s UI. The app has enough buttons already — no need to build it a junk drawer.

## SuggestedMetric / connectable decisions

| Suggested metric | Path | Signal ID | Name | Cmd |
| --- | --- | --- | --- | --- |
| commandedLambda | Emissions.Testing | EVEREST_TEST_COMMANDED_EQUIVALENCE_RATIO_0144 | Commanded equivalence ratio test | 0144 |
| distanceSinceDTCsCleared | DTCs | EVEREST_DISTANCE_SINCE_DTC_CLEAR_F431 | Distance since DTC clear | 22F431 |
| dpfSootLoad | Emissions.DPF | EVEREST_DPF_SOOT_055D_DIV64 | DPF soot load 055D div64 | 22055D |
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
| transmissionGear | Transmission | EVEREST_GEAR_ENGAGED_7E1_1E1F | Transmission gear engaged | 221E1F |

## Diesel connectable notes

| Connectable group | Decision | Reason |
| --- | --- | --- |
| Throttle position | Use `EVEREST_ACCEL_PEDAL_032B` | Generic throttle-position candidates failed; pedal position is the useful driver-input signal. |
| Short-term fuel trim | Added `0106` / `0108` as test-only | Diesels may not use petrol-style closed-loop fuel trims, but these let Pelican show whether the PCM exposes anything. |
| O2 lambda | Added `0124` as test-only | Diesel air/fuel control is better represented by lambda/excess-air style values if supported. |
| Commanded lambda | Added `0144` as test-only | Commanded equivalence ratio is more relevant than pretending petrol fuel trims are the whole story. |
| Mass air flow | Added generic SAE `0110` | MAF is a useful diesel air-side signal if supported. |
| State of health | Not added | `SOCE` / `SOCR` are EV/hybrid-style state-of-certified-energy/range concepts, not meaningful Everest diesel health PIDs. |

## Confirmed / working and currently understood

| Group | PID / cmd | ECU | Signal | Status | Value / behaviour seen | Decision / notes |
| --- | --- | --- | --- | --- | --- | --- |
| Fuel / range | 22F42F | 7E0→7E8 | Fuel remaining | Works | 78% previously; active as fuelTankLevel | Ford/Ranger-style fuel PID. |
| Fuel / range | 012F | 7E0→7E8 | Generic fuel level | Works | 77–78% previously; backup only | Kept under Fuel.Generic. |
| Fuel / range | 224195 | 7E0→7E8 | Distance to empty, displayed | Works | 336 km matched dash | Active as fuelRange. |
| Movement | 01A6 | 7E0→7E8 | Odometer | Works | Odometer value plausible | Active as odometer. |
| Movement | 22F40D | 7E0→7E8 | Vehicle speed Ford extended raw | Works | About 4 km/h under dash | Kept as raw speed connectable. |
| Movement | 22F40D + 4 | 7E0→7E8 | Vehicle speed Ford extended corrected | Works / derived | Adds 4 km/h to raw | Kept as corrected speed connectable. |
| Wheel speed | 222B06 | 760→768 | Front right wheel speed | Works | 0–13 km/h seen | Ford ABS path; not suggestedMetric. |
| Wheel speed | 222B07 | 760→768 | Front left wheel speed | Works | 0–13 km/h seen | Ford ABS path; not suggestedMetric. |
| Wheel speed | 222B08 | 760→768 | Rear right wheel speed | Works | 0–13 km/h seen | Ford ABS path; not suggestedMetric. |
| Wheel speed | 222B09 | 760→768 | Rear left wheel speed | Works | 0–13 km/h seen | Ford ABS path; not suggestedMetric. |
| Body motion | 222B0C | 760→768 | Lateral angle | Works | Around -1° seen | Ford ABS path; signed degrees. |
| Body motion | 222B11 | 760→768 | Lateral acceleration | Works | Small +/- values seen | Ford ABS path; scalar divided by 50. |
| Control | 22032B | 7E0→7E8 | Accelerator pedal position | Works | 0–4% seen | Active as practical throttlePosition connectable. |
| Engine | 22F404 | 7E0→7E8 | Engine load | Works | Connectable | Active as engineLoad. |
| Engine | 22F405 | 7E0→7E8 | Coolant temperature | Works | 47–56°C warm-up | Matched built-in ECT. |
| Engine | 22F40C | 7E0→7E8 | Engine speed | Works | Normal RPM behaviour | Active as engineSpeed. |
| Engine | 22F41F | 7E0→7E8 | Engine run time | Works | 32 min 56 sec seen | Current engine run time, not lifetime hours. |
| Engine | 22F45C | 7E0→7E8 | Engine oil temperature | Works | 52°C earlier; active | Active as engineOilTemperature. |
| Transmission | 221E1C | 7E1→7E9 | Transmission fluid temperature | Works / chosen | 5/72 formula retained | Best match after warm-drive comparison. |
| Transmission | 221E12 | 7E1→7E9 | Transmission gear commanded | Works | 1 and 9/10 seen | Kept active. |
| Transmission | 221E1F | 7E1→7E9 | Transmission gear engaged | Works | 1 and 9/10 seen | Active as transmissionGear. |
| Transmission | 221E14 | 7E1→7E9 | Torque converter slip speed | Works | High unlocked, low locked | Behaviour looks correct. |
| Transmission | 221E15 | 7E1→7E9 | Output shaft speed OSS | Works | ~991–1025 rpm seen | Active. |
| Transmission | 221E16 | 7E1→7E9 | Input shaft speed ISS/TSS | Works | ~10,000 rpm seen | Active; watch scale sanity. |
| Emissions | 22055D | 7E0→7E8 | DPF soot load 055D div64 | Works / useful | 100% when dash Exhaust Filter Full | Useful but keep monitoring. |
| Emissions | 22052E | 7E0→7E8 | EGR open percentage | Works | 21% seen | Active. |
| Emissions | 220569 | 7E0→7E8 | EGT pre-turbo | Works / plausible | ~200°C seen | Active. |
| AdBlue | 220487 | 7E0→7E8 | AdBlue tank level | Works | ~17 L after fill | Best AdBlue quantity signal. |
| AdBlue | 220685 | 7E0→7E8 | AdBlue tank temperature | Works / plausible | 20°C seen | Active. |
| Battery | 22402A | 726→72E | Aux 12V battery voltage | Works | Voltage trace plausible | Active as starterBatteryVoltage. |
| Battery | 224028 | 726→72E | Aux 12V battery SOC / charge | Works / plausible | Raw + div255 retained | Both kept; neither is default connectable. |
| Battery | 224027 | 726→72E | Battery age | Works / unit unclear | 153 scalar seen | Raw scalar useful; hours interpretation lower-confidence. |
| Maintenance | 22054B | 7E0→7E8 | Oil life remaining | Works | 47% seen | Compare later with FordPass/dash. |

## New v0.6.1 diesel connectable/test additions

| Group | PID / cmd | ECU | Signal | Status | Decision / notes |
| --- | --- | --- | --- | --- | --- |
| Engine air | 0110 | 7E0→7E8 | MAF airflow | Added / fallback | Generic SAE MAF. Useful diesel air-side signal if supported. |
| Fuel trim | 0106 | 7E0→7E8 | Short term fuel trim bank 1 | Test only | Petrol-style trim may not be supported/meaningful on this diesel. Added for comparison only. |
| Fuel trim | 0108 | 7E0→7E8 | Short term fuel trim bank 2 | Test only | Same as above; useful to see whether the PCM exposes anything. |
| Lambda | 0124 | 7E0→7E8 | Wide-range O2/lambda equivalence ratio | Test only | Diesel-relevant if supported, but scale/support must be proven live. |
| Lambda | 0144 | 7E0→7E8 | Commanded equivalence ratio | Test only | Added as commanded-lambda style candidate. |
| State of health | SOCE / SOCR | — | State of certified energy/range | Not added | EV/hybrid-style health/range degradation signals; not a useful Everest diesel PID target. |

## Active signals currently present in `default.json`

This table is generated from the current parsed `default.json` so the log and JSON stay aligned.

| Path | Signal ID | Name | Cmd | ECU | Format | Suggested metric |
| --- | --- | --- | --- | --- | --- | --- |
| Battery | EVEREST_ALTERNATOR_CURRENT_0551 | Alternator current output | 220551 | 7E0→7E8 | len 16; val÷64; range ..250; ampere | — |
| Battery | EVEREST_AUX_12V_BATTERY_AGE_HOURS_4027 | Aux 12V battery age | 224027 | 726→72E | len 16; val×24; range ..1572840; hours | — |
| Battery | EVEREST_BATTERY_CURRENT_402B_726 | Aux 12V battery current | 22402B | 726→72E | len 8; val-127; range -127..128; ampere | — |
| Battery | EVEREST_BATTERY_SOC_4028_726 | Aux 12V battery state of charge | 224028 | 726→72E | len 8; range ..100; percent | — |
| Battery | EVEREST_AUX_12V_BATTERY_VOLTAGE_402A | Aux 12V battery voltage | 22402A | 726→72E | len 8; val÷20; val+6; range ..18.75; volts | starterBatteryVoltage |
| Battery | EVEREST_BATTERY_AGE_SCALAR_4027 | Battery age scalar | 224027 | 726→72E | len 16; range ..1825; scalar | — |
| Battery | EVEREST_BATTERY_CHARGE_4028_DIV255 | Battery charge 4028 div255 | 224028 | 726→72E | len 8; val×100; val÷255; range ..100; percent | — |
| Body | EVEREST_GRILL_SHUTTER_DUTY_CYCLE_0707 | Grill shutter duty cycle | 220707 | 7E0→7E8 | len 8; val×100; val÷255; range ..100; percent | — |
| Climate | EVEREST_AC_PRESSURE_SENSOR_VOLTAGE_9800 | A/C compressor pressure sensor voltage | 229800 | 7E0→7E8 | len 16; signed; val÷1024; range ..5; volts | — |
| Climate | EVEREST_AC_REFRIGERANT_PRESSURE_0604 | A/C refrigerant pressure | 220604 | 7E0→7E8 | len 16; signed; range -32768..32767; kilopascal | — |
| Climate | EVEREST_AMBIENT_AIR_TEMP_F446 | Ambient air temperature | 22F446 | 7E0→7E8 | len 8; val-40; range -40..215; celsius | — |
| Climate | EVEREST_AAT_SENSOR_VOLTAGE_03BA | Ambient air temperature sensor voltage | 2203BA | 7E0→7E8 | len 16; val×0.0048875855; range ..5; volts | — |
| Control | EVEREST_ACCEL_PEDAL_032B | Accelerator pedal position | 22032B | 7E0→7E8 | len 8; val×100; val÷255; range ..100; percent | throttlePosition |
| DTCs | EVEREST_DISTANCE_SINCE_DTC_CLEAR_F431 | Distance since DTC clear | 22F431 | 7E0→7E8 | len 16; range ..65535; kilometers | distanceSinceDTCsCleared |
| DTCs | EVEREST_DISTANCE_WITH_MIL_F421 | Distance with MIL | 22F421 | 7E0→7E8 | len 16; range ..65535; kilometers | — |
| DTCs | EVEREST_WARMUPS_SINCE_DTC_CLEAR_F430 | Warm-ups since DTC clear | 22F430 | 7E0→7E8 | len 8; range ..255; scalar | — |
| Emissions.AdBlue | EVEREST_ADBLUE_LEVEL_LITRES_0487 | AdBlue tank level | 220487 | 7E0→7E8 | len 16; val÷1000; range ..18.5; liters | — |
| Emissions.AdBlue | EVEREST_ADBLUE_TEMP_0685 | AdBlue tank temperature | 220685 | 7E0→7E8 | len 8; val-40; range -40..215; celsius | — |
| Emissions.DPF | EVEREST_DPF_SOOT_055D_DIV64 | DPF soot load 055D div64 | 22055D | 7E0→7E8 | len 16; val÷64; range ..100; percent | dpfSootLoad |
| Emissions.EGR | EVEREST_EGR_OPEN_052E | EGR open percentage | 22052E | 7E0→7E8 | len 8; val×100; val÷255; range ..100; percent | — |
| Emissions.Exhaust | EVEREST_EGT_PRE_TURBO_0569 | Exhaust manifold EGT pre-turbo | 220569 | 7E0→7E8 | len 16; val÷32; range ..1500; celsius | — |
| Emissions.Testing | EVEREST_TEST_COMMANDED_EQUIVALENCE_RATIO_0144 | Commanded equivalence ratio test | 0144 | 7E0→7E8 | len 16; val÷32768; range ..1.9999694824; ratio | commandedLambda |
| Emissions.Testing | EVEREST_TEST_O2_LAMBDA_EQUIVALENCE_RATIO_0124 | O2 lambda equivalence ratio test | 0124 | 7E0→7E8 | len 16; val÷32768; range ..1.9999694824; ratio | o2Lambda |
| Engine | EVEREST_PCM_ACTUAL_ENGINE_PERCENT_TORQUE_F462 | Actual engine percent torque | 22F462 | 7E0→7E8 | len 8; val-125; range ..100; percent | — |
| Engine | EVEREST_COOLANT_TEMP_F405 | Coolant temperature | 22F405 | 7E0→7E8 | len 8; val-40; range -40..215; celsius | engineCoolantTemperature |
| Engine | EVEREST_CYL_HEAD_TEMP_SENSOR_2_05FC | Cylinder head temperature sensor 2 | 2205FC | 7E0→7E8 | len 16; signed; val÷64; range -512..511.984375; celsius | — |
| Engine | EVEREST_ENGINE_LOAD_F404 | Engine load | 22F404 | 7E0→7E8 | len 8; val×100; val÷255; range ..100; percent | engineLoad |
| Engine | EVEREST_ENGINE_OIL_TEMP_F45C | Engine oil temperature | 22F45C | 7E0→7E8 | len 8; val-40; range -40..215; celsius | engineOilTemperature |
| Engine | EVEREST_ENGINE_REFERENCE_TORQUE_F463 | Engine reference torque | 22F463 | 7E0→7E8 | len 16; range ..65535; newtonMeters | — |
| Engine | EVEREST_ENGINE_RUN_TIME_F41F | Engine run time | 22F41F | 7E0→7E8 | len 16; range ..65535; seconds | — |
| Engine | EVEREST_ENGINE_SPEED_F40C | Engine speed | 22F40C | 7E0→7E8 | len 16; val÷4; range ..16383.75; rpm | engineSpeed |
| Engine | EVEREST_INTAKE_AIR_TEMP_F40F | Intake air temperature | 22F40F | 7E0→7E8 | len 8; val-40; range -40..215; celsius | — |
| Engine | EVEREST_IAT_FAULT_0700 | Intake temperature fault | 220700 | 7E0→7E8 | len 8; range ..255; scalar | — |
| Engine | EVEREST_IAT_SENSOR_VOLTAGE_1279 | Intake temperature sensor voltage | 221279 | 7E0→7E8 | len 16; val×5; val÷1023; range ..5; volts | — |
| Engine | EVEREST_AIR_CHARGE_TEMP_051C | Intercooler air charge temperature | 22051C | 7E0→7E8 | len 8; val-40; range -40..200; celsius | — |
| Engine.Boost | EVEREST_MAP_SENSOR_VOLTAGE_0301 | Manifold absolute pressure sensor voltage | 220301 | 7E0→7E8 | len 16; val×0.0009765625; range ..5; volts | — |
| Engine.Boost | EVEREST_MANIFOLD_PRESSURE_F40B | Manifold pressure | 22F40B | 7E0→7E8 | len 8; val×10000; val÷68947; range ..36; psi | — |
| Engine.Boost | EVEREST_WASTEGATE_DUTY_CYCLE_0462 | Wastegate duty cycle | 220462 | 7E0→7E8 | len 16; val×100; val÷32768; range ..100; percent | — |
| Engine.Boost.Generic | EVEREST_GENERIC_MAP_010B | Generic MAP | 010B | 7E0→7E8 | len 8; range ..255; kilopascal | — |
| Engine.Generic | EVEREST_GENERIC_MAF_0110 | Air flow rate from mass air flow sensor | 0110 | 7E0→7E8 | len 16; val÷100; range ..655.35; gramsPerSecond | massAirFlow |
| Fuel | FORD_DTE_DISP | Distance to empty, displayed | 224195 | 7E0→7E8 | len 16; val÷10; range ..6553.5; kilometers | fuelRange |
| Fuel | EVEREST_LPFP_SENSOR_VOLTAGE_054D | Fuel pressure sensor voltage, low | 22054D | 7E0→7E8 | len 16; signed; val÷1024; range ..5; volts | — |
| Fuel | EVEREST_LPFP_ACTUAL_0548 | Fuel pressure, low actual | 220548 | 7E0→7E8 | len 16; signed; val×10000; val÷137892; range ..200; psi | — |
| Fuel | EVEREST_LPFP_DESIRED_041F | Fuel pressure, low desired | 22041F | 7E0→7E8 | len 8; val×435; val÷1000; range ..110; psi | — |
| Fuel | EVEREST_FUEL_REMAINING_F42F | Fuel remaining | 22F42F | 7E0→7E8 | len 8; val×100; val÷255; range ..100; percent | fuelTankLevel |
| Fuel | EVEREST_FUEL_TEMP_0522 | Fuel temperature | 220522 | 7E0→7E8 | len 8; val-40; range -40..200; celsius | — |
| Fuel | EVEREST_HPFP_DESIRED_03DC | High pressure fuel pressure desired | 2203DC | 7E0→7E8 | len 16; val×1.4503773969; range ..3500; psi | — |
| Fuel | EVEREST_HPFP_SENSOR_VOLTAGE_0324 | High pressure fuel rail sensor voltage | 220324 | 7E0→7E8 | len 16; val÷204.6; range ..5; volts | — |
| Fuel | EVEREST_LPFP_DUTY_0307 | Low pressure fuel pump commanded duty cycle | 220307 | 7E0→7E8 | len 16; val×0.0030517578; range ..100; percent | — |
| Fuel.Generic | EVEREST_GENERIC_FUEL_LEVEL_012F | Generic fuel level | 012F | 7E0→7E8 | len 8; val×100; val÷255; range ..100; percent | fuelTankLevel |
| Fuel.Testing | EVEREST_TEST_SHORT_TERM_FUEL_TRIM_BANK_1_0106 | Short term fuel trim bank 1 test | 0106 | 7E0→7E8 | len 8; val×100; val÷128; val-100; range -100..99.21875; percent | shortTermFuelTrim |
| Fuel.Testing | EVEREST_TEST_SHORT_TERM_FUEL_TRIM_BANK_2_0108 | Short term fuel trim bank 2 test | 0108 | 7E0→7E8 | len 8; val×100; val÷128; val-100; range -100..99.21875; percent | shortTermFuelTrim |
| Maintenance | EVEREST_OIL_LIFE_REMAINING_054B | Oil life remaining | 22054B | 7E0→7E8 | len 8; range ..100; percent | — |
| Movement | EVEREST_ODOMETER_01A6 | Odometer | 01A6 | 7E0→7E8 | len 32; val×0.1; range ..429496729.5; kilometers | odometer |
| Movement | EVEREST_SPEED_FORD_EXTENDED_F40D_CORRECTED | Vehicle speed Ford extended corrected | 22F40D | 7E0→7E8 | len 8; val+4; range ..259; kilometersPerHour | speed |
| Movement | EVEREST_SPEED_FORD_EXTENDED_F40D | Vehicle speed Ford extended raw | 22F40D | 7E0→7E8 | len 8; range ..255; kilometersPerHour | speed |
| Movement.BodyMotion | FORD_LAT_G | Lateral acceleration | 222B11 | 760→768 | len 16; signed; val÷50; range -1..1; scalar | — |
| Movement.BodyMotion | FORD_LAT_ANG | Lateral angle | 222B0C | 760→768 | len 8; signed; range -90..90; degrees | — |
| Movement.WheelSpeed | FORD_FL_VSS | Front left wheel speed | 222B07 | 760→768 | len 8; range ..200; kilometersPerHour | — |
| Movement.WheelSpeed | FORD_FR_VSS | Front right wheel speed | 222B06 | 760→768 | len 8; range ..200; kilometersPerHour | — |
| Movement.WheelSpeed | FORD_RL_VSS | Rear left wheel speed | 222B09 | 760→768 | len 8; range ..200; kilometersPerHour | — |
| Movement.WheelSpeed | FORD_RR_VSS | Rear right wheel speed | 222B08 | 760→768 | len 8; range ..200; kilometersPerHour | — |
| Transmission | EVEREST_ISS_TSS_1E16_RPM | Input shaft speed ISS/TSS 1E16 | 221E16 | 7E1→7E9 | len 16; val÷4; range ..10000; rpm | — |
| Transmission | EVEREST_OSS_1E15_RPM | Output shaft speed OSS 1E15 | 221E15 | 7E1→7E9 | len 16; val÷4; range ..10000; rpm | — |
| Transmission | EVEREST_TCC_SLIP_1E14 | Torque converter slip speed | 221E14 | 7E1→7E9 | len 16; val÷4; range ..5500; rpm | — |
| Transmission | EVEREST_TRANS_TEMP_1E1C_5_72 | Transmission fluid temperature | 221E1C | 7E1→7E9 | len 16; val×5; val÷72; val-17; range ..200; celsius | — |
| Transmission | EVEREST_GEAR_COMMANDED_7E1_1E12 | Transmission gear commanded | 221E12 | 7E1→7E9 | len 8; range ..10; scalar | — |
| Transmission | EVEREST_GEAR_ENGAGED_7E1_1E1F | Transmission gear engaged | 221E1F | 7E1→7E9 | len 8; range ..10; scalar | transmissionGear |

## Rejected, removed, or archive-only candidates

| PID / cmd | ECU | Signal | Decision | Reason |
| --- | --- | --- | --- | --- |
| 22010D | 7E0→7E8 | Ford vehicle speed candidate | Rejected | Static/stale at 16 km/h; replaced by 22F40D. |
| 010D | 7E0→7E8 | Generic vehicle speed | Rejected / backup only | No useful value in early testing; production uses Ford extended speed. |
| 22010C | 7E0→7E8 | Ford RPM candidate | Rejected | Wrong scale / impossible diesel RPM; replaced by 22F40C. |
| 0111 | 7E0→7E8 | Generic throttle position | Rejected | Negative response / out of range; accelerator pedal 22032B is used instead. |
| 220330 | 7E0→7E8 | Throttle position candidate | Rejected | Negative response / out of range. |
| 22164F | 7E0→7E8 | Fuel rail pressure candidate | Rejected | Negative response / out of range. |
| 221674 | 7E1→7E9 | Transmission temp alternate | Rejected | Negative response / out of range. |
| 225A7A | 7E0→7E8 | PCM gear candidate | Rejected | TCM gear PIDs work instead. |
| 221E1B | 7E1→7E9 | Transmission output shaft speed candidate | Rejected | Negative response / out of range; 221E15 works. |
| 22042C | 7E0→7E8 | DPF soot mass experimental | Rejected | Negative response / out of range. |
| 221440 | 7E0→7E8 | MAP candidate | Rejected | Negative response / out of range; 22F40B / 010B retained. |
| 221442 | 7E0→7E8 | BARO candidate | Rejected | Negative response / out of range. |
| 2203CA | 7E0→7E8 | Intake air temp 2 | Rejected | Negative response / out of range. |
| 220415 | 7E0→7E8 | Oil pressure candidate | Rejected | Negative response / out of range. |
| 7DF 22F45C raw / FORD_EOP | 7DF | Engine oil pressure raw retest | Archive only / removed | Raw value exists but scale/meaning not proven; not active in default.json. |
| 224029 | 726→72E | Battery temperature estimated | Rejected | Negative response / out of range. |
| 220683 | 7E0→7E8 | AdBlue tank level mm | Rejected as main value | Responds but not useful; litres PID is better. |
| 220684 | 7E0→7E8 | AdBlue tank level percentage | Rejected | Wrong meaning/scale; tank had been filled. |
| 2203F4 / 2203F5 / 2203F6 | 7E0→7E8 | EGT alternate candidates | Suspicious / removed | Fixed 255°C-style values; likely wrong formula/default/null. |

## Still unresolved / research queue

| Priority | Target | Status | Notes |
| --- | --- | --- | --- |
| 1 | DPF regeneration active / exhaust filter cleaning state | Not found | High-value target. FORScan config clues suggest strategy exists, but we need a live state PID. |
| 2 | DPF fullness matching dash | Partly found | 22F55D div64 matched Exhaust Filter Full once. Keep monitoring before calling it perfect. |
| 3 | Oil pressure | Not solved | Keep as research only. Do not promote FORD_EOP / 7DF raw evidence without scale proof. |
| 4 | Engine hours / idle hours | Not solved | Engine run time 22F41F is current run time, not lifetime hours. |
| 5 | True boost pressure | Partly found | MAP absolute works. Need clean BARO or calculated boost strategy. |
| 6 | Actual high-pressure fuel rail pressure | Partly found | Desired rail pressure and sensor voltage respond; true actual high-pressure rail still needs confirmation. |
| 7 | Diesel lambda / commanded equivalence | Testing | 0124 and 0144 added as test-only connectables. |
| 8 | Short-term fuel trim on diesel | Testing / likely low value | 0106 and 0108 added to prove whether the PCM exposes anything; do not assume petrol-style behaviour. |
| 9 | Factory TPMS tyre temperature | Low priority | Interesting only if factory sensors/modules expose it; Oricom TPMS is separate. |

## FORScan / Ranger research notes

Source reviewed previously:

- `FORScan - (2024+) 6G Ranger.pdf`
- Scope: North American 2024+ Ranger / 6G Ranger community FORScan data.
- Relevance: Everest UB / U704 is platform-related to Ranger P703 / Next-Gen Ranger.
- Confidence rule: useful as a clue only; it does **not** prove live PID support on this Australian MY25.25 Everest.

Important caution:

> Most FORScan entries are As-Built / feature configuration addresses, not live OBD PIDs. Do not copy them into `default.json` as commands.

| Address | Type | Research clue | PID-log decision |
| --- | --- | --- | --- |
| 720-06-01 | IPC As-Built | Oil pressure / oil temp display clues | Research clue only — do not copy to default.json. |
| 720-07-01 | IPC As-Built | Digital engine/trans temp gauge clue | Supports trans-temp research only. |
| 720-04-01 | IPC As-Built | Engine hours / idle hours display clue | Future research target only. |
| 7D0-10-01 | APIM As-Built | Oil life / menu support clue | Supports oil-life context only. |
| 7E0-162-02 | PCM As-Built | Manual exhaust filter cleaning config clue | Research clue only; not a live PID. |
| 760-04-03 | ABS As-Built | Drive-mode/off-road config clue | FORScan config only; not a live PID. |

## Testing rules going forward

### Add candidates only when useful

Use this path format while testing:

```text
Category.Testing.<identifiable PID name>
```

Examples:

```text
Engine.Testing.OilPressure
Engine.Testing.EngineHours
Engine.Testing.IdleHours
Emissions.Testing.DPFRegenState
Emissions.Testing.ExhaustFilterCleaning
Fuel.Testing.ShortTermFuelTrim
Emissions.Testing.Lambda
Transmission.Testing.IPCTransTempMirror
Tires.Testing.FactoryTPMSTemperature
```

### Promote only when all three are true

- Live response received.
- Value is plausible for the vehicle state.
- Behaviour changes correctly during warm-up, movement, throttle/load, regen, gear changes, or other relevant real-world state.

### Reject quickly when any are true

- Negative response / out of range.
- Blank or stale value.
- Impossible scale.
- Looks like a config address instead of a live PID.
- Duplicates a better existing signal.

## Current default.json recommendation

Keep active:

- Confirmed working signals in the current v0.6.1 layout.
- Raw and corrected Ford extended vehicle speed.
- Ford ABS wheel speeds and body-motion signals, but **not** as `suggestedMetric`.
- Engine oil temperature, engine load, coolant temperature, RPM, DTE, fuel remaining, fuel rate, odometer, DPF soot load, transmission temperature, transmission gear, and starter battery voltage connectables.
- MAF, lambda/equivalence, and short-term fuel-trim entries as **test/compare** connectables only until proven on this diesel.
- Accelerator pedal position as the practical throttle-position connectable.

Keep research-only:

- Oil pressure.
- DPF regen state / exhaust filter cleaning state.
- Engine hours / idle hours.
- True boost pressure calculation.
- Actual high-pressure fuel rail pressure.
- Factory TPMS tyre temperature.

Keep removed:

- Failed `EVEREST_TEST_*` wheel-speed/body-motion guesses.
- `EVEREST_TEST_ENGINE_OIL_PRESSURE_7D-F_F45C_RAW`.
- Static Ford speed candidate `22010D`.
- Bad Ford RPM candidate `22010C`.
- DPF `0579` / `057B` as dash-fullness candidates.
- AdBlue mm / AdBlue percent candidates as primary level values.

## Change log

### 2026-05-08 — v0.6.1 diesel connectables pass

- Added generic MAF `0110` as `massAirFlow`.
- Added short-term fuel trim bank 1/2 test entries `0106` and `0108` as `shortTermFuelTrim`.
- Added wide-range O2/lambda `0124` and commanded equivalence ratio `0144` test entries.
- Mapped confirmed accelerator pedal `22032B` to `throttlePosition` for Pelican usability.
- Deliberately did not add SOCE/SOCR state-of-health entries because they are EV/hybrid-style metrics, not useful Everest diesel health signals.
- Regenerated active-signal and suggestedMetric tables from parsed JSON.

### 2026-05-08 — clean alignment pass

- Rebuilt the testing log against the current `default.json`.
- Added full active-signal table generated from parsed JSON.
- Added connectable/suggestedMetric table.
- Clarified raw vs corrected Ford extended speed.
- Clarified wheel speeds are active signals but not suggestedMetric.
- Clarified oil pressure remains archive-only / research-only.
- Cleaned headings, wording, table structure, and testing rules.
