# 02 — Component selection

Every active component and every non-obvious passive gets an entry here: at least
two candidates, compared on the parameter that actually decided it, plus the
calculation that proves the choice is safe.

Datasheets are stored as files in `datasheets/`, not as links. Links rot.

---

## Selection summary

| Ref | Function | Chosen part | Package | Deciding parameter | Unit cost |
|---|---|---|---|---|---|
| U1 | 3.3 V LDO | AP2112K-3.3TRG1 | SOT-23-5 | Dropout + thermal | |
| U2 | Astable oscillator | NE555 (or TLC555) | SOIC-8 | | |
| J1 | USB-C receptacle | | 16-pin SMD | | |
| D1 | Reverse polarity | | | | |

---

## EXAMPLE ENTRY — U1, 3.3 V regulator

### Requirement
ER-5: 3.3 V, ≤ 250 mA, ±5 %, from a 5 V input (ER-2).

### Candidates

| Parameter | AMS1117-3.3 | AP2112K-3.3 | Why it matters |
|---|---|---|---|
| Dropout at 250 mA | ~1.1 V | 0.25 V | With 4.75 V worst-case input, AMS1117 needs 4.4 V minimum — marginal. AP2112K needs 3.55 V — comfortable. |
| Quiescent current | 5 mA typ | 55 µA typ | ER-7 budgets 20 mA total. 5 mA of that spent on the regulator doing nothing is 25 % of the budget wasted. |
| Accuracy | ±2 % | ±1.5 % | Both satisfy ER-5. Not the deciding factor. |
| Package | SOT-223 | SOT-23-5 | SOT-223 has a large thermal pad — better heat spreading, but bigger. |
| θ_JA | ~62 °C/W (SOT-223, with copper pour) | ~150 °C/W (SOT-23-5) | Directly sets junction temperature — see calculation below. |
| Enable pin | none | yes, active high, **no internal pull-up** | Extra part needed (pull-up), but allows future power sequencing. Flagged as an error risk. |
| Price / stock | cheaper, ubiquitous | slightly more, good stock | Not deciding. |

### Thermal calculation (the actual constraint)

A linear regulator dissipates the voltage it drops, times the current through it:

    P = (V_in − V_out) × I_load

Worst case for this board, at the full 250 mA of ER-5, with the maximum input of
5.25 V (ER-2):

    P = (5.25 − 3.3) × 0.250 = 0.49 W

Junction temperature, AP2112K in SOT-23-5, θ_JA ≈ 150 °C/W, ambient 25 °C:

    T_j = 25 + (0.49 × 150) = 98 °C

Datasheet maximum junction temperature is 125 °C, so this passes — but with only
27 °C of margin, and margin evaporates in a warm room or an enclosure.

**Realistic load check:** the actual on-board load is the 555 plus two LEDs,
under 15 mA:

    P = (5.25 − 3.3) × 0.015 = 0.029 W  ->  T_j = 25 + 4.4 = 29 °C

Comfortable.

### Decision

**AP2112K-3.3**, chosen for dropout headroom and quiescent current (which
protects ER-7). The thermal result means the ER-5 headline rating of 250 mA is
only valid at room temperature with airflow; **documenting this as a known
limitation rather than pretending the board is a 250 mA supply.**

If a later board needs a sustained 250 mA at 3.3 V from 5 V, the correct answer
is a buck converter, not a bigger LDO — 0.49 W thrown away as heat is 40 % of the
delivered power.

### Risk registered
No internal pull-up on EN. Add 100 kΩ EN→VIN in the schematic and verify at
review. (See design log — this is a known trap.)
