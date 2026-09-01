# Current state, 2026-09-01

This document lists effective, working work only. Reverted experiments are
intentionally omitted.

## What works now

The normal TinyDraw V2 product runs on the physical Waveshare
ESP32-S3-Touch-AMOLED-1.8 V2 and in esp32sim's browser. In the emulator, the
real firmware reaches READY, accepts a paced seven-sample drag through the
modeled CST820, draws a long diagonal stroke through the modeled CO5300, and
commits it without touch, presentation, or crash failures. The exact same
TinyDraw source branch builds for hardware. There is no emulator-specific
TinyDraw firmware fork.

The esp32sim fork also has a strict Rust gate. Its seven workspace members
format, compile, lint, document, and pass debug and release tests without Rust
warnings. Release builds retain debug assertions and overflow checks.

## TinyDraw repository

Repository: `aliceisjustplaying/tinydraw`

Pull request: <https://github.com/aliceisjustplaying/tinydraw/pull/4>

Branch: `maintenance/idf61-probes`

Head: `2643aa7`

The pull request contains seven granular commits:

1. Update the ESP-IDF dependency lock to 6.1.
2. Add the ESP32-S3 core-timing calibration probe source.
3. Record the two-boot calibration results.
4. Add the reusable boot-time probe tool.
5. Replace the deprecated touch read API with the current ESP-IDF 6.1 API.
6. Apply the repository's clang-format policy to the four files that failed
   the full-tree gate.
7. Remove duplicate static linkage of `tinydraw_core`.

The first four commits publish the source and receipts that had previously
existed only in local fixture work. The final three make the branch pass the
current product build and quality gates. None adds emulator-only behavior.

The normal product passes 31 debug tests, 31 release tests, 13 sanitizer
tests, a clean ESP-IDF 6.1 build, physical flash and boot, and the emulator
stroke gate. The installed ESP-IDF emits five CMake warnings about its own
`esp_wifi` and `wpa_supplicant` private include cycle. TinyDraw compiler and
deprecation warnings are zero.

No further TinyDraw commit is currently expected. A later hardware evidence
request may add a narrowly scoped probe or capture tool, but normal emulator
work does not require modifying TinyDraw.

## esp32sim repository

Repository: `aliceisjustplaying/esp32sim`

`main` remains an upstream mirror. Effective fork work is split across these
branches:

- `maintenance/rust-safeguards` at `b138473`: Rust 1.98 pin, inherited strict
  workspace lints, rustfmt policy, release checks, the one-command
  `scripts/pre-commit.sh` gate, all lint remediation, and the NTP fraction
  precedence fix found during linting.
- `board/tinydraw-v2-maintained` at `b7c9b87`: the safeguard work plus generic
  synchronous GP-SPI/MISO, GP-SPI2 DMA delivery, board-driven GPIO input,
  CST820 and CO5300 models, Waveshare V2 wiring and browser support, and the
  TinyDraw one-command workflows.
- `lane-a/gp-spi-device-hook` at `246c699`: an older focused upstream-shaped
  GP-SPI candidate retained for later comparison.
- `core/measured-phase1`: effective unfinished CORE phase-1 work. It is not an
  accepted phase-1 exit and no new work is authorized during this cleanup
  round.
- `lane-b/design-spike`, `lane-g/ci-spec`, `lane-g/upstream-ci`, and
  `puck/base`: retained design, future release, and provenance branches.

The work that can be offered upstream is classified in
[`board-upstream-split-2026-09-01.md`](board-upstream-split-2026-09-01.md).
The generic parts are GP-SPI/MISO, GP-SPI2 DMA, board-driven GPIO input, and
reusable CST820 and CO5300 device models. Waveshare wiring is useful board
support. The launcher, product marker checks, and paced TinyDraw stroke are an
optional third-party integration example. No upstream pull request has been
opened because the maintainer wants to test and decide the grouping first.

BOARD's emulator implementation is done under the current documented gate:
the pinned normal product passed a paced multi-sample browser stroke and the
same source passed on physical hardware. BOARD's evidence service remains
open. In particular, panel timing accuracy is not done.

## Program-office repository

Repository: `aliceisjustplaying/esp32s3-cycle-accurate-wasm`

Branch: `main`

This repository contains no product implementation. It contains the roadmap,
binding decisions, lane briefs, status ledger, provenance, timing evidence,
review reports, and hardware or emulator receipts. The current additions are:

- a fresh CORE recovery brief that forbids TypeScript implementation and
  requires the real esp32sim backend path;
- the authorized 1,228-record, 30-callback observation bundle;
- the Rust safeguard report;
- the BOARD upstream split review;
- the normal TinyDraw V2 validation receipt;
- this current-state overview and the updated status ledger.

The observation bundle is real captured input but is not yet a complete
executable replay checkpoint. It lacks initial CPU, RAM, MMIO, and device
state. That missing boundary must be resolved before a new CORE agent can
build a legitimate end-to-end differential.

## Fake contract tests

The fake backend tests are useful unit tests for backend-neutral scheduler
rules such as stop precedence, quotas, deadlines, and ledger behavior. They
are fast and deterministic. They cannot prove the product path works because
they do not execute the esp32sim backend, real scheduler adapter, timing
importer, or canonical product ledger together. They remain supporting tests,
not the CORE phase-1 exit gate.

## TE cadence

The modeled 60 Hz GPIO13 TE cadence is approximate because it was chosen to
exercise the firmware's rising-edge contract without a hardware timing
receipt. It can be measured. The open BOARD evidence task is a hash-pinned
logic-analyzer capture of QSPI, GPIO13 TE, I2C, and GPIO21 touch interrupt,
with firmware hash, board revision, pin map, operating state, edge count, and
reported timing statistics. Until that exists, 60 Hz is compatibility
behavior, not an adopted hardware timing claim.

## One-command workflows

Keep the TinyDraw and maintained esp32sim checkouts anywhere and give the
script the TinyDraw path.

Run the normal TinyDraw product in the emulator and open the browser:

```text
./scripts/tinydraw-v2.sh run /path/to/tinydraw
```

Run the automated paced-stroke emulator gate:

```text
./scripts/tinydraw-v2.sh smoke /path/to/tinydraw
```

Build and flash the normal product to the connected board:

```text
./scripts/tinydraw-v2.sh flash /path/to/tinydraw /dev/cu.usbmodem101
```

Run every Rust safeguard in esp32sim:

```text
./scripts/pre-commit.sh
```

The script builds what it needs. `run` is interactive and remains open until
stopped. `smoke` exits with success or a concrete failure. `flash` leaves the
normal product on the board.

## Work remaining after cleanup

No new lane work is authorized until the maintainer explicitly says to start.
When work resumes, the sequence remains CORE phase 1 and then CORE phase 2
only after phase 1 genuinely exits. CORE phase 1 still needs a real measured
differential through `Esp32SimBackend`, the measured scheduler, the schema-2
timing importer, and the canonical product ledger.

Separately, the maintainer can test TinyDraw pull request 4 and decide whether
to merge it. The maintainer can then decide which esp32sim groups to offer
upstream. BOARD can later collect the logic-analyzer timing evidence and the
remaining silicon receipts; those evidence tasks do not reopen the completed
normal-product emulator gate.
