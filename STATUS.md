# Status

Last updated 2026-09-01. Authoritative documents are
[`roadmap.md`](roadmap.md), decisions 0006 through 0014, and the current lane
briefs. Decisions 0013 and 0014 govern product identity and the measured
scheduler contract. Receipts live in `timing/`, `reviews/`, `experiments/`,
and `lanes/receipts/`.

## Authority and pause

The repository is in a cleanup and organization round. The maintainer has
explicitly said not to start new work without a new go. No fresh CORE agent,
CORE phase 2, SPEED, SHIP, new evidence run, or upstream pull request is
authorized during this pause.

## Maintenance result

The fork's Rust safeguard gate is complete on esp32sim branch
`maintenance/rust-safeguards` at `b138473`. Rust 1.98.0, the latest stable
compiler released on 2026-08-20, is pinned. The existing Rust 2021 language
edition is retained as required by the safeguard specification. All seven
workspace members inherit the strict lint policy, and release builds retain
debug assertions and overflow checks. `scripts/pre-commit.sh` passed from the
repository root and by absolute path from the program-office checkout. There
are zero compiler, Clippy, rustdoc, unfulfilled-expectation, or undocumented
lint-exception diagnostics. Debug and release tests pass. The report is
[`reviews/rust-safeguards-2026-09-01.md`](reviews/rust-safeguards-2026-09-01.md).

The cleanup fixed one functional defect found by linting: NTP fractional
seconds now use `(nanoseconds << 32) / 1_000_000_000`. Other changes preserve
intentional wrapping, raw pointers, JIT contracts, hardware tables, and ABI
behavior behind documented scoped expectations.

## Lane state

- **BOARD emulator implementation**: done on esp32sim branch
  `board/tinydraw-v2-maintained` at `b7c9b87`. Generic synchronous GP-SPI and
  MISO, GP-SPI2 DMA delivery, board-driven GPIO input, CST820, CO5300,
  Waveshare V2 wiring, browser touch, and one-command TinyDraw workflows are
  implemented. TinyDraw `2643aa7` passed a paced seven-sample browser stroke,
  and the same normal product source passed on physical hardware. See the
  [normal-product receipt](lanes/receipts/board-tinydraw-v2-normal-2026-09-01/README.md).
  The earlier one-dot screenshot is not acceptance evidence.
- **BOARD evidence service**: open but paused. Chip identity is accepted as
  CST820. IDF 6.1 timing rebaseline, JTAG lock-step, and earlier receipts
  remain valid at their recorded pins. Panel timing claims still require a
  logic-analyzer capture. The modeled 60 Hz GPIO13 TE pulse remains an
  approximate compatibility signal, not adopted hardware timing.
- **CORE phase 1**: not exited and paused. Effective work on
  `core/measured-phase1` includes the backend contract, supporting fake tests,
  measured interpreter scheduler, schema-2 timing importer, receipt-complete
  ledgers, active-device deadlines, real esp32sim backend adapter, stop
  precedence, quota refusals, and block-cost cache through `e5dea08`. The
  authorized observation input at `8367594` contains 1,228 trace records and
  30 ordered ROM callbacks. Its program-office copy has SHA-256
  `b53ce35fcf88e5da1a995bfdb76543993334ec2fc8cc7a8a21b63e5061f143ad`.
  It lacks executable initial CPU, RAM, MMIO, and device state. The remaining
  phase-1 gate must drive the actual `Esp32SimBackend`, measured scheduler,
  schema-2 importer, and canonical product ledger. A fresh agent must use
  [`lanes/CORE-RECOVERY.md`](lanes/CORE-RECOVERY.md) when the maintainer gives
  a new go. No TypeScript implementation or TypeScript timing-rule port is
  permitted.
- **CORE phase 2**: undispatched and blocked on a genuine phase-1 exit.
- **SPEED** and **SHIP**: undispatched. Their retained branches are not active
  work.

## TinyDraw pull request

TinyDraw pull request 4 is open:
<https://github.com/aliceisjustplaying/tinydraw/pull/4>.

