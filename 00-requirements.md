# 00 — Requirements

Written before any schematic work. Every later decision must trace back to a line
in this document, or it is arbitrary.

**Revision:** A
**Date:** 2026-08-10

---

## 1. Purpose

A USB-C powered breakout board providing regulated 5 V and 3.3 V rails and a
visible ~1 Hz blink, used to validate the complete design-to-bring-up workflow
and as a general-purpose bench power breakout afterwards.

## 2. Electrical requirements

| ID | Requirement | Value |
|---|---|---|
| ER-1 | Input source | USB-C receptacle, USB 2.0, power only |
| ER-2 | Input voltage | 5 V nominal, 4.75–5.25 V |
| ER-3 | Input current budget | ≤ 500 mA total |
| ER-4 | Output rail 1 | 5 V, passthrough, ≤ 500 mA |
| ER-5 | Output rail 2 | 3.3 V regulated, ≤ 250 mA, ±5 % |
| ER-6 | Reverse polarity | Board must survive reversed input on the header |
| ER-7 | Quiescent current | ≤ 20 mA with both indicator LEDs lit |

## 3. Functional requirements

| ID | Requirement |
|---|---|
| FR-1 | Board is powered from any standard USB-C source (charger, PC port, power bank) |
| FR-2 | A green LED indicates the 5 V rail is live |
| FR-3 | A red LED indicates the 3.3 V rail is live |
| FR-4 | An NE555 in astable configuration blinks an LED at 1 Hz ±20 %, duty cycle 40–60 % |
| FR-5 | 5 V, 3.3 V, GND and the 555 output are exposed on a 2.54 mm header |
| FR-6 | Every rail and the 555 output are probe-accessible without removing components |

## 4. Mechanical requirements

| ID | Requirement | Value |
|---|---|---|
| MR-1 | Board size | ≤ 50 × 40 mm |
| MR-2 | Layer count | 2 |
| MR-3 | Thickness | 1.6 mm |
| MR-4 | Mounting | 4 × M3 clearance holes (3.2 mm), 5 mm from each corner |
| MR-5 | Connector position | USB-C flush with one board edge |
| MR-6 | Passive package | 0805 minimum (hand-solderable) |

## 5. Success criteria — how "it works" is measured

| ID | Criterion | Measurement method |
|---|---|---|
| SC-1 | 3.3 V rail measures 3.30 V ±5 % at no load | DMM at header pin |
| SC-2 | 3.3 V rail stays within ±5 % at 200 mA load | DMM, resistive load |
| SC-3 | Total input current with no external load ≤ 20 mA | USB inline power meter |
| SC-4 | Blink period 1.0 s ±0.2 s | Stopwatch over 20 cycles, or scope |
| SC-5 | No component exceeds 60 °C after 10 min continuous operation | Finger test, then IR thermometer if available |
| SC-6 | Board is recognised as a load by a USB-C charger (CC resistors correct) | Board powers up from a USB-C PD charger, not only from a USB-A adapter cable |

## 6. Out of scope

Explicitly not doing, and why:

- **USB data lines** — no data function required; would add differential-pair
  routing complexity that belongs on Board 3.
- **USB-C Power Delivery negotiation** — 5 V default is sufficient for the
  current budget; a PD sink controller is unjustified complexity here.
- **Switching regulation** — at ≤ 250 mA on the 3.3 V rail, LDO dissipation is
  acceptable (see `02-component-selection/`). Efficiency is not a requirement.
- **Battery operation** — no requirement.

## 7. Open questions

- [ ] Reverse polarity via series Schottky or P-channel MOSFET? (see design log 2026-XX-XX)
- [ ] Header pinout order — decide before layout
