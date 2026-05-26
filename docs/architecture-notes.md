# Architecture Notes

This document explains the design decisions behind the HVAC two-level threshold logic.

---

## 1. Design Goal

The goal is to design a small, clear, and fault-aware HVAC control logic.

The system should:

- control heating, ventilation, and air conditioning
- turn off all devices when the room is empty
- classify temperature into low, medium, and high states
- detect impossible threshold states
- avoid unsafe output when the input is contradictory

---

## 2. Why Use Two-Level Threshold Logic?

A simple design may use separate `hot` and `cold` signals.

However, this design uses two hierarchical threshold flags:

- `B`: low-threshold flag
- `A`: high-threshold flag

This creates a natural state progression:

| A | B | State |
|---:|---:|---|
| 0 | 0 | LOW |
| 0 | 1 | MEDIUM |
| 1 | 1 | HIGH |
| 1 | 0 | FAULT |

This is useful because the invalid state can be used for diagnosis.

---

## 3. Valid State Design

Not every binary combination should be treated as valid.

Because the high threshold is stricter than the low threshold:

`A = 1 => B = 1`

So the state:

`A = 1, B = 0`

should not happen.

Instead of ignoring it, the system treats it as a fault.

---

## 4. Fail-Safe Output Design

The output equations are written to avoid unsafe activation.

| Output | Equation |
|---|---|
| `AC` | `P · A · B` |
| `HEAT` | `P · ¬A · ¬B` |
| `VENT` | `P · ¬A · B` |
| `FAULT` | `A · ¬B` |

The air conditioner uses:

`AC = P · A · B`

instead of only:

`AC = P · A`

This prevents the air conditioner from turning on during the invalid state:

`A = 1, B = 0`

---

## 5. Separation of Concerns

The design separates the system into four layers:

| Layer | Responsibility |
|---|---|
| Sensor layer | Read physical temperature / threshold signals |
| Logic layer | Convert threshold flags into system states |
| Output layer | Control AC, heating, and ventilation |
| Diagnosis layer | Detect invalid or contradictory states |

This makes the system easier to explain, test, and extend.

---

## 6. Why This Is More Than a Truth Table

The truth table is not only used to control outputs.

It also defines:

- valid states
- invalid states
- safety behavior
- diagnostic meaning
- system boundaries

This turns a simple logic exercise into a small architecture pattern.

---

## 7. Possible Extensions

This design can be extended with:

- hysteresis to avoid rapid switching
- humidity input
- CO2 or air-quality sensor
- fan speed control
- sensor timeout detection
- event logging
- backend dashboard
- database storage for fault history

---

## 8. Design Principle

Architecture reduces ambiguity before implementation starts.

For this case:

- natural-language requirement becomes input/output definitions
- temperature behavior becomes binary state logic
- invalid combinations become diagnostic signals
- outputs are constrained by fail-safe equations

---

## Summary

This case demonstrates a small but important idea:

A system should not only decide what to do when things are normal.

It should also know when the input no longer makes sense.