Branch `maintenance/idf61-probes` at `0835e5b` contains the four requested
IDF 6.1 probe and receipt commits, the current ESP-IDF touch API fix, a
four-file formatting cleanup, duplicate static-link removal, boot-probe
correctness fixes, and corrected calibration contracts. All 16 inline review
threads were audited: 11 unique valid findings were fixed, five were duplicate
reports, and the unrelated generic docstring quota was rejected. It passes 31
debug, 31 release, and 13 sanitizer tests. The clean normal-product and
calibration builds have zero project warnings. Hardware reaches READY after
normal-product restore, the calibration reaches `CALIBRATION_DONE`, and the
emulator commits the seven-sample stroke. Review evidence is in
[`lanes/receipts/tinydraw-pr4-review-2026-09-01/`](lanes/receipts/tinydraw-pr4-review-2026-09-01/README.md).

The installed ESP-IDF 6.1 SDK emits five CMake component-ownership warnings
about its own cyclic private include relationship between `esp_wifi` and
`wpa_supplicant`. These are not TinyDraw source warnings. Removing them would
require expanding scope into the installed SDK or selecting a different SDK
revision.

No further TinyDraw commit is expected for normal emulator operation. A later
BOARD hardware-evidence request may add a narrowly scoped probe or capture
tool.

## One-command operation

From esp32sim branch `board/tinydraw-v2-maintained`:

```text
./scripts/tinydraw-v2.sh run /path/to/tinydraw
./scripts/tinydraw-v2.sh smoke /path/to/tinydraw
./scripts/tinydraw-v2.sh flash /path/to/tinydraw /dev/cu.usbmodem101
```

`run` builds and opens the normal product in the browser. `smoke` performs the
paced stroke and fails closed on missing markers or crashes. `flash` builds
and flashes the normal product. `./scripts/pre-commit.sh` runs the complete
Rust safeguard gate. The plain-English repository breakdown is
[`reviews/current-state-2026-09-01.md`](reviews/current-state-2026-09-01.md).

## Upstream classification

No esp32sim upstream pull request has been opened. The tested work is split in
[`reviews/board-upstream-split-2026-09-01.md`](reviews/board-upstream-split-2026-09-01.md):

1. Generic synchronous GP-SPI and MISO.
2. Generic GP-SPI2 DMA delivery.
3. Generic board-driven GPIO input.
4. Reusable CST820 and CO5300 models.
5. Waveshare AMOLED 1.8 V2 wiring, CLI, browser support, and board docs.
6. Optional TinyDraw launcher and paced-stroke integration example.

The maintainer will test first and then decide which groups to offer upstream.

## Branch cleanup

Deleted after merge or supersession proof:

- esp32sim remote `board/tinydraw-v2-demo`, superseded by
  `board/tinydraw-v2-maintained`;
- esp32sim remote `maintenance/lints-*` branches after their commits were
  integrated into `maintenance/rust-safeguards`;
- corresponding local lint and old BOARD pointers after `git cherry` or
  range-diff verification;
- program-office remote receipt branches after their receipts landed on
  `main`;
- program-office remote `core/phase1-differential` after the only effective
  artifact, the hash-identical shared observation input, landed on `main`.

Retained deliberately:

- esp32sim `maintenance/rust-safeguards` and
  `board/tinydraw-v2-maintained`, which are the reviewed working heads;
- esp32sim `core/measured-phase1`, the source of effective unfinished CORE
  work for a future fresh recovery;
- esp32sim `lane-a/gp-spi-device-hook`, `lane-b/design-spike`,
  `lane-g/ci-spec`, `lane-g/upstream-ci`, and `puck/base`, which still anchor
  candidate, design, release, or provenance material;
- TinyDraw `codex/lane-0-idf61-probes` and `board/touch-identity-probe`, which
  anchor published fixture and hardware-identity evidence;
- TinyDraw `maintenance/idf61-probes`, the open pull-request branch.

## Persistent historical fixtures

The IDF 6.1 timing fixtures remain pinned to TinyDraw `3db39856`:

- panel-probe ELF SHA-256
  `143e9f5185d010a8b5344ee5ed2c82a99928dba6839a84d746219d9045de468f`;
- vector demo ELF SHA-256
  `1b0475db6ab30e1e6b6ee07ae77ae46b21c874cac64a736e5ba86604a68234ce`;
- gate-harness ELF SHA-256
  `4e121a3642a6f18766cfe96c2be6adc8a0017fba4afa82105d642168ea40e2c8`.

Their source is published on TinyDraw branch `codex/lane-0-idf61-probes` at
`632c966`. These fixtures are historical evidence inputs. Normal-product
validation now uses TinyDraw `2643aa7` as recorded above.

## Resolved direction and active decisions

No maintainer ambiguity currently gates the proposed hardware batch.

