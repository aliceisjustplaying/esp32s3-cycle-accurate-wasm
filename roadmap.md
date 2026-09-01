# ESP32-S3 cycle-model roadmap

Date: 2026-08-31, revision 4.

The product is a browser-hosted cycle-accurate ESP32-S3 emulator built
from our [esp32sim](https://github.com/joakimeriksson/esp32sim) fork,
scoped to the complete SoC plus the exact Waveshare
ESP32-S3-Touch-AMOLED-1.8 board; the first useful milestone is real
TinyDraw firmware boot, draw, and touch in the browser. Governing
decisions:
[0013](decisions/0013-product-identity-fork-owns-the-product.md)
(product identity: the fork owns the product, puck is the donor,
evidence, and decision repository),
[0014](decisions/0014-measured-scheduler-and-adapter-contract.md) (the
measured scheduler and adapter contract),
[0011](decisions/0011-adopt-esp32sim-execution-foundation.md) (esp32sim
as the execution foundation), and
[0008](decisions/0008-tiered-cost-vocabulary-and-acceptance-bounds.md)
(cost tiers). Current state lives in [`STATUS.md`](STATUS.md).

## Definition of done

The product boots the board's real merged firmware image (real ELF plus
the real mask ROM) on an emulated dual-core ESP32-S3 with display, touch,
and IMU, at real time on M1-class hardware in a browser, with cycle
accounting that passes a silicon correlation suite at the decision-0008
tiers: exact on SRAM-resident kernels, within 1 percent on frame-scale
workloads, distribution agreement on RTC and long-window PSRAM paths.

## Architecture

- **Execution:** esp32sim's `xtensa-lx7` core and `esp32s3` SoC, pinned by
  commit. Fast mode is upstream's behavior, unchanged. **Measured mode**
  is this project's addition: calibrated cycle accounting through
  `advance_ccount`, per-block base costs in the block cache, cache and
  MSPI models, window-exception costs, and tighter dual-core interleaving
  quanta. Both modes share one core; fast mode's speed is never taxed by
  measured mode's bookkeeping. Observation is defined at the CPU backend
  level per decision 0012: the native JIT fast path bypasses the `Bus`
  trait (review F-031, `xtensa-lx7/src/block.rs:143,187`), so measured
  mode is interpreter-first and a cross-mode conformance program gates
  any JIT participation.
- **Calibration:** this repository's timing lab, receipts, and tier
  vocabulary are the only source of measured-mode costs. Unknown costs
  stay unknowns; totals stay blocked until their events are costed.
- **Verification:** upstream's JTAG lock-step harness for architectural
  agreement; this project's correlation suite for cycle-level agreement in
  measured mode; the interpreter-versus-JIT bit-identity rule for engine
  changes; pixel-level behavior checks built on donor concepts from the
  puck archive's differential harness.
- **UI:** the fork owns the thin web shell (decision 0013); donor pieces
  from the puck archive (page, recorder, freeze, and regression layers)
  port into it selectively, with provenance.
- **Upstream relationship:** contributions upstream where wanted (wasm
  JIT backend, boards, peripheral gaps), fork-carried where not (measured
  mode, if upstream prefers staying instruction-level).

## Lane plan at a glance

Per-lane implementation briefs, including each lane's home repository,
branch convention, reading list, constraints, and exit criteria, live in
[`lanes/`](lanes/README.md). The complete handoff to an implementing
agent is a checkout of this repository, one lane name, and that lane's
brief. On 2026-08-31 the maintainer collapsed the original ten lanes
(0, A through H) into four; the briefs carry the mapping, and older
documents' lane-letter references resolve through it.

| Lane | Scope | Cloud-viable | Blocked by |
| --- | --- | --- | --- |
| CORE | Measured execution per decisions 0014 and 0008: the versioned `backend-api` crate and contract tests, measured interpreter scheduler, timing-profile schema 2 importer and tiered ledger, measured cost payload, one-shot TypeScript differential gate; then phase 2, dual-core contention (interleave quanta as a decision record, MSPI arbitration, litmus firmware, contended-cohort correlation) | Fully | Nothing (phase 2 by phase 1) |
| BOARD | The exact Waveshare board and its silicon evidence: capture-first device models (CO5300-class panel, touch, sensors, TCA9554 wiring), chip-identity adoption, the JTAG lock-step oracle, probe families, and the toolchain-currency remainder (first-line diagnosis, weak identities) | Modeling yes, captures no | Captures gate panel and touch modeling |
| SPEED | wasm JIT backend, upstream-shaped: browser real time with `--no-jit` bit-identity, measured against the browser-speed receipts, decision 0010's profiling checkpoint | Correctness yes, M1 gates local | Upstream contact |
| SHIP | CI (built, awaiting the rustfmt disposition), boundary review of CORE's validator seam with hostile-input tests, then the thin web shell, correlation suite at 0008 bounds, and the release battery | Fully | Shell and release by CORE and SPEED | Nothing; unchanged by 0011 |

