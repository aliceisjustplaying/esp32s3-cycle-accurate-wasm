# esp32s3-cycle-accurate-wasm

The program office for a browser-hosted, wasm-based, cycle-accurate
ESP32-S3 emulator. The product itself is built in our
[esp32sim fork](https://github.com/aliceisjustplaying/esp32sim); this
repository carries everything that governs and proves it: decisions,
roadmap, lane briefs, review archives, the timing lab and its
hardware-measured receipts, and the experiment records.

The product in one sentence: the complete ESP32-S3 SoC plus the exact
Waveshare ESP32-S3-Touch-AMOLED-1.8 board, running real firmware in the
browser in real time, with cycle accounting calibrated against real
silicon under the tiered claim vocabulary of
[decision 0008](decisions/0008-tiered-cost-vocabulary-and-acceptance-bounds.md).

## Map

- [`roadmap.md`](roadmap.md): the plan, revision 4. Start here.
- [`STATUS.md`](STATUS.md): the running ledger of what happened and what
  is authoritative.
- [`decisions/`](decisions/): the numbered decision journal (0008
  onward; 0001 through 0007 predate this project and live in the puck
  archive, see [`PROVENANCE.md`](PROVENANCE.md)).
- [`lanes/`](lanes/): one implementation brief per roadmap lane, plus
  the coordinator brief. An agent's complete handoff is this repository
  and one lane letter.
- [`reviews/`](reviews/): archived external reviews with maintainer
  dispositions.
- [`timing/`](timing/): the timing lab: hardware-measured cost receipts
  and the TypeScript timing machine (retained for the one-shot
  differential gate of
  [decision 0014](decisions/0014-measured-scheduler-and-adapter-contract.md),
  then donor history).
- [`experiments/`](experiments/): experiment records with receipts
  (browser speed ceilings, the esp32sim adoption boot, the flexe
  execution-core experiment, the QEMU oracle survey).

## The four repositories

| Repository | Role |
| --- | --- |
| this one | Program office: decisions, roadmap, lanes, receipts, reviews |
| [aliceisjustplaying/esp32sim](https://github.com/aliceisjustplaying/esp32sim) | The product: Rust machine, measured mode, adapter, wasm bridge, web shell. `main` mirrors upstream; `puck/base` carries the fork; `lane-*/` branches carry work |
| [aliceisjustplaying/puck](https://github.com/aliceisjustplaying/puck), branch `codex/esp32s3-timing-model` | Frozen archive and donor: where this project was born; UI, recorder, and harness code ported selectively with provenance (decision 0013) |
| [aliceisjustplaying/tinydraw](https://github.com/aliceisjustplaying/tinydraw) | The reference firmware and the hardware calibration probe projects |

## Onboarding an agent

Read [`AGENTS.md`](AGENTS.md), then [`roadmap.md`](roadmap.md), then the
decisions it cites, then your lane brief in [`lanes/`](lanes/). The
coordinator brief is [`lanes/COORDINATOR.md`](lanes/COORDINATOR.md).
