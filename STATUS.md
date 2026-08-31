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

- **Lane 0 (toolchain)**: the ESP-IDF 6.1 flag day is complete. All
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
- **Lane A (board devices)**: capture request
  [`A-01`](lanes/requests/A-01-v2-controller-and-identity.md) is
  specified and executable; its chip-identity portion is captured and
  accepted. Open: the logic-analyzer capture (about 40 MHz QSPI, TE,
  I2C, touch interrupt) is required before panel or touch modeling
  begins; the touch controller stays unnamed until it identifies itself
  (decision 0014). A synchronous GP-SPI board-response hook exists as an
  unmerged upstream-shaped candidate at esp32sim commit `246c699`.
- **Lane B (measured mode)**: the design spike is dispositioned as
  decision 0014; implementation has not started and is assigned to a
  blank-slate agent per
  [`lanes/COORDINATOR.md`](lanes/COORDINATOR.md). The schema-1
  `timing.json` remains rejected for measured totals (it loses the
  affine MMIO intercept, the committed evidence is `3n - 8`);
  timing-profile schema 2 is lane B's.
- **Lane C (contention)**: not started. The dual-core policy ADR may be
  drafted now; implementation waits for lane B's adapter and measured
  core.
- **Lane D (wasm JIT)**: waiting on upstream contact before work starts.
- **Lane E (silicon oracle)**: validated the A-01 identity bundle and
  completed two independent 8,000-step JTAG lock-step sessions against
  upstream esp32sim: no PC divergence, zero timing resynchronizations,
  one persistent register difference at step 15. Receipt:
  [`E-01-jtag-lockstep`](lanes/receipts/E-01-jtag-lockstep.md). No flash
  writes; the board was restored and released. Open: long-window PSRAM
  cells await strict two-boot assembly; arbitration and cache
  store/writeback captures are blocked on reviewed probe code; CCOUNT
  comparison is blocked on lane B's measured mode.
- **Lane F (integration and ship)**: blocked by lanes C and D.
- **Lane G (CI)**: the CI specification is built and verified on local
  esp32sim branches `lane-g/ci-spec` (commits `196727d`, `6ba6a6d`) and
  upstream-shaped `lane-g/upstream-ci` (`4762edc`, `3b58cc6`): pinned
  actions, mandatory fail-closed decoder corpora with visible case
  counts and hashes (10 Xtensa, 9 RISC-V, zero mismatches), and boundary
  defects proven to fail. Nothing is pushed yet. Blocker awaiting
  maintainer disposition: pre-existing whole-tree rustfmt debt at fork
  base `aa851249` (893 diff hunks across 39 files), which lane G does
  not rewrite unilaterally.
- **Lane H (boundary hardening)**: scope folded per the program-office
  extraction: review lane B's validated-output seam as it lands (decision
  0014 assigns the primary validator there), then harden the product's
  public web surface when lane F builds it.

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

- The esp32sim author's reply to the maintainer's contact (gates lane D
  and the upstream-versus-fork split for measured mode).
- Maintainer disposition on the fork-wide rustfmt rewrite (gates lane
  G's final exit criterion).
- A maintainer board session with the external logic analyzer for
  A-01's bus capture (gates lane A's panel and touch modeling).
