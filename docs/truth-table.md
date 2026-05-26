# Truth Table

This document defines the truth table for the HVAC two-level threshold logic.

---

## Input Signals

| Symbol | Meaning |
|---|---|
| `P` | Presence detection |
| `A` | High-threshold flag |
| `B` | Low-threshold flag |

Where:

- `P = 1`: someone is in the room
- `P = 0`: no one is in the room
- `A = 1`: temperature is above the high threshold
- `B = 1`: temperature is above the low threshold

Because the high threshold is stricter than the low threshold:

`A = 1 => B = 1`

Therefore:

`A = 1, B = 0`

is an invalid state.

---

## Temperature State Mapping

| A | B | State | Meaning |
|---:|---:|---|---|
| 0 | 0 | LOW | Temperature is below the low threshold |
| 0 | 1 | MEDIUM | Temperature is between low and high thresholds |
| 1 | 1 | HIGH | Temperature is above the high threshold |
| 1 | 0 | FAULT | Impossible threshold state |

---

## HVAC Control Truth Table

| P | A | B | System State | AC | HEAT | VENT | FAULT |
|---:|---:|---:|---|---:|---:|---:|---:|
| 0 | 0 | 0 | Empty room | 0 | 0 | 0 | 0 |
| 0 | 0 | 1 | Empty room | 0 | 0 | 0 | 0 |
| 0 | 1 | 1 | Empty room | 0 | 0 | 0 | 0 |
| 0 | 1 | 0 | Fault detected | 0 | 0 | 0 | 1 |
| 1 | 0 | 0 | Low temperature | 0 | 1 | 0 | 0 |
| 1 | 0 | 1 | Medium temperature | 0 | 0 | 1 | 0 |
| 1 | 1 | 1 | High temperature | 1 | 0 | 0 | 0 |
| 1 | 1 | 0 | Fault detected | 0 | 0 | 0 | 1 |

---

## Design Notes

The system does not treat all binary combinations as valid states.

The state `A = 1, B = 0` is used as a diagnostic signal because it violates the threshold hierarchy.

This helps detect:

- wrong wiring
- wrong sensor mapping
- threshold misconfiguration
- sensor failure
- data processing errors
