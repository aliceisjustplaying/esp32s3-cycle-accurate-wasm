# Status

Last updated 2026-09-01. Authoritative documents: [`roadmap.md`](roadmap.md)
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

Dispatch sequence set by the maintainer: BOARD's demo, then CORE phase
1, then CORE phase 2.

- **CORE**: phase 1 has not exited. Effective work on esp32sim branch
  `core/measured-phase1` includes the versioned adapter contract, fake
  contract suite, measured interpreter scheduler, schema-2 timing importer,
  receipt-complete ledgers, active-device deadlines, the esp32sim backend
  adapter, stop precedence, quota refusals, and measured block-cost cache
  through `e5dea08`. The maintainer-authorized observation bundle is committed
  at `8367594`. It records 1,228 trace observations and 30 ROM callbacks, but
  it does not contain executable initial CPU, RAM, MMIO, and device state.
  The missing exit gate is a differential that drives the recorded Flexe path
  through the actual `Esp32SimBackend`, measured scheduler, timing importer,
  and canonical product ledger. The first attempted differential did not do
  that and was removed. Recovery requires a fresh CORE agent after the Rust
  maintenance gate is green. Phase 2 remains undispatched.
- **SPEED**: not in the current sequence; dispatched when the
  maintainer adds it.
- **SHIP**: not dispatched. Boundary review starts when CORE's
  validator seam lands; shell and release wait for CORE and SPEED. CI
  belongs to the release workstream; its material waits untouched on
  fork branches `lane-g/ci-spec` (`6ba6a6d`) and `lane-g/upstream-ci`
  (`3b58cc6`). The fork tree is rustfmt-clean at `puck/base`
  (`3051793`).
- **BOARD**: the ESP-IDF 6.1 flag day is complete. All
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
  record [`A-01`](lanes/requests/A-01-v2-controller-and-identity.md)).
  The on-device identity probe read `0xA7=0xB7`, `0xA8=0x41`, and
  `0xA9=0x02` at I2C address `0x15`; the exact V2 board's controller is
  adopted as CST820, matching the vendor's V2 board identification
  ([receipt](lanes/receipts/board-touch-identity-2026-09-01/README.md)).
  The demo-first milestone is complete on esp32sim branch
  `board/tinydraw-v2-demo` at `2c0b320`: the IDF 6.1 gate harness boots at
  interpreter speed, the CO5300 panel visibly draws the TinyDraw UI, and a
  scripted browser drag crosses the CST820 model and commits a visible stroke
  with 22 touch events, one down, one up, and zero touch or presentation
  failures
  ([receipt](lanes/receipts/board-tinydraw-v2-demo-2026-09-01/README.md)).
  The modeled 60 Hz TE cadence is demo-only and not timing evidence; the
  external logic-analyzer capture remains required before timing-accuracy
  claims. The physical board was restored to the IDF 6.1 gate harness,
  verified booting, and released.
  A synchronous GP-SPI
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

Fixture ELFs remain machine-local to the maintainer. Their IDF 6.1 probe and
calibration source is published on TinyDraw branch
`codex/lane-0-idf61-probes` at `632c966`; the normal TinyDraw 2.2 product build
has not yet been validated in the emulator.

## Maintenance pause, 2026-09-01

No lane work is authorized until the fork's Rust quality gate passes without
warnings. The fork work is on esp32sim branch `maintenance/rust-safeguards`,
starting at `c6463b5`, with Rust 1.98.0 pinned, workspace lints inherited by
all seven crates, debug assertions and overflow checks enabled in release,
and `scripts/pre-commit.sh` as the required local gate. This is fork policy;
upstream suitability is not a constraint for this round.

The remaining maintenance work is: finish all lint remediation; pass the gate
from the repository root and by absolute path from another directory; add
one-command normal TinyDraw build/run and emulator quickstart scripts; validate
the normal TinyDraw 2.2 build in the emulator and on hardware; prepare a clean
TinyDraw pull request containing the four published IDF 6.1 probe commits if
that validation passes; then remove only branches proven merged or superseded.

## Current document ambiguities to resolve

1. "Replay bundle" was used for an observation log that cannot initialize the
   emulator. CORE needs a precise executable checkpoint contract and a named
   trace start before another agent can implement the real differential.
