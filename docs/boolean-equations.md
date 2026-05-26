# Boolean Equations

This document derives the Boolean equations for the HVAC two-level threshold control logic.

---

## Input Signals

| Symbol | Meaning |
|---|---|
| `P` | Presence detection |
| `A` | High-threshold flag |
| `B` | Low-threshold flag |

Where:

- `P = 1`: someone is in the room
- `A = 1`: temperature is above the high threshold
- `B = 1`: temperature is above the low threshold

Because the high threshold is stricter than the low threshold:

`A = 1 => B = 1`

Therefore:

`A = 1, B = 0`

is an invalid threshold state.

---

## Output Signals

| Symbol | Meaning |
|---|---|
| `AC` | Air conditioning |
| `HEAT` | Heating |
| `VENT` | Ventilation |
| `FAULT` | Fault detection |

---

## Valid Temperature States

| A | B | State |
|---:|---:|---|
| 0 | 0 | LOW |
| 0 | 1 | MEDIUM |
| 1 | 1 | HIGH |
| 1 | 0 | FAULT |

---

## Equation for Air Conditioning

Air conditioning should turn on only when:

- someone is in the room
- the high threshold is active
- the low threshold is also active

Equation:

`AC = P · A · B`

Why include `B`?

In theory, `A = 1` already implies `B = 1`.

However, including `B` makes the output safer because it prevents the air conditioner from turning on during the invalid state:

`A = 1, B = 0`

---

## Equation for Heating

Heating should turn on only when:

- someone is in the room
- the temperature is below the low threshold
- the high threshold is not active

Equation:

`HEAT = P · ¬A · ¬B`

This corresponds to the state:

`A = 0, B = 0`

---

## Equation for Ventilation

Ventilation should turn on when:

- someone is in the room
- the temperature is in the medium range
- the low threshold is active
- the high threshold is not active

Equation:

`VENT = P · ¬A · B`

This corresponds to the state:

`A = 0, B = 1`

---

## Equation for Fault Detection

Fault is detected when:

- the high threshold is active
- the low threshold is inactive

Equation:

`FAULT = A · ¬B`

This state should not happen in a correctly wired and correctly configured system.

---

## Summary

| Output | Equation |
|---|---|
| `AC` | `P · A · B` |
| `HEAT` | `P · ¬A · ¬B` |
| `VENT` | `P · ¬A · B` |
| `FAULT` | `A · ¬B` |

---

## Design Note

The system is designed to fail safely.

If the threshold signals contradict each other, the system does not blindly activate HVAC devices.

Instead, it enters a fault state so the issue can be diagnosed.
