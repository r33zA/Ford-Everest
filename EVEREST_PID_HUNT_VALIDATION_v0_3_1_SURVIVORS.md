# EVEREST PID HUNT VALIDATION — v0.3.1 Survivors Only

Date: 2026-05-17  
Vehicle target: Ford Everest Trend MY25.25, 2.0L Bi-Turbo Diesel, 10AT, full-time 4WD  
Purpose: reduced survivor-focused validation file based on first v0.3 Pelican results.

## Summary

| Item | Count |
| --- | ---: |
| Commands | 23 |
| Signals | 58 |
| Suggested metrics preserved | 11 |
| Duplicate signal IDs | 0 |
| Source baseline | `everest_pid_hunt_validation_v0_3.json` |

## Why v0.3.1 exists

v0.3 proved several candidates were dead on this Everest and several were genuinely alive.

This version removes obvious non-responders and focuses on signals that either responded with a real value, responded with a valid zero, or are required as known-good references.

## Removed / deprioritised from v0.3

- TPMS `2813–2816`
- Tyre size `16F0`
- TCM guesses `1E20`, `1E30`
- fuel rail pressure/injection block `1610–1615`
- fuel rail temp `168E`
- steering angle `3201`
- boost/VGT block `1441`, `1442`, `16C4–16C7`
- IAT2 `03CA`
- BCM guesses `4029`, `402C`, `402D`
- DPF pressure `09E2`, `116C`
- regen history `FD86–FD89`
- distance since regen `0434`
- soot `042C`
- exhaust back pressure `1445`

## Survivors worth testing further

| PID | Result seen | Current interpretation |
| --- | --- | --- |
| `220610` | raw `5948`, decoded `59.48` | Strong soot/load candidate |
| `220614` | raw `1149`, decoded `115 km` | Strong DPF distance/counter candidate |
| `220373` | valid `0` | DPF pressure candidate; test under load |
| `220579` | valid `0` | DPF load/status candidate; may need regen/distance |
| `22057B` | valid `0` | DPF soot/open-loop candidate; may need regen/distance |
| `220440` | valid `0` | Regen status bitfield candidate |
| `22F48B` | raw A `127`, raw AB `32512` | Strong status/bitfield candidate |
| `22F478` | first word raw `1797`, raw packet alive | Multi-EGT packet candidate; decode still unknown |
| `22F463` | raw `500`, decoded `10°C` | Plausible EGRT/manifold temp candidate |
| `22F460` | raw `65481`, decoded value bad | Alive but current formula likely wrong/signed/invalid |

## Best next test

Graph/log:

- `0610`
- `0614`
- `0373`
- `0579`
- `057B`
- `0440`
- `F48B`
- `F478`
- `F463`
- existing EGT `0569`
- coolant temp
- RPM
- speed
- engine load
- accelerator pedal

## Commit message

```text
Reduce v0.3 PID hunt to survivor validation set
```

## Extended description

```text
Created v0.3.1 of the Ford Everest MY25.25 PID validation set based on initial Pelican test results.

This version removes obvious negative-response candidates from v0.3 and focuses the validation file on PIDs that responded with live values or valid zeroes. The strongest current survivors are 220610, 220614, 220373, 220579, 22057B, 220440, 22F48B, 22F478, 22F463, and 22F460.

Additional survivor-specific alternate formulas and raw companion values were added to help determine whether these candidates represent DPF soot/load, distance since regen or DPF counter data, DPF pressure, regen status bitfields, and exhaust temperature packets.

Validation:
- JSON parses successfully.
- No duplicate signal IDs.
- Known reference signals retained.
- Dead v0.3 exploratory groups removed or deprioritised.
```
