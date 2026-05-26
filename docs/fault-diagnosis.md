# Fault Diagnosis

This document explains how the HVAC two-level threshold logic detects invalid or contradictory states.

---

## Core Rule

The system uses two hierarchical threshold flags:

| Symbol | Meaning |
|---|---|
| `B` | Low-threshold flag |
| `A` | High-threshold flag |

Because the high threshold is stricter than the low threshold:

`A = 1 => B = 1`

Therefore, this state is invalid:

`A = 1, B = 0`

---

## Why `A = 1, B = 0` Is a Fault

If the temperature is high enough to activate the high-threshold flag, it must also be high enough to activate the low-threshold flag.

So the state:

| A | B | Meaning |
|---:|---:|---|
| 1 | 0 | High threshold active, low threshold inactive |

should not happen in a correctly working system.

Instead of treating this state as normal data, the system marks it as:

`FAULT`

---

## Possible Causes

The invalid state may indicate one of the following problems:

| Cause | Explanation |
|---|---|
| Wrong wiring | The high-threshold and low-threshold signals may be connected to the wrong pins |
| Wrong GPIO mapping | The firmware may read the wrong input pin for `A` or `B` |
| Sensor failure | One of the sensors or comparator outputs may be stuck at `0` or `1` |
| Threshold misconfiguration | The high threshold may be configured lower than the low threshold |
| Inverted comparison logic | A comparison such as `>` or `<` may be implemented incorrectly |
| Data processing error | The backend or firmware may map the threshold flags incorrectly |
| Timing mismatch | `A` and `B` may be sampled at different times, causing inconsistent readings |

---

## Safe Behavior

When the system detects:

`A = 1, B = 0`

it should:

1. Disable main HVAC actions if they depend on the invalid input.
2. Raise a fault signal.
3. Log the fault for diagnosis.
4. Avoid silently treating the data as a normal temperature state.

---

## Recommended Fault Output

| Fault Type | Condition | Suggested Action |
|---|---|---|
| Threshold contradiction | `A = 1, B = 0` | Disable normal control and report fault |
| Configuration error | `high_threshold <= low_threshold` | Reject configuration |
| Sensor stale | Sensor data has not updated for too long | Mark sensor as unreliable |
| Mapping error | Input flags do not match expected behavior | Check wiring and firmware mapping |

---

## Design Principle

A reliable system should ask two questions:

1. What should I do when the input is valid?
2. What should I do when the input should not be possible?

The second question is what turns simple control logic into fault-aware system design.

---

## Example

Assume:

- Low threshold = 25°C
- High threshold = 30°C

If the measured temperature is 32°C:

| Signal | Expected Value |
|---|---:|
| `A` | 1 |
| `B` | 1 |

If the system reads:

| Signal | Actual Value |
|---|---:|
| `A` | 1 |
| `B` | 0 |

then the system should not treat the reading as a normal high-temperature state.

It should treat it as a fault.

---

## Summary

The `FAULT` state is not an extra feature.

It is a safety boundary.

It prevents the system from trusting contradictory data and helps diagnose problems before they become control errors.
