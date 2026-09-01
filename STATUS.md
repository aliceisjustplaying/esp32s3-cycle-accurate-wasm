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
`maintenance/rust-safeguards` at `b138473`. Rust 1.98.0 is pinned, all seven
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

Branch `maintenance/idf61-probes` at `2643aa7` contains the four requested
IDF 6.1 probe and receipt commits, the current ESP-IDF touch API fix, a
four-file formatting cleanup, and duplicate static-link removal. It passes 31
debug, 31 release, and 13 sanitizer tests. The clean normal-product build has
zero compiler or deprecation warnings. Physical hardware reaches READY and
LIVE_SETTLE without failures, and the emulator commits the seven-sample
stroke.

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

## Remaining ambiguities and parked questions

1. CORE executable boundary: “What exact instruction boundary and complete
   initial CPU, RAM, MMIO, and device state define the executable Flexe replay
   checkpoint for the real measured-path differential?”
2. TE evidence contract: “For the GPIO13 TE analyzer capture, what operating
   state, edge count, statistic, and acceptance bound should be adopted before
   replacing the approximate compatibility cadence with hardware timing?”
3. esp32sim upstream relationship: “After maintainer testing, which of the six
   reviewed groups should be offered upstream, and should reusable CST820 and
   CO5300 models be separated from Waveshare board wiring?”
4. TinyDraw pull request: “After maintainer testing, should TinyDraw pull
   request 4 be merged as the seven-commit series now validated on hardware
   and in the emulator?”
5. SDK-owned warnings: “Does the zero-warning requirement include patching or
   repinning the installed ESP-IDF SDK to suppress its five
   `esp_wifi`/`wpa_supplicant` CMake ownership warnings?”

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

- hash-pinned logic-analyzer capture of about 40 MHz QSPI, GPIO13 TE, I2C, and
  GPIO21 touch interrupt;
- first-line cache pooling diagnosis and six below-threshold receipt
  identities;
- long-window PSRAM, arbitration, and cache store or writeback probes after
  reviewed probe code exists;
- CCOUNT lock-step comparison after CORE measured mode exists.

The board currently runs the normal TinyDraw V2 product built from `2643aa7`.
The serial port is released.

## Overnight report, 2026-09-01, maintenance continuation

Advanced with commits:

- esp32sim Rust safeguards landed through `b138473`: ten granular commits from
  `c6463b5` through `b138473`, with the complete gate green.
- esp32sim BOARD maintenance landed through `b7c9b87`: generic device and DMA
  support, CST820, CO5300, Waveshare wiring, browser touch, one-command normal
  product workflows, a fail-closed paced seven-sample stroke gate, and the
  inherited Rust safeguards.
- TinyDraw pull request 4 advanced through `2643aa7`: four published probe
  commits plus `f5c239b`, `000dcdd`, and `2643aa7`. Host, sanitizer, clean
  product, hardware, and emulator checks pass.
- program-office commits `717db3f`, `70894c8`, `6771e6c`, `3469547`,
  `821aaf0`, and `6c27ed5` added the fresh CORE recovery brief, authorized
  observation input, safeguard report, upstream split, and corrected lane
  rules. Later granular cleanup commits archive the normal-product receipt,
  current-state overview, BOARD exit, and this ledger.

Parked, exact questions:

- CORE: “What exact instruction boundary and complete initial CPU, RAM, MMIO,
  and device state define the executable Flexe replay checkpoint for the real
  measured-path differential?”
- Upstream: “After maintainer testing, which of the six reviewed esp32sim
  groups should be offered upstream, and in what pull-request grouping?”
- SDK: “Does the zero-warning requirement include patching or repinning the
  installed ESP-IDF SDK to suppress its five SDK-owned CMake warnings?”

Morning decision order:

1. Test TinyDraw pull request 4 and decide whether its seven validated commits
   should merge.
2. Decide whether SDK-owned CMake warnings are inside this repository cleanup
   scope.
3. Give an explicit go before any new CORE or BOARD evidence work starts.
4. When CORE is authorized, define or approve the executable replay boundary.
5. After BOARD testing, choose the esp32sim upstream grouping.

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
