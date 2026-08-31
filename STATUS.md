# Status

Last updated 2026-08-31. Authoritative documents: [`roadmap.md`](roadmap.md)
(revision 4) and [`decisions/`](decisions/) 0006 through 0014, with
[0013](decisions/0013-product-identity-fork-owns-the-product.md) (the
esp32sim fork owns the product) and
[0014](decisions/0014-measured-scheduler-and-adapter-contract.md) (the
accepted measured scheduler and adapter contract) governing current work.
Incubation history lives in the puck archive (see
[`PROVENANCE.md`](PROVENANCE.md)); receipts live in [`timing/`](timing/),
[`reviews/`](reviews/), [`experiments/`](experiments/), and
[`lanes/receipts/`](lanes/receipts/).

## Lane state

On 2026-08-31 the maintainer collapsed the original ten lanes into
four: CORE (was B, C), BOARD (was 0, A, E), SPEED (was D), SHIP (was F,
G, H). The briefs in [`lanes/`](lanes/README.md) carry the mapping.

- **CORE**: the measured-mode design spike is dispositioned as decision
  0014; implementation has not started and goes to a blank-slate agent
  per [`lanes/COORDINATOR.md`](lanes/COORDINATOR.md). Schema-1
  `timing.json` stays rejected for measured totals (it loses the affine
  MMIO intercept, the committed evidence is `3n - 8`); timing-profile
  schema 2 belongs to CORE. Phase 2 (dual-core contention) waits for
  phase 1's exit; its interleave decision record may be drafted early.
- **SPEED**: waiting on upstream contact before work starts.
- **SHIP**: CI is built, verified, and pushed to the fork
  (`lane-g/ci-spec` at `6ba6a6d`, upstream-shaped `lane-g/upstream-ci`
  at `3b58cc6`): pinned actions, fail-closed decoder corpora with
  visible case counts and hashes (10 Xtensa, 9 RISC-V, zero
  mismatches), boundary defects proven to fail. Blocker awaiting
  maintainer disposition: pre-existing fork-wide rustfmt debt (893
  hunks across 39 files at base `aa851249`). Boundary review starts
  when CORE's validator seam lands; shell and release wait for CORE and
  SPEED.
- **BOARD**, absorbing the completed toolchain flag day and the silicon
  service: the ESP-IDF 6.1 flag day is complete. All
  fixtures rebuilt and pinned on v6.1 with xtensa-esp-elf 15.2.0 (hashes
  below). Four timing boots recovered 802 passing receipts across 210
  identities; 204 meet the strict two-independent-receipt criterion.
  Silicon-architectural headlines unchanged (35-cycle window pair,
  1.000 cycles per straight-line instruction, +1 loop alignment at +3
  mod 4). IDF-owned timing moved as recorded: level-1 interrupt
  entry/resume 228/142 to 227/143, level-3 223/138 to 222/139, median
  boot to first output 0.582 s to 0.472 s. Evidence:
  [`timing/evidence/idf61-rebaseline-3db3985/`](timing/evidence/idf61-rebaseline-3db3985/README.md).
  Open: a systematic one-cycle first-line cache probe shift is retained
  as a diagnostic, not adopted; that cost class stays blocked in the
  importer until the pooling-probe diagnosis and adoption disposition
  land. Six identities remain below strict recovery (two with zero
  receipts, four with one) after repeated USB capture truncation.
  Continuing BOARD: chip identity is captured and accepted (request
  record [`A-01`](lanes/requests/A-01-v2-controller-and-identity.md), a
  retired format kept as a receipt); the touch controller stays unnamed
  until the capture identifies it (decision 0014). A synchronous GP-SPI
  board-response hook is pushed as an upstream-shaped candidate at
  `lane-a/gp-spi-device-hook` (`246c699`). Two independent 8,000-step
  JTAG lock-step sessions against upstream esp32sim passed: no PC
  divergence, zero timing resynchronizations, one persistent register
  difference at step 15
  ([receipt](lanes/receipts/E-01-jtag-lockstep.md)); no flash writes,
  board restored and released. Open BOARD work, in rough order: the
  first-line cache pooling diagnosis and adoption disposition; the six
  identities below strict two-receipt recovery; the logic-analyzer bus
  capture (about 40 MHz QSPI, TE, I2C, touch interrupt), which gates
  timing-accuracy claims about the panel (demo modeling proceeds
  firmware-contract-first per the BOARD brief, with the board attached
  locally); strict two-boot assembly of the long-window
  PSRAM cells; arbitration and cache store/writeback probes (blocked on
  reviewed probe code); CCOUNT lock-step comparison (blocked on CORE's
  measured mode).

## Persistent fixtures (ESP-IDF v6.1, TinyDraw `3db39856`)

- Panel-probe ELF SHA-256
  `143e9f5185d010a8b5344ee5ed2c82a99928dba6839a84d746219d9045de468f`
- Vector demo ELF SHA-256
  `1b0475db6ab30e1e6b6ee07ae77ae46b21c874cac64a736e5ba86604a68234ce`
- Gate-harness ELF SHA-256
  `4e121a3642a6f18766cfe96c2be6adc8a0017fba4afa82105d642168ea40e2c8`

Fixture ELFs are machine-local to the maintainer (tinydraw
`out/fixtures/`), a known cloud-lane limitation recorded in the roadmap.

## Waiting on

- The esp32sim author's reply to the maintainer's contact (gates SPEED
  and the upstream-versus-fork split for measured mode).
- Maintainer disposition on the fork-wide rustfmt rewrite (gates SHIP's
  CI exit criterion).
- A maintainer board session with the external logic analyzer for the
  bus capture (gates BOARD's timing-accuracy claims about the panel;
  demo modeling proceeds without it).