2. The fake contract suite is a deterministic test of backend-neutral scheduler
   rules. The documents did not clearly say that it cannot satisfy phase 1;
   phase 1 requires the same path through the real esp32sim backend.
3. BOARD has a demo milestone and a continuing evidence-service role. The
   documents do not define whether "BOARD done" means the browser demo, normal
   TinyDraw 2.2 manual validation, or the later logic-analyzer timing receipts.
4. The 60 Hz TE signal is a demo cadence derived from the emulator clock, not a
   hardware receipt. A logic-analyzer capture must define the pin, sample count,
   operating state, and acceptance statistic before hardware cadence is adopted.
5. "Unmodified TinyDraw V2" needs one pinned product revision and a decision on
   whether the four IDF 6.1 probe and calibration commits belong in that product
   revision. Hardware and emulator validation will supply the decision evidence.
6. The safeguard specification names `clippy::transmute_int_to_ptr`, which does
   not exist in pinned Rust 1.98.0. The fork uses the rustc lint
   `integer_to_ptr_transmutes` for the same defect class.
7. The earlier overnight report asked how to group BOARD work for upstream.
   The maintainer has deferred that decision until the generic and TinyDraw-only
   pieces are separated and tested.

## Hardware queue

Tasks needing the physical board or the maintainer's hands, owned by
lane BOARD, serviced in maintainer-scheduled sessions:

- Bus capture with the external logic analyzer (about 40 MHz QSPI, TE,
  I2C, touch interrupt). Needed before timing-accuracy claims about the
  panel; demo modeling proceeds without it.

## Overnight report, 2026-09-01

Advanced:

- BOARD identified the exact V2 touch controller as CST820 and archived the
  receipt in program-office commits `d3d7cbe` and `c64351e`. TinyDraw probe
  commit `4db22a6` and esp32sim demo commits `4886ec0`, `e128b82`, `fe8f720`,
  and `2c0b320` produced the visible browser UI and a scripted visible touch
  stroke. The IDF 6.1 gate harness was restored and the board released.
- CORE phase 1 advanced on esp32sim branch `core/measured-phase1` through
  `88f526d`, `bab791e`, `8dcf27e`, `b2aed7f`, `a96a4e0`, `21edb94`,
  `8d7bc78`, `cdfc693`, `04f6813`, `49fb685`, `a4340d5`, `fe7cd05`, and
  `e5dea08`. Coordinator review reran `cargo test --workspace`: 58 tests and
  all doc tests passed. No baseline was generated or changed.

Parked, exact questions:

- CORE: “May CORE generate and commit the canonical shared replay bundle
  containing the 1,228 raw trace records with SHA-256
  `e025823c09a8c5558dbd2147bde05e8396c6d0f4b6103ca77947d11b4bc27d2d`
  and 30 ordered ROM callback events for gate ELF SHA-256
  `4e121a3642a6f18766cfe96c2be6adc8a0017fba4afa82105d642168ea40e2c8`, or
  will the maintainer provide it?”
- Upstream relationship: “Should esp32sim branch
  `board/tinydraw-v2-demo` at `2c0b320` be offered to
  joakimeriksson/esp32sim as a PR, and if so in what commit grouping?”

Morning decisions, in priority order:

1. Answer CORE's replay-bundle question. It gates the phase-1 exit and all
   phase-2 dispatch.
2. Decide whether and how to offer the BOARD demo branch upstream.

Confusing or contradictory documents:

- CORE phase 1 requires the shared-trace differential, but only summary
  counts and hashes are committed; the donor test also names paths removed
  during the product split.
- The coordinator brief generally says to advance after a lane parks, while
  its phase-2 rule says phase 2 starts only after phase 1 exits. The specific
  phase-2 rule was followed, so phase 2 was not dispatched.
- BOARD's exit text names `TINYDRAW_LIVE_FAIL presenter=1 touch=1` as the
  milestone even though a passing run emits `TINYDRAW_LIVE_STROKE` and
  `TINYDRAW_LIVE_DONE` and correctly emits no `TINYDRAW_LIVE_FAIL` marker.
