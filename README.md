# B01 — Caveman Brick

This is where I started. A USB-C brick that steps 5 V down to 3.3 V and blinks
an LED off an NE555. I wanted one project
small enough to get wrong in every way a first board usually goes wrong, so
the next one wouldn't have to, at least not in the same ways :)

| | |
|---|---|
| **Status** | Rev A — schematic done, ERC clean |
| **Started** | 2026-08-10 |
| **Designer** | Vasii Vlad Andrei |
| **EDA tool** | KiCad 10.0.5 |
| **Layers** | 2 |
| **Dimensions** | 50 × 40 mm (target) |
| **Fabricator** | TBD |


## Key learnings

<!-- Fill in after the postmortem. 3-5 bullets, specific and technical.
     "I learned KiCad" is worthless. "I learned that an LDO's thermal
     resistance, not its current rating, was the real constraint" is not. -->

_Not yet built._

## Repository map

Not every folder below has much in it yet — this board's log stayed light
since it was mostly about learning the tools, not the circuit. The next board that will get my next sleepless night gets
the full treatment.

| Path | Contents |
|---|---|
| `00-requirements.md` | What the board must do, and how success is measured |
| `01-design-log.md` | Dated chronological log of every decision, calculation and mistake |
| `02-component-selection/` | Part comparison tables and datasheets |
| `03-schematic/` | KiCad project files and exported schematic PDFs per revision |
| `04-layout/` | Stackup, design rules, DRC reports, 3D renders |
| `05-bom/` | Bill of materials |
| `06-assembly/` | Assembly photos and notes |
| `07-bringup/` | Power-on checklist and measured results |
| `08-test/` | Test plan and measurements against the success criteria |
| `09-postmortem.md` | What failed, root causes, Rev B change list |

## Reproducing

KiCad 10.0.5 or later. Open `03-schematic/board01.kicad_pro`. Fabrication
outputs for the released revision are in `04-layout/gerbers/`.

## License

Hardware: CERN-OHL-S-2.0. Documentation: CC-BY-4.0.
