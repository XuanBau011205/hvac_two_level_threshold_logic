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

```text
P = 1: someone is in the room
P = 0: no one is in the room

A = 1: temperature is above the high threshold
B = 1: temperature is above the low threshold
