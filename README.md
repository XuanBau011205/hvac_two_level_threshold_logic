# HVAC Two-Level Threshold Logic

This repository is a mini system design case study for an HVAC control system.

The goal is to convert a natural-language requirement into:

- input/output definitions
- truth table
- Boolean equations
- fault-state detection
- clear technical documentation

---

## Problem

Design a control logic for an HVAC system in an office room.

When someone is in the room:

- turn on air conditioning when the room temperature is high
- turn on heating when the room temperature is low
- turn off both air conditioning and heating when the temperature is in the medium range
- turn on ventilation to increase fresh air and oxygen in the room

When no one is in the room:

- turn off all HVAC devices

---

## Core Idea

Instead of using independent hot/cold signals, this design uses two hierarchical threshold flags:

- `B`: low-threshold flag
- `A`: high-threshold flag

Because the high threshold is stricter than the low threshold:

`A = 1 => B = 1`

Therefore, the state:

`A = 1, B = 0`

is logically impossible in a correctly wired and correctly configured system.

This impossible state is treated as a fault signal.

---

## Inputs

| Symbol | Meaning |
|---|---|
| `P` | Presence detection |
| `A` | High-threshold flag |
| `B` | Low-threshold flag |

Where:

- `P = 1`: someone is in the room
- `P = 0`: no one is in the room
- `B = 1`: temperature is above the low threshold
- `A = 1`: temperature is above the high threshold

---

## Outputs

| Symbol | Meaning |
|---|---|
| `AC` | Air conditioning |
| `HEAT` | Heating |
| `VENT` | Ventilation |
| `FAULT` | Fault detection |

---

## Temperature State Mapping

| A | B | Temperature State | Meaning |
|---:|---:|---|---|
| 0 | 0 | LOW | Below low threshold |
| 0 | 1 | MEDIUM | Between low and high threshold |
| 1 | 1 | HIGH | Above high threshold |
| 1 | 0 | FAULT | Impossible state |

---

## HVAC Truth Table

| P | A | B | State | AC | HEAT | VENT | FAULT |
|---:|---:|---:|---|---:|---:|---:|---:|
| 0 | 0 | 0 | Room empty | 0 | 0 | 0 | 0 |
| 0 | 0 | 1 | Room empty | 0 | 0 | 0 | 0 |
| 0 | 1 | 1 | Room empty | 0 | 0 | 0 | 0 |
| 0 | 1 | 0 | Fault detected | 0 | 0 | 0 | 1 |
| 1 | 0 | 0 | Low temperature | 0 | 1 | 0 | 0 |
| 1 | 0 | 1 | Medium temperature | 0 | 0 | 1 | 0 |
| 1 | 1 | 1 | High temperature | 1 | 0 | 0 | 0 |
| 1 | 1 | 0 | Fault detected | 0 | 0 | 0 | 1 |

---

## Boolean Equations

| Output | Equation | Meaning |
|---|---|---|
| `AC` | `P · A · B` | Cooling is active only when someone is present and high temperature is confirmed |
| `HEAT` | `P · ¬A · ¬B` | Heating is active when someone is present and temperature is below the low threshold |
| `VENT` | `P · ¬A · B` | Ventilation is active when someone is present and temperature is in the medium range |
| `FAULT` | `A · ¬B` | Fault is detected when the high threshold is active but the low threshold is inactive |

Notes:

- `AC = P · A · B` prevents cooling from turning on during the invalid `A = 1, B = 0` state.
- `FAULT = A · ¬B` detects threshold contradiction.
- When `P = 0`, all HVAC devices stay off.

---

## Logic Flow

1. Read temperature input.
2. Compare with low threshold to create `B`.
3. Compare with high threshold to create `A`.
4. Check whether the threshold state is valid.
5. Use the truth table to choose the HVAC output.
6. If `A = 1, B = 0`, enter `FAULT` state.

Flow:

`Temperature Input → Threshold Flags → Truth Table → HVAC Output / Fault`

---

## Fault Diagnosis

The state `A = 1, B = 0` may indicate:

- wrong wiring
- wrong GPIO mapping
- threshold misconfiguration
- sensor failure
- inverted comparison logic
- data processing error

A good control system should not silently process impossible states as normal data.

---

## Design Principle

A system should not only handle valid states.

It should also detect states that should never happen.

This case demonstrates how a simple truth table can become a small safety logic pattern.

---

## Repository Purpose

This repository is not a full HVAC implementation.

It is a small technical design case that demonstrates:

- requirement analysis
- binary state modeling
- truth-table design
- Boolean logic
- fault-state detection
- architecture documentation
