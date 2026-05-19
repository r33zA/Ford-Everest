# Ford Everest MY25.25 PID Testing Log

Version: 2026-05-17 v0.6.4 DPF promoted — testing root folder tidy  
Aligned default file: `default_everest_my25_25_v0_6_4_dpf_promoted_testing_root.json` / `signalsets/v3/default.json` target

## Update focus

- Kept the v0.6.4 DPF promotion baseline.
- Moved all temporary DPF and exhaust testing signals into the root `Emissions.Testing` folder.
- This keeps all live test items together for easier screenshots while driving.
- Stable promoted DPF signals remain in `Emissions.DPF`.
- Existing O2 / commanded lambda testing signals remain in `Emissions.Testing`.

## Path changes

| Signal | Old path | New path |
| --- | --- | --- |
| `EVEREST_TEST_DPF_REGEN_STATUS_0440_RAW8_A` | `Emissions.DPF.Testing` | `Emissions.Testing` |
| `EVEREST_TEST_DPF_STATUS_F48B_RAW8_A` | `Emissions.DPF.Testing` | `Emissions.Testing` |
| `EVEREST_TEST_DPF_STATUS_F48B_RAW16_AB` | `Emissions.DPF.Testing` | `Emissions.Testing` |
| `EVEREST_TEST_EGRT_F463_CELSIUS` | `Emissions.Exhaust.Testing` | `Emissions.Testing` |
| `EVEREST_TEST_EGRT_F463_RAW16_AB` | `Emissions.Exhaust.Testing` | `Emissions.Testing` |
| `EVEREST_TEST_EXHAUST_MULTI_EGT_F478_RAW16_AB` | `Emissions.Exhaust.Testing` | `Emissions.Testing` |
| `EVEREST_TEST_EXHAUST_MULTI_EGT_F478_CANDIDATE_C` | `Emissions.Exhaust.Testing` | `Emissions.Testing` |

## Sanity-check snapshot

| Check | Result |
| --- | ---: |
| Commands | 69 |
| Signals | 77 |
| Signals with suggestedMetric | 21 |
| Testing-path signals moved | 7 |
| Duplicate signal IDs | 0 |
| JSON validation | Passed |

## Commit message

```text
Group emissions test PIDs under root Testing
```

## Extended description

```text
Moved the temporary DPF and exhaust validation PIDs from nested testing folders into the root Emissions.Testing folder.

This keeps all active emissions test signals together in Pelican, making them easier to view, enable, and screenshot during live driving tests.

Stable promoted signals remain unchanged:
- EVEREST_DPF_FULLNESS_0610 stays under Emissions.DPF.
- EVEREST_DISTANCE_SINCE_DPF_REGEN_0614 stays under Emissions.DPF.

Moved testing-only signals:
- 220440 DPF regen status raw A
- 22F48B DPF status raw A / raw AB
- 22F463 EGRT Celsius / raw AB
- 22F478 multi-EGT candidate / raw AB

Validation:
- JSON parses successfully.
- No duplicate signal IDs.
- Command and signal counts unchanged.
```
