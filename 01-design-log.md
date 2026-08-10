# 01 — Design log

Chronological. Newest entries at the bottom. Written **as the work happens**, not
reconstructed afterwards.

## Rules for entries

1. Every entry is dated.
2. Every decision states the **alternatives considered** and the parameter that decided it.
3. Every calculation is shown, with units, including the standard value actually chosen.
4. Every failure records **symptom → measurement → root cause → change**, in that
   order. "It didn't work, I fixed it" is not a log entry.
5. Dead ends and mistakes stay in the log. They are the most valuable content here.

---

## 2026-08-10 — Project start, requirements fixed

Wrote `00-requirements.md`. Scope deliberately minimal: the objective is one
complete pass through the full workflow, not an impressive circuit.

Decided against including USB data and PD negotiation. Reasoning: both would add
failure modes I cannot yet debug (differential pair impedance, PD state machine),
and neither serves any requirement. Deferred to Board 3.

Set up repository and folder structure. KiCad 10.0.5 installed.

**Next:** hand-draw the circuit, calculate all component values.

---

## EXAMPLE ENTRY — delete once you have real ones

## 2026-XX-XX — 555 timing network calculation

Target: 1 Hz, ~50 % duty cycle (FR-4).

Standard astable configuration, NE555 datasheet section 8.3.3:

    t_high = 0.693 × (R1 + R2) × C
    t_low  = 0.693 × R2 × C
    T      = t_high + t_low = 0.693 × (R1 + 2×R2) × C

Chose C = 10 µF (ceramic X7R, 0805, 16 V) to keep resistor values in a sane
range. For T = 1 s:

    R1 + 2×R2 = 1 / (0.693 × 10e-6) = 144.3 kΩ

Duty cycle = (R1 + R2) / (R1 + 2×R2). A true 50 % is impossible in this topology
because t_high always exceeds t_low. Targeting the lowest achievable duty within
the FR-4 window of 40–60 %: make R1 small relative to R2.

    R1 = 4.7 kΩ  ->  R2 = (144.3 - 4.7)/2 = 69.8 kΩ  ->  use 68 kΩ (E12)

Recalculated with standard values R1 = 4.7 kΩ, R2 = 68 kΩ:

    T = 0.693 × (4700 + 136000) × 10e-6 = 0.975 s      -> within FR-4 (1.0 s ±0.2 s)
    Duty = (4700 + 68000) / 140700 = 51.7 %            -> within FR-4 (40-60 %)

**Risk noted:** X7R ceramic capacitors lose significant capacitance under DC bias
and have ±15 % tolerance over temperature. Expected period spread is therefore
roughly ±20 %, sitting right at the FR-4 limit. Accepted for Rev A because the
requirement is loose and this is an indicator, not a clock. **To verify at
bring-up (SC-4).** If it fails, Rev B uses a film capacitor or a trimmer for R2.

---

## EXAMPLE ENTRY — delete once you have real ones

## 2026-XX-XX — Bring-up failure: 3.3 V rail dead

**Symptom:** Green 5 V LED lit, red 3.3 V LED dark.

**Measurements:**
- Input at USB-C: 5.02 V — OK
- U2 (AP2112K) pin 1 VIN: 5.01 V — OK, so power reaches the regulator
- U2 pin 5 VOUT: 0.00 V
- U2 pin 3 EN: 0.00 V  ← anomaly
- Continuity EN to VIN: open

**Root cause:** The AP2112K enable pin is active-high and has no internal pull-up.
I left EN floating, assuming it defaulted enabled. Datasheet section 7.3.2 states
EN must be driven above 1.2 V; a floating pin leaves the regulator in shutdown.
This is a **schematic error that ERC could not catch**, because an unconnected
input pin was still connected to a net — it was simply the wrong net (none).

**Immediate fix:** bodge wire from pin 3 to pin 1. Rail came up at 3.29 V.

**Rev B change:** 100 kΩ pull-up from EN to VIN, on the schematic, with a note.
**Process change:** added "check every IC enable/reset/mode pin against the
datasheet" to the pre-ERC schematic review checklist. This class of error —
a pin that is electrically fine but functionally wrong — is invisible to ERC,
so it needs a human checklist.