- Flexe is a legacy experimental Xtensa interpreter from the frozen puck-era
  feasibility work. It is not the current product. CORE recovery should boot
  the current esp32sim machine deterministically from reset and select a named
  reachable comparison boundary. Choosing that boundary and reconstructing
  its state is an engineering task, not a maintainer question. A future agent
  parks only if it can name exact state that cannot be derived.
- The TE electrical capture contract already exists in request A-01: cold
  reset through one known frame, at least 120 rising edges, the ten named
  signals, precise analyzer settings, raw exports, and correlated firmware
  markers. No new acceptance bound is being adopted before capture.
- The esp32sim upstream decision is deliberately deferred until most remaining
  work is complete. The intent is to offer broadly, with final grouping chosen
  after more implementation and maintainer testing.
- TinyDraw pull request 4 review-thread remediation is complete. Automated
  re-review is pending before the maintainer tests or merges it.
- The five SDK-owned CMake warnings are explicitly out of scope.

Resolved document ambiguities:

- the 1,228-record artifact is called an observation input, not a complete
  replay checkpoint;
- fake backend tests are supporting unit tests and cannot satisfy CORE phase
  1;
- BOARD emulator implementation is done at the normal-product gate, while its
  evidence service remains open;
- “unmodified TinyDraw” means no emulator-specific source path, with the same
  normal product source validated on hardware and emulator;
- Rust 1.98 uses rustc `integer_to_ptr_transmutes` because the requested
  `clippy::transmute_int_to_ptr` lint name does not exist.

## Hardware queue

Paused pending a new maintainer go:

- immediately available IDF 6.1 receipt-gap, core-timing, boot-time, and TE
  diagnostic cohorts;
- offline PSRAM long-window assembly before deciding whether to repeat it;
- later reviewed arbitration, external-store, dirty-writeback,
  instruction-PSRAM, and first-line pooling probes;
- later logic-analyzer capture of about 40 MHz QSPI, GPIO13 TE, I2C, and
  GPIO21 touch interrupt when an analyzer and physical landmarks are
  available;
- CCOUNT lock-step comparison after CORE measured mode exists.

The complete proposed order and its current blockers are in
[`reviews/hardware-measurement-inventory-2026-09-01.md`](reviews/hardware-measurement-inventory-2026-09-01.md).

The board currently runs the normal TinyDraw V2 product built from `0835e5b`.
The serial port is released.

## Overnight report, 2026-09-01, maintenance continuation

Advanced with commits:

- esp32sim Rust safeguards landed through `b138473`: ten granular commits from
  `c6463b5` through `b138473`, with the complete gate green.
- esp32sim BOARD maintenance landed through `b7c9b87`: generic device and DMA
  support, CST820, CO5300, Waveshare wiring, browser touch, one-command normal
  product workflows, a fail-closed paced seven-sample stroke gate, and the
  inherited Rust safeguards.
- TinyDraw pull request 4 advanced through `0835e5b`: four published probe
  commits plus `f5c239b`, `000dcdd`, `2643aa7`, `76ccfb9`, and `0835e5b`.
  Host, sanitizer, clean product, calibration, hardware, and emulator checks
  pass.
- program-office commits `717db3f`, `70894c8`, `6771e6c`, `3469547`,
  `821aaf0`, and `6c27ed5` added the fresh CORE recovery brief, authorized
  observation input, safeguard report, upstream split, and corrected lane
  rules. Later granular cleanup commits archive the normal-product receipt,
  current-state overview, BOARD exit, and this ledger.

Maintainer decisions received:

- SDK-owned CMake warnings are out of scope.
- esp32sim upstream pull requests are deferred until most remaining work is
  complete; the intent is to upstream broadly after more testing.
- hardware measurements should be front-loaded before longer agent work, after
  the maintainer reviews the proposed inventory.
- no fresh CORE work starts until an explicit go.

Confusing or contradictory documents found and corrected:

- the old status called the authorized observation input a replay bundle even
  though it cannot initialize the emulator;
- the BOARD brief described only the gate-harness demo and did not define the
  later normal-product completion gate;
- the old status simultaneously described maintenance as incomplete after the
  final safeguard head was already green;
- the demo receipt documented a passing marker mismatch that has since been
  corrected in the BOARD brief;
- “unmodified TinyDraw V2” was not explicit about allowing ordinary upstream
  maintenance changes while forbidding an emulator-specific product fork.