SHIP's CI and boundary workstreams grew from the accepted
external-review findings, see
[`RESPONSE.md`](reviews/2026-08-31-external/RESPONSE.md). The critical
path is CORE phase 1 then phase 2 for accuracy, and SPEED for browser
real time; the two paths are independent of each other. **Demo
milestone: lane BOARD alone boots the real board image with the panel
drawing in the browser at interpreter speed.** Progress is measured
against exit criteria, not time estimates.

The measured-mode design spike is complete: its draft was reviewed,
trimmed, and accepted as decision 0014, which is now the normative
contract (interpreter-only, single core, networking off, fail closed).
CORE is in the implementation phase against that record.

## Toolchain currency

The project tracks the latest stable ESP-IDF; the board and fixtures
build with v6.1. An IDF bump is a provenance event, not a chore: every
hardware receipt pins the IDF version, sdkconfig hash, and compiler,
because a new compiler changes codegen and can shift measured costs.
Bump rules (lane BOARD):

- rebuild the fixture ELFs under the new version and re-pin hashes,
  addresses, and the ISA inventory;
- rerun the receipt cohorts and confirm the silicon-architectural
  numbers (window pair, issue rate, loop alignment, cache ladders, MMIO
  costs) are bit-identical: they are chip claims and should not move,
  and any shift is a probe diagnostic, not a chip change;
- re-measure and re-pin everything that times IDF's own code (interrupt
  entry and resume through the dispatcher, boot-to-app_main), which is
  expected to change;
- one flag day, no mixing: each receipt is valid only for its pinned
  toolchain, and the cross-version delta is itself a
  toolchain-sensitivity receipt.

The current baseline ledger is
[`idf61-rebaseline-3db3985`](timing/evidence/idf61-rebaseline-3db3985/README.md);
open items are in [`STATUS.md`](STATUS.md). esp32sim itself is
version-agnostic about application firmware (it boots unmodified
images), so this section concerns this project's receipts and fixtures,
not the emulator.

## Hardware access and cloud lanes

Coding lanes may run on cloud agents with no board access. The board gates
verification loops, not development:

| Lane | Cloud-agent viable | What still needs the board |
| --- | --- | --- |
| CORE | Fully; costs come from committed receipts | Phase 2's contended-cohort correlation runs |
| BOARD | Modeling and analysis | Captures, probes, lock-step, the toolchain remainder |
| SPEED | Correctness | M1 performance gates (cloud numbers are directional) |
| SHIP | Fully | Final differential-harness runs before release |

The receipts pipeline is the interface: cloud lanes consume committed,
hash-pinned evidence from git and queue hardware needs in `STATUS.md`;
lane BOARD services the queue. Fixture ELFs become release artifacts or
committed extracts before any cloud dispatch that needs them, including
the esp32sim fork's test firmware. A second board serves lane BOARD
first.

## Standing rules

- Every adopted number keeps its receipt; refusals name their decision-0008
  tier candidate.
- The board is a one-owner-at-a-time resource.
- Fast mode is never slowed by measured mode; the two modes' outputs are
  differentially compared where they should agree (architectural state)
  and documented where they should not (time).
- Engine changes preserve upstream's interpreter-versus-JIT bit-identity
  rule; measured mode adds its own rule: same trace in, same cycle ledger
  out, deterministically.
- Upstream courtesy: fixes and capabilities upstream wants go upstream
  first; the fork carries only what upstream declines, in a clean patch
  stack with `PROVENANCE.md` from day one.
- Emulator networking defaults to none; live egress is opt-in and never
  available to the execution of third-party material (decision 0012).
- No browser TypeScript or other product caller imports esp32sim
  internals; all machine access crosses the fork-owned Rust adapter, and
  dependency lint enforces it (decisions 0013, 0014).
- TypeScript work is minimized: the fork's web shell is a thin transport
  and UI client; no TypeScript execution engine is ever built.
- Goldens carry semantic assertions and provenance sidecars; a
  conformance test whose corpus is missing fails, never skips.
- The correlation suite's first pass is scheduled; its residue is not.
