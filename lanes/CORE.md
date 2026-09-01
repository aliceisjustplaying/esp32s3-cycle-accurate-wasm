# Lane CORE: measured execution

Home: esp32sim fork, `core/*` branches off `puck/base`, one agent, own
clone. Decision 0014 is the normative contract.

Binding: decisions 0014 (the accepted contract, including its cut list)
and 0008 (cost tiers). Background, on demand: 0013, 0012, the roadmap,
and the timing lab (`../timing/README.md`); note 0014 rejects schema-1
`timing.json` for measured totals (it loses the affine MMIO intercept,
the committed evidence is `3n - 8`) and assigns timing-profile schema 2
to this lane.

Hard constraints:

- Observation is defined at the CPU backend level. A `Bus`-trait wrapper
  alone is insufficient: the native JIT fast path bypasses it
  (`xtensa-lx7/src/block.rs:143,187`). Measured mode is
  interpreter-first; JIT participates only after the cross-mode
  conformance program of decision 0012 proves observation equivalence.
- Fast mode is never taxed: upstream behavior with measured mode off is
  bit-identical.
- Costs come from receipt-pinned manifests with decision-0008 tiers;
  unknown costs stay unknown and block totals; no invented cycles. The
  toolchain-sensitive first-line cache class stays blocked until lane
  BOARD's diagnosis disposition lands.
- Networking off; single core (core 0) in phase 1; decision 0014's cut
  list is binding. Identity artifact formats belong to lane BOARD.

## Phase 1: adapter and measured interpreter

Scope: the `backend-api` crate
(create/load/reset/run-to-deadline/inject/drain/inspect/capabilities/
close), a fake backend and the esp32sim backend passing the same
contract tests including the slice-invariance property test (partitioned
run versus whole run, byte for byte), the measured interpreter scheduler
per 0014 (deferred completion-phase access, pending instructions across
calls, typed device deadlines, budgets and stop precedence, epochs,
wrapping CCOUNT projection), timing-profile schema 2 and its importer,
then the measured cost payload: per-block base costs, cache and
line-fill models, the window-exception pair (35 cycles per spilled frame
past depth 6), the loop-alignment rule (+1 cycle at +3 mod 4), dependent
load-use, and MMIO classes with the affine intercept preserved.

Differential: the one-shot gate of 0014. Replay shared traces through
the existing TypeScript timing machine (`../timing/`) and through
measured mode once, compare ledgers, archive the receipt here, then
retire the TypeScript machine as donor history.

Recovery gate: the frozen 1,228-record observation bundle is hash-pinned, but
it is not executable backend input. It does not contain the initial Xtensa
register windows, PS, SAR, CCOUNT, RAM, MMIO, and device state required to
force `Esp32SimBackend` through those boundaries. A recovery agent starts from
the last reviewed measured implementation plus the unchanged bundle. Its first
milestone is a minimal proof that the real adapter can initialize and execute
one recorded boundary through `Esp32SimBackend`, `Esp32TimingSource`, the
schema-2 importer, and the product ledger. No independent replay classifier,
hard-coded donor costs, checkpoint format, or receipt work precedes that proof.
If existing artifacts cannot produce it, the lane parks with the exact missing
artifact or interface decision.

Phase 1 exit: adapter contract tests green on both backends,
slice-invariance included; measured mode reproduces the flexe boot
replay's event accounting on shared traces; the one-shot differential
receipt is archived; determinism holds (same trace in, same cycle ledger
out).

## Phase 2: dual-core contention (starts after phase 1 exits)

Scope: dual-core interleave quanta and tie-break rules, proposed as a
decision record (with deterministic trace events for core step,
interrupt assert and accept, device deadline) before the capability is
enabled; MSPI arbitration between cores and DMA; interrupt delivery
anchored to the measured dispatcher costs (ESP-IDF 6.1 values: level-1
entry/resume 227/143, level-3 222/139); race-sensitive litmus firmware;
correlation against the contended receipt cohorts in
`../timing/evidence/` (the `*_core1_contended` cells). New capture needs
go to lane BOARD through STATUS's hardware queue.

No arbitration guesses adopted without receipts: interval or
distribution tiers exist for policies that cannot be identified exactly.
JIT stays out of scope for both phases.

Phase 2 exit: contended-cohort correlation within decision-0008 bounds;
the scheduler decision record accepted; the litmus suite green and
deterministic.
