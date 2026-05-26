
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

Instead of using independent `hot` and `cold` signals, this design uses two hierarchical threshold flags:

- `B`: low-threshold flag
- `A`: high-threshold flag

Because the high threshold is stricter than the low threshold:

```text
A = 1  =>  B = 1
