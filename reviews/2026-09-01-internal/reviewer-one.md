# Combined thermonuclear code review and adversarial architecture review

**Review snapshot:** 2026-09-01
**Reviewer:** Grok 4.6, internal pass labeled reviewer-one
**Workspace:** `esp32s3-cycle-accurate-wasm` on `main` (HEAD at review time: `96e4bb8`)
**Product fork inspected:** `/Users/sarah/src/a/esp32sim` on `puck/base` at `3051793` (rustfmt; pin history `2114ffc` upstream, then provenance, then program-office pointer, then rustfmt)
**Related local trees read, not modified:** `/Users/sarah/src/a/puck`, `/Users/sarah/src/a/tinydraw`, `/Users/sarah/src/a/esp32s3-lane-b/esp32sim` (design-spike leftovers), `/Users/sarah/src/a/esp32s3-lane-e` (E-01 raw path cited)
**Prior review this follows:** [`../2026-08-31-external/puck-esp32s3-adversarial-review-2026-08-31.md`](../2026-08-31-external/puck-esp32s3-adversarial-review-2026-08-31.md) and [`../2026-08-31-external/RESPONSE.md`](../2026-08-31-external/RESPONSE.md)
**Sibling slices in this directory:** [`timing-lab-review.md`](timing-lab-review.md), [`fork-rust-review.md`](fork-rust-review.md). This file is the combined pass: code quality of this repo's executable surface plus architecture of the four-repo program.

**Finding distribution:** 8 P0, 22 P1, 27 P2, 11 P3.

## Executive decision

The program office is unusually honest about evidence and unusually dishonest about the product sentence. The TypeScript timing lab is a careful fail-closed scheduler sitting on a schema that decision 0014 already rejected. The product fork is still upstream plus rustfmt. Measured mode does not exist.

Proceed with CORE, but not under the current kickoff, not against schema 1 and not by wrapping `Bus` or `Machine::run`.

The direction is still the right one: the fork owns the product, this repo owns decisions and receipts, measured mode is interpreter-first, fast mode is never taxed, unknown costs block totals. That is a real architecture. What is on the box ("browser-hosted cycle-accurate emulator") is a conjunction of two modes that the dispatch sequence will not both produce. What is in the donor lab cannot currently prove the second mode.

This is a follow-up to the 2026-08-31 external review, not a replacement. That review's P0s were accepted on paper. In code they are still open. RESPONSE.md's "compressed Gate 0 is completed" is true as paperwork.

Do not start CORE until: the kickoff names the fork and `puck/base`; schema 2 is treated as 0014-authorized implementation (not a new decision) or the freeze is lifted by a toy adapter that executes one instruction with a hand-written ledger; the donor lab paths are retargeted or the gate is rescoped to receipts rather than TypeScript totals; 0011's Bus sentence is treated as superseded (it is); measured mode is a new `run_until`, not a wrapper. Anything else is crates that cannot merge without lying about costs.

### Current-state scorecard

| Area | Assessment | Note |
| --- | --- | --- |
| Evidence culture | Strong, with self-violations | Parser, most adoption tests and IDF 6.1 `SHA256SUMS` are fail-closed. STATUS silicon headlines and schema-1 scalars are not. |
| Donor timing lab | Intact scheduler, broken home | 164 tests pass. Six fail because extraction left puck sibling paths. Schema 1 cannot hold `3n - 8`. |
| Product fork | Upstream plus provenance plus rustfmt | No `backend-api`, no measured mode, no observation seam. JIT still bypasses `Bus`. |
| Architecture on paper | Coherent two-mode design | 0012/0013/0014 are the right shape. 0010 and 0011 still contain superseded claims. |
| Product sentence / DoD | Conjunction of two modes | Real time needs SPEED. Cycle accounting needs CORE. SPEED is not in the dispatch sequence. |
| Proof surface | Document, not CI | This repo has no `package.json` and no `.github`. Fork `puck/base` CI is Pages only. |
| Release / public surface | Not ready | Wasm C ABI is unversioned. Pages fetches mask ROM from `releases/latest`. SHIP is not dispatched. |

## Scope, method and limits

Binding documents read: `AGENTS.md`, `STATUS.md`, `README.md`, `roadmap.md`, `PROVENANCE.md`, decisions 0008 and 0010 through 0014 (0006, 0007, 0009 skimmed), all of `lanes/`, `timing/README.md`, experiment READMEs, `reviews/2026-08-31-external/RESPONSE.md`.

Executable surface reviewed: `timing/` (scheduler, cache, MMU, address map, calibration, consumer, runtime replay, adoption tests), `experiments/` (flexe-wasm, browser-speed, qemu-oracle, esp32sim-adoption), the fork at `puck/base` (JIT fast path, `Machine::run`, wasm ABI, CI, decoder tests, networking defaults, board/boot defaults).

Method: source inspection, parallel specialist passes over the timing machine, calibration/evidence, experiments, architecture/decisions, fork `puck/base` and docs consistency, plus the commands below. Claims were checked against committed files. Architecture attacks were prosecuted as real defect, documented accepted risk or false alarm.

Limits:

- No physical board access.
- IDF 6.1 `boot-*-recovered.tar.gz` archives were not exploded or re-parsed. `SHA256SUMS` was verified.
- Flexe, browser-speed and live QEMU were not executed. They cannot import in this tree.
- `cargo test` on the fork was not run in this pass. The sibling fork review in this directory did run it: 13 tests pass, both objdump diffs skip when `XTENSA_DIS_FILES` is unset.
- Window 35 and loop +1 were not traced to a named kernel. Under this project's own rule they are not claims yet.

## Verification snapshot

| Check | Result |
| --- | --- |
| `bun test` in `timing/` (bun, this pass) | 164 pass, 6 fail, 6113 `expect()` calls, 170 tests across 23 files, about 0.9 s |
| Failing tests | `consumer.test.ts` "parses the checked-in timing profile"; `rtc-boot-read-exclusion.test.ts`; three `runtime-report.test.ts` cases; `xtos-intlevel-adoption.test.ts` profile parse. All `ENOENT` on repo-root `timing.json`. |
| `shasum -a 256 -c timing/evidence/idf61-rebaseline-3db3985/SHA256SUMS` | All OK (README, `toolchain-delta.json`, six capture gzips, four receipt tarballs) |
| D-cache analysis SHA-256 | `84dc4150f56e66d5b6f971e48cf6cf4255833452219517c3106d7bb0b3d15b89`, matches the adoption JSON |
| I-cache analysis SHA-256 | `258155e0f9c561e7da9da31e38f13787df79cbe1b98f94b2cd19145be7307180`, matches the adoption JSON |
| Affine MMIO adoption | `timing/evidence/esp32s3-rev02-tinydraw-e8a9f0e-mmio-write-adoption.json` slope 3, intercept -8 |
| Burst OLS vs adoption JSON | I-cache `15686/59 ≈ 265.864` → 266; D-cache flash `33576/71 ≈ 472.901` → 473; PSRAM `12063/71 ≈ 169.901` → 170. No math error found. |
| Root `package.json` / `.github` / `tsconfig.json` | Absent |
| Fork `backend-api` / measured mode | Absent on `puck/base` |

The six test failures are one extraction defect, not scheduler-logic failures. Copying `timing/timing.json` to the repo root would make those tests pass while still asserting puck-era evidence path strings. That would hide the retargeting bug, not fix it.

# Part 1. Thermonuclear code review

Findings first. Not capped.

## P0

### 1. The donor lab cannot load its own profile in this tree

After extraction, the live file is `timing/timing.json`. Six tests and the replay CLI still open the puck sibling `../timing.json` (repo-root `timing.json`, which does not exist):

- `timing/consumer.test.ts:253`
- `timing/xtos-intlevel-adoption.test.ts:13,105`
- `timing/rtc-boot-read-exclusion.test.ts:8`
- `timing/runtime-report.ts:251`
- `timing/report.ts:68`

Confirmed by running the suite: those six fail with `ENOENT` on `/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/timing.json`. `timing/runtime-report.ts:21` still sets `PROFILE_SOURCE = "packs/esp32-s3-touch-amoled-18/timing.json"`. `timing/report.ts` still prints a puck pack usage string and (per the sibling timing-lab review) still imports `../../../src/wasm`, which does not exist here, so the shadow-ledger CLI is dead in this checkout.

Decision 0014's one-shot gate and CORE's "replay shared traces through `../timing/`" exit criterion are not executable here.

### 2. Schema 1 stores MMIO writes as scalar 3. The committed evidence is `3n - 8`

`timing/evidence/esp32s3-rev02-tinydraw-e8a9f0e-mmio-write-adoption.json:99-100` has `affineSlopeCyclesPerAccess: 3` and `affineInterceptCycles: -8` (deltas 6136 at n=2048, 12280 at n=4096). `mmio-write-slope-adoption.test.ts:128-147` recomputes that intercept, then still expects `adoptedAccesses[].cycles: 3`.

`timing/timing.json:100-144` and `parseTimingProfile` (`timing/consumer.ts:713-748`) only accept a positive integer `cycles`. `EventLatency` / `CacheLatency` are one integer or unknown (`timing/execution.ts:12-23`, `timing/cache.ts:24-35`). n matched writes in this machine cost `3n`, never `3n-8`. The disagreement with any Rust affine ledger is exactly 8 cycles, plus whatever else is wrong.

Decision 0014 already rejected schema 1 for that reason. It is still the only machine-readable profile and the only executable cost model. Flexe full-ELF replay looks up those scalars (`experiments/esp32s3-flexe-wasm/full-elf-timing-replay-test.ts:275-278,425-437`).

IDF 6.1 `toolchain-delta.json:44` reports `mmioWriteSlopeCyclesPerAccess: 2.998046875` (`12280/4096`), which is the flattened one-point slope, not the two-point affine. The silicon-architectural headline file STATUS cites therefore publishes the rejected model shape.

### 3. First-line cache fills are parsed as `calibrated` after BOARD and 0014 blocked them

`timing/consumer.ts:627` requires `cacheLineFillCycles.status === "calibrated"`. `timing/timing.json:52-71` still carries v6.0.2 first-line 204 / 115 / 82. IDF 6.1 measured 203 / 114 / 81 and set `adopted: false` (`timing/evidence/idf61-rebaseline-3db3985/toolchain-delta.json:74-84`). Subsequent-line 266 / 473 / 170 are unchanged and are the usable class. The profile does not distinguish them.

A gate that feeds those first-line numbers into `CacheConfiguration.costs.lineFill` is replaying a blocked cost class and will not match IDF 6.1 silicon. `runtime-report.ts` does not use those numbers (SRAM-only, fills unknown). Flexe replay does.

### 4. Evidence paths in the live profile 404 in this repo and the parser does not care

Every `timing/timing.json` evidence string is `packs/esp32-s3-touch-amoled-18/timing/evidence/...` (lines 16, 30, 54, 75, 82, 161). `parseTimingProfile` only requires a non-empty string (`consumer.ts:502-505` and the later evidence `stringAt` calls). Missing files are accepted.

`consumer.test.ts:262-393` hardcodes those dead paths, so retargeting the profile without editing the test fails, and leaving them keeps the profile unhooked from `timing/evidence/`. The six files exist under `timing/evidence/` with the same basenames.

Flexe replay still imports `../../packs/esp32-s3-touch-amoled-18/timing/...` (`experiments/esp32s3-flexe-wasm/timing-replay-test.ts:8-19,100`, `full-elf-timing-replay-test.ts:5-16,156-158`, `trace-timing-adapter.ts:5-16`, `flexe-load-use.ts:1-2`, `flexe-beqz.ts:1`, `trace-timing-adapter-test.ts:9-10`). Those files now live at `timing/`.

### 5. JIT fast-path hits never touch `Bus`. Decision 0011 still says they do

On `puck/base` at `3051793`:

```
esp32s3/src/bus.rs:1097-1101
    fn fast_mem(&mut self) -> Option<FastMem> {
        Some(FastMem {
            tlb: self.tlb.as_ptr(),
            page_ver: self.page_ver.as_mut_ptr(),
        })
    }
```

`xtensa-lx7/src/block.rs:235` sets `fast = bus.fast_mem().is_some()`. JIT loads/stores then host-load/store through that table (`xtensa-lx7/src/jit/mod.rs` around 736-816). Hits never call `Bus::read*` / `write*`. Helpers do call `bus.note_pc` plus `Bus`. Fast hits do not, so even `cur_pc` attribution is wrong under JIT.

CORE.md and decision 0012 still cite pre-rustfmt `block.rs:143,187` and `esp32s3/src/bus.rs:622`. Today `bus.rs:622` is SHA DMA. The finding is not stale. The line pins are.

Decision 0011:44-46 still says the graft points already exist because "every memory access crosses the `Bus` trait." That is false on the native JIT. 0012/0014 papered F-031 as interpreter-first. There is still no CPU-backend observation seam, no measured flag, no pending instruction, no `TimingBlocked`. Wrapping `SocBus` is a silent miss on SRAM, mapped flash and mapped PSRAM.

Wasm is already interpreter-only (AArch64 JIT compiled out). The browser path is the safer first host. Native measured mode must not enable JIT until the 0012 conformance program.

### 6. Kickoff clones the wrong tree

`lanes/COORDINATOR.md:29-35` tells the agent to clone only `esp32s3-cycle-accurate-wasm` on `main`. Every product brief's Home is the esp32sim fork (`lanes/CORE.md:3`, `BOARD.md:3`, `SPEED.md:3`, `SHIP.md:3`). The template never names `puck/base` or `core/*`.

An agent who follows the kickoff literally cannot write product code. An agent who reads CORE.md and guesses the fork can start typing. That is not the same as a complete handoff.

### 7. Schema 2 is assigned, not specified, and the freeze forbids specifying it

Decision 0008:73-76 said `timing.json` and the timing machine's cost types would grow tier fields (`exact`, `affine`, `interval`, `distribution`, `unexplained`). They did not. `TimingProfileV1` is still `schemaVersion: 1` (`timing/consumer.ts:61-68,435-453`). `exactKeys` would reject those keys if someone added them to schema 1.

Decision 0014 rejects schema 1 for measured totals and assigns schema 2 to lane B / CORE. There is no schema-2 file, no JSON Schema, no Rust types. The lane-b spike assigned an owner. It did not specify fields.

Coordinator rule: cross-lane timing vocabulary needs a decision record before merge (`lanes/COORDINATOR.md:57-59`). META FREEZE: no new decisions until measured mode executes its first adapter instruction (`AGENTS.md`). That first instruction needs the schema-2 importer. Catch-22.

`roadmap.md` listing CORE as "Fully" unblocked is false.

Refusals in this machine also fail 0008's "a refusal must state its tier candidate." Costs are `calibrated | uncalibrated | unknown`. An unknown MMIO reason string is not a tier. Measured-mode ledgers that carry 0008 fields cannot be compared field-for-field to this JSON without a translator the gate is not allowed to invent.

### 8. Flexe decoder fails open on unimplemented S3 ops

Patch `experiments/esp32s3-flexe-wasm/patches/0003-add-esp32s3-lx7-subset.patch` (around 993-997): unimplemented `exec_esp32s3` returns 0 and falls through to the LX6 decoder. Unknown QRST/LSAI cases `default: break` (instruction counted, no-op). Unknown user registers do fail closed. That is the exception, not the rule.

Unsupported markers auto-install only when `elfSha256` matches one pinned inventory (`full-elf-runner.ts:428-432`). The browser runner never passes `unsupported` (`browser/main.ts:71-75`). Any other ELF, including the same firmware rebuilt, runs with an empty marker table. Colliding `ee.*` encodings execute as LX6.

"Adjacent encoding fail-closed" tests inject a host marker (`dynamic-runner-test.ts:493-498`). They do not prove the decoder refuses the encoding.

ISA markers are 16/24-bit only (`isa-inventory.ts:413-421`). The wasm binder never treats 4-byte encodings as 4-byte. ISA "support" is mnemonic-in-`EMIT()` plus a hardcoded patch list (`isa-inventory.ts:196-218`), not encoding-accurate execution.

CORE's "flexe boot replay" gate is this stack.

## P1

### 9. STATUS silicon headlines are not receipt-pinned in this tree

AGENTS.md: no receipt, no claim.

| Headline | Where the number actually lives |
| --- | --- |
| 35-cycle window pair | Only `timing/evidence/idf61-rebaseline-3db3985/toolchain-delta.json:36`. No named kernel in exploded receipts. STATUS cites the IDF 6.1 README, which does not quote 35. |
| +1 loop alignment at +3 mod 4 | Only `toolchain-delta.json:38` (`loopResiduePlus3AdditionalCyclesPerIteration: 1`). No kernel. |
| 1.000 cycles per straight-line instruction | Narrative in `timing/evidence/README.md:42-47`. Profile cites `esp32s3-rev02-tinydraw-bf169bc-counters-candidate.json`, which is `status: "candidate"`, `isaClaim: "not-claimed"` (lines 2-8). `sram_instruction_issue_single_core` is 9234 cycles exact over 300 samples and 3 boots. Nothing in-repo divides that by an instruction count. |
| IRQ 227/143 and 222/139 (from 228/142 and 223/138) | `toolchain-delta.json:47-64` only. Not in `timing.json`. |
| Boot to first output 0.582 s to 0.472 s | `toolchain-delta.json:66-72`. v6.1 samples `[0.4316, 0.4724, 0.4764]`, median `0.4723518749997311`. |
| 802 passing receipts, 210 identities, 204 two-boot | Arithmetic in the README and `toolchain-delta.json:19-33` (199+203+199+201=802; 204+6=210). Tarballs hash-pin (`SHA256SUMS` verified). No test opens the archives or runs `parseHardwareCalibrationReceipt`. |
| Six identities below strict recovery | Named in the IDF 6.1 README and `toolchain-delta.json:24-33`. Recovery of the other 204 is not re-checked in this repo. |

`lanes/CORE.md:40-41` then tells phase 1 to implement the window pair and loop rule from those headlines.

### 10. The TypeScript machine does not model the costs CORE must implement

Grep of `timing/{machine,execution,cache,runtime-trace}.ts` finds no window exception, no loop alignment, no CCOUNT. Every runtime-report instruction gets issue cost 1 (`runtime-report.ts:162-236`). `beqz` 1/3, load-use +1, window 35 and loop +1 are absent. `dependentLoadUseHazard.status` is `"unmodeled"` in the profile, and runtime-report does not even surface it as unknown on dependent loads.

`runtime-report.test.ts:30-31` then asserts `costCalibration: "calibrated"` and `unknownCostEventIds: []` on a 4-event SRAM fixture. That is a characterization test of a partial replay labeled complete. A taken `beqz` and a dependent SRAM load through `runRuntimeTimingReport` will be short 2 and 1 cycles relative to the adopted receipts.

Decision 0014 forbids writing new TypeScript for the gate, so those costs cannot be added to the oracle. The honest gate is identical traces, identical refused names, identical unknown sets. Not equal cycle totals.

### 11. IDF 6.1 is not what the profile uses

Exploded adoptions (beqz, MMIO, burst, ROM, bf169bc, hot-hit) are v6.0.2. IDF 6.1 receipts exist only as `timing/evidence/idf61-rebaseline-3db3985/receipts/*.tar.gz`. No test in this repo parses them or re-adopts costs. STATUS headlines rest on `toolchain-delta.json` plus the README, not on the production parser running in this tree.

### 12. No cache-burst adoption test

There is no `*-adoption.test.ts` for `esp32s3-rev02-tinydraw-a91d1d7-cache-burst-adoption.json`. I-cache and D-cache tests use synthetic JSON (`icache-burst-analysis.test.ts`, `dcache-burst-analysis.test.ts`). Burst OLS on the committed analysis files is correct (see verification snapshot). CI will not notice a drifted receipt, a rewritten analysis file or a hash mismatch feeding `cacheLineFillCycles`.

`receipts-a91d1d7/boot-1` is missing `dcache_psram_burst_1_lines_hot_single_core.json`, `dcache_psram_burst_8_lines_cold_single_core.json` and `icache_flash_burst_8_lines_cold_core1_contended.json`. D-cache analysis uses boots 2 and 3 only (40 receipts). I-cache uses all three (24 receipts). That is fail-closed, not a math bug.

### 13. Store-buffer drain does not apply to loads, atomics, MMIO or cache fills

`timing/execution.ts:478-480`: `waitsForStoreBuffer` is `store || fence` only. A same-core load issues while the prior store is still draining. Tests lock CPU-overlaps-drain (`trace-adapter.test.ts:424-434`) and never ask a load to wait.

Opt-in buffering also demands exactly one cache `store` emission (`machine.ts:851-907`). Line-split stores, dirty writeback, write-through and MMIO throw instead of a named 0008 unknown. ESP32-S3 D-cache is write-back allocate. A buffered store miss that evicts dirty, or a word that straddles a line, aborts the whole run. `machine.test.ts` has no `storeBuffer` cases.

### 14. Dual-core is first-class here. Measured v1 is core 0 only

`machine.ts:54-57` requires two core streams. Default tie-break is core 0 wins (`execution.ts:542-552`, `machine.test.ts:654-677`). Decision 0014: capability absence is a typed refusal. A shared dual-core trace is a false fail (Rust refuses, TypeScript runs) or a false pass (compare only core 0 and ignore TypeScript core-1 MSPI contention).

TinyDraw already runs both cores on esp32sim (`experiments/esp32sim-adoption/README.md`: both cores, FreeRTOS, dual-core idle loop). BOARD demo of the real image is dual-core fast mode with 64-instruction quanta.

### 15. Default issue order is not program order

Omitted `issueOrder` dumps all memory, then all CPU, then fences, then DMA (`machine.ts:68-69,558-576`). The runtime recorder always fills `issueOrder` (`runtime-trace.ts:343-350`). A gate that calls `runTimingMachine` directly without it serializes execute after the entire memory stream.

### 16. CCOUNT is unbounded bigint. 0014 wants a wrapping u32 projection

`execution.ts:230-234,764`. Calibration receipts do wrap (`calibration.ts:3-4,449-453`: `(endCcount - startCcount + 2^32) % 2^32`, tested at `calibration.test.ts:111-124`). The scheduler never wraps, never exposes CCOUNT, never handles CCOMPARE.

CCOMPARE block limits in the fork assume 1 cycle/insn (`xtensa-lx7/src/block.rs:297-304` uses `ccompare - ccount` as an instruction limit). The first measured cost that is not 1 (window pair, MMIO slope, cache fill) makes timer IRQs land on the wrong instruction if that math is reused.

Zero-length kernels (`startCcount == endCcount`) are refused because `cycles` must be >= 1. A kernel that wraps exactly `2^32` cycles would also compute expected 0 and fail. Unlikely for these microbenchmarks.

### 17. `Machine::run` cannot host measured mode

Fork `esp32s3/src/machine.rs:634-739`: 64-insn dual-core quanta, core 0 then core 1 if released, 512-cycle idle skip (`QUANTUM * 8`) when both idle, lazy 256-cycle device deferral, wall-clock pacing in `after_round`. Stop budget is `max_insns`, not a virtual-cycle deadline.

`cycles_until_timer` covers systimer and TIMG only (`periph.rs:2274-2306`). Everything else is `MAX_TICK_DEFER`. Decision 0014: unknown is `TimingBlocked`. Fast mode's "256 is fine, WAV stayed bit-identical" is a throughput choice, not a measured-mode policy.

Slice invariance cannot hold if CORE reuses this loop. 0014 is right: keep it as fast mode. Measured mode needs a separate `run_until` that never enters it. JIT is on by default on aarch64 (`block.rs:106`, `cli/src/main.rs:273-276`). `--no-jit` only clears `jit_enabled`; the block interpreter still runs. Wasm has no `--no-jit` equivalent.

### 18. Wasm is an unversioned Machine ABI, not the adapter

`esp32sim_alloc` is unbounded (`wasm/src/lib.rs:73-77`). `esp32sim_run(cycles, unix_ms)` injects host Unix time into SNTP, sets `max_cycles`, then calls `m.run(u64::MAX)`: cycle cap on the bus, infinite instruction budget. Panic hook logs to JS (`lib.rs:94`). Worker paces with `performance.now()` and `Date.now()`.

Product board names in `esp32sim_new` are `atech14` / `waveshare-lcd4b` / `waveshare-cam` / `none` (`board.rs:56-64`, `lib.rs:84-96`). There is no Waveshare AMOLED 1.8 board. CLI default `--boot app` (`cli/src/main.rs:27`), not real ROM plus complete flash. Identity is CLI text overlays into `init_regs`, not opaque hash-pinned artifacts.

Do not wrap `wasm/src/lib.rs` and call it `backend-api`. Do not put 0014 types onto `Machine` in place.

### 19. Decoder / JIT tests fail open. CI is Pages

`xtensa-lx7/tests/objdump_diff.rs:33-36` returns success if `XTENSA_DIS_FILES` is unset (it prints that the env var is unset and returns). Same skip on RISC-V. AArch64 encoder test skips without Apple clang (`jit/a64.rs:459-465`).

Hermetic tests that actually run: crypto unit tests, two periph tests, the skippable encoder and objdump tests. `cargo test --workspace` can be green while the decoder corpus never ran. "977 544 instructions, 0 mismatches" is README/architecture prose (`README.md:13`, `docs/architecture.md:49`). F-048 already called this fail-open. The fail-closed corpus lives on unmerged `lane-g` (`esp32s3-lane-g/esp32sim/xtensa-lx7/tests/corpus/mandatory.dis`). SHIP.md forbids touching those branches until the release workstream.

This repo has no `package.json`, no `.github`, no CI. Fork CI on `puck/base` is `.github/workflows/pages.yml` only. JTAG "8000 steps, 0 PC divergences" is a BOARD receipt (`lanes/receipts/E-01-jtag-lockstep.md`), not a fork test, and it does not claim CCOUNT.

### 20. Pages fetches mask ROM from `releases/latest` with unpinned actions

Fork `.github/workflows/pages.yml:19-30`: `actions/checkout@v4`, `dtolnay/rust-toolchain@stable`, `Swatinem/rust-cache@v2`, then:

```
url=$(curl -sL https://api.github.com/repos/espressif/esp-rom-elfs/releases/latest | python3 -c '...')
curl -sL "$url" -o rom.tar.gz
```

Product receipts need a hash. ROM is Apache-2.0 `esp-rom-elfs` (decision 0009 is sound). Native lookup is `~/.espressif/tools/esp-rom-elfs/*/esp32s3_rev0_rom.elf`. Wasm docs correctly say ROM is gitignored. Redistribution of an unpinned blob in Pages is not a receipt.

### 21. Product DoD is a conjunction. Dispatch is not

`roadmap.md` definition of done: real merged firmware on dual-core S3, at real time on M1-class hardware in a browser, with cycle accounting that passes a silicon correlation suite at decision-0008 tiers.

Decision 0010 measured, on an Apple M1 Pro with Chrome 151: pinned flexe interpreter 109 MIPS in V8; JIT-ceiling about 4,600 emulated MIPS; dual-core worst case 480M emulated instructions per second. Interpreter cannot be real time. 0014 measured v1 is interpreter-only, single core, networking off. SPEED is "not in the current sequence" (`STATUS.md`, `lanes/COORDINATOR.md`).

The two-mode split (fast vs measured) is the architecture that would make this non-contradictory. The DoD and the README one-sentence do not say that. They sell one emulator that is both live and cycle-accurate. Under the current dispatch (BOARD demo, CORE 1, CORE 2, SPEED never) that product cannot exist.

Not a reason to throw out 0014. A reason to stop calling the conjunction the definition of done.

### 22. 0014 still archives the CORE differential receipt "in puck"

`decisions/0014-measured-scheduler-and-adapter-contract.md:97-100`: "archives the comparison as a receipt in puck. The TypeScript machine is then retired as donor history." The amendment at 0014:110-114 retargets only "Numbered decision home: this record, in puck," not this paragraph. `AGENTS.md` and `PROVENANCE.md` forbid new puck writes. CORE.md:44-47 correctly says archive the receipt here.

### 23. 0011 is still wrong in the accepted body. 0010 was never amended

0011:44-46 "every memory access crosses the `Bus` trait": false (finding 5). 0011:54 "SH8601-class QSPI panel": living docs say CO5300-class (`0013:43`, `BOARD.md:70`, `roadmap.md:69`). 0011:39 locates the pin in `docs/roadmap.md`: 404 here (root `roadmap.md`). 0011:12,74 still say "roadmap lanes 1 and 3" (pre-collapse numbering).

0010 is still accepted with "engine written in this repository," "TypeScript-only rule" and "The TypeScript code is not the production path and is not deleted" (0010:31-56). 0011 superseded build-here. 0013/0014 retire the TS machine after one shot. This repo is not TypeScript-only. 0010 was never amended. SPEED still binds 0010's checkpoint.

0010 also still wants timing compiled into generated JIT code. 0014 says JIT observation waits for cross-mode conformance. SPEED.md: "Design for later measured-mode participation (inlined accounting seams), but correctness and speed first." That is 0010 leaking into a lane that 0014 forbids from measuring until the proof exists.

### 24. Chip identity is "captured and accepted" by pointing at a request

`STATUS.md:49-50` cites `lanes/requests/A-01-v2-controller-and-identity.md`, not a receipt. `lanes/receipts/` has only E-01. A-01 still contains unfinished bus/touch capture and names "CST816S-family" (`A-01:8-9`) while 0014 forbids naming the touch controller until the on-device ID probe. The archived review said CST820. Firmware log says CST816S (`experiments/esp32sim-adoption/README.md:41`). Three names, none adopted as a receipt.

0014 cut chip-identity formats from the adapter contract. Captures exist. Formats do not. CORE cannot apply identity through the adapter without inventing the format 0014 forbade. Clawback needs a decision. Freeze forbids that.

### 25. E-01's raw lock-step bundle is a laptop path

`lanes/receipts/E-01-jtag-lockstep.md:4,38-39`: owner "lane E"; raw bundle `/Users/sarah/src/a/esp32s3-lane-e/esp32sim/out/lane-e/idf61/jtag-lockstep`. Hashes in the markdown receipt. Bytes not in this repository. Same class as fixture ELFs (`STATUS.md:79-80`, `PROVENANCE.md:33-35`): hash-as-receipt is OK, files are maintainer-local, cloud CORE cannot replay them.

### 26. Flexe and browser-speed experiments cannot run here

`experiments/esp32s3-flexe-wasm/full-elf-runner.ts:1` imports `../../src/wasm` (puck's loader). `puck-loader-test.ts:3` same. `test.ts:18-28` chains puck-loader, dynamic runner, both timing replays, full-elf runner and ISA inventory. None of those can start.

All three experiment `tsconfig.json` files extend missing `../../tsconfig.json`. Defaults: `TINYDRAW_ROOT=/Users/sarah/src/a/tinydraw`, flexe at `/private/tmp/flexe-34ea9eb6eef921b59a55e6a435c7fc55c5727835`, objdump under `~/.espressif/tools/xtensa-esp-elf/esp-15.2.0_20251204/...` (`constants.ts:4-35`). Browser-speed wants `puppeteer-core` with no package manifest.

Archive banners on the experiment READMEs are honest ("any build or run commands in this document executed in that repository, not this one"). CORE is still told to use the flexe boot replay as the gate. Decision 0013: no TypeScript execution engine is ever built; the existing timing machine is one-shot donor only. These files are a driver, sparse ELF loader, ROM/MMIO host and timing replay. Archive status and META FREEZE keep them from growing. Treating them as product work still violates 0013.

### 27. QEMU "oracle" tests never talk to QEMU

`experiments/esp32s3-qemu-oracle/test.ts` compares corpus JSON to a fixture whose `qemuPath` is `fixture/qemu-system-xtensa` and whose `qemuSha256` is 64 ones (`fixtures/espressif-qemu-observation.fixture.json`). Live mode is skipped unless tools exist (`test.ts:29-38`).

Live runner issues: GDB dumps instruction bytes using the corpus reference widths, then `stepi` that many times (`run.ts:179-183`); width is not independently decoded. `parseCaseOutput` sets `steps: caseEntry.instructionBudget` (`run.ts:267`), not a counted GDB step total. Termination is always `instructionBudgetReached`. `assertTraceMatchesCode` requires linear PC = start + sum(widths) (`oracle.ts:227-241`); branching cases would be accepted only if they accidentally match that line. Pinning is `ESP32S3_QEMU_COMMIT` env equality (`run.ts:87-90`), not a hash of the binary. `qemuVersion` then appends `[branch esp-develop]` regardless (`run.ts:342`). `scalar_probe` runs at `0x40070000` (`flexe-corpus.json:15`), which is not ESP32-S3 IRAM (`0x40370000`). Live writes into `0x40070000` can hit ROM.

Decision 0011 demoted QEMU to tie-breaker. The default test is a JSON self-check.

### 28. README numbers do not match the baselines tests lock

RGB565 replay README: 451 cycles, 98 issued, 43 I-cache hits, two line fills, per-record SHA `4e241941...` (`experiments/esp32s3-flexe-wasm/README.md:377-391`). Baseline JSON the test locks: `totalCycles: "655"`, `issuedEvents: 100`, `hit: 44`, `line-fill: 3`, `perRecordSha256: "579133c0..."` (`esp32s3-timing-replay-baseline.json:51-68`). Tests compare to JSON, not the README (`timing-replay-test.ts:483-488`). Trace SHA in the test is `833a31ab...` (`timing-replay-test.ts:34`); README cites `3a977c98...`. sdkconfig SHA in README `ac1749b0...` vs baseline `44c7f88a...`.

Browser-speed README table: Chrome interpreter 109.0 MIPS, JIT ceiling 4,618 (`experiments/esp32s3-browser-speed/README.md:50-53`). Checked-in confirmation: 104.99 / 4392.79 (`results/2026-08-31-chrome.json:10-16`). Native JIT 8587 vs table 9705 (`results/2026-08-31-bun-and-native.json`). The README already flags the confirmation run (102 to 105 MIPS, Chrome ceiling 4,393) and says conclusions rest on 4x to 10x margins. SPEED.md and 0010 quote the table, not the JSON. There is no test that re-runs the benches.

Browser-speed still sizes boot against an "837-instruction boundary" (`README.md:96`). Full-ELF baseline and tests are 940 steps (`esp32s3-full-elf-baseline.json`, `full-elf-runner-test.ts:838`). PIE fixture SHA in flexe README `3cb3f1d4...` vs ISA test pin `1b0475db...` (`isa-inventory-test.ts:121`), which matches STATUS's vector demo. Browser-speed boot-time 0.577-0.595 s is IDF 6.0.2; STATUS's IDF 6.1 median is 0.472 s.

### 29. Networking default has a trap

CLI creates AP plus subnet only if `--wifi` is passed (`cli/src/main.rs:66-67,179-226`). No `--wifi` means no radio. If `--wifi` is set, `--net` defaults to `"nat"` (`cli/src/main.rs:67`) and NAT to host sockets is enabled unless `--net none`. Decision 0012 ("Networking in the emulator defaults to none. Live egress is opt-in") is true for the radio, false for the NAT default once wifi is requested.

Wasm: no NAT (`wasm/src/lib.rs:211-246`). Wifi is still a public `esp32sim_wifi` entry. Demo manifests ship a wifi spec (`web/wasm/fw/README.md:6`). Browser wifi is opt-in, not on at `esp32sim_new`. Easy to copy `"nat"` into the adapter as "NAT on."

### 30. BOARD demo is untrusted guest code on the unhardened ABI

Decision 0012: untrusted always includes firmware images, Wasm modules and anything a browser visitor uploads. The public gallery and any path that executes third-party material get the full boundary treatment. Hardening is SHIP, which waits for CORE's validator seam, and SHIP is not dispatched.

RESPONSE.md declined pausing implementation for the foundation gate. Gate 0 (license note, provenance file, trust ADR, V2-first) is "complete." The P0 validator/WASI findings are not in the fork. A BOARD browser demo of real TinyDraw is firmware on `esp32sim_run`. Safe only if that demo is never public. `pages.yml` publishes `web/` on push to `main`. Public by design.

`parseXtensaElf32WithDigest` copies the whole file with no byte cap (`elf-image-core.ts:51-121`). A huge ELF can OOM a tab before the runner's 768-page check. Browser `MAX_STEPS = 1024` (`browser/core.ts:6`). Server is `127.0.0.1` only (`browser-server.ts:21`). Decision 0012 treats local own-firmware as out of threat scope. Still no size quota. `Bun.serve({ development: true })` (`browser-server.ts:23`). No CSP.

## P2

### 31. Silent "ready at 0, calibrated" when `earliest` is omitted

`timing/execution.ts:506-507`: `earliestCalibration` defaults to `"calibrated"`. Missing readiness is not unknown. Mixed traces can look calibrated because the scheduler filled in a calibrated zero.

### 32. Cache tags physical offsets, not window virtual addresses

`address-map.ts:401-408`, `mmu.ts:462-471`, `cache.ts:470-476`. ESP32-S3 MMU sits behind the cache: miss fill uses physical, tags are in the IROM/DROM virtual windows. Set index bits sit inside the 64 KiB page so identity-free injective maps hit the same set. Aliases (two DROM pages, one physical) collapse to one line here and would not on a virtually tagged cache. Remap without invalidate would miss here and hit in hardware.

### 33. Shared D-cache bursts are still per-core clients

`cache.ts:598-623`. `clientId` is `cache:${core}:${cache}:${memory}` and continuation requires `previous.core === core`. Sequential shared-D misses from both cores always pay first-line again. Fine for v1 single-core. Poison for any dual-core miss burst comparison.

DMA on MSPI correctly nulls `previousMspiBurst` (`execution.ts:730-732` plus `mspiBurstFor(dma) === null`), matching the documented "intervening DMA restarts the fill cost."

### 34. `literal-load` to MMIO would be classified as a write

`address-map.ts:418-424`: `operation` is read only if `kind === "load"`. `literal-load` falls through to write. Unreachable today because MMIO cannot be executable (`address-map.ts:269-272`) and literal-load uses execute permission. Still a type lie if that guard moves.

### 35. Instruction width 4 is accepted

`trace-adapter.ts:87-92`. A 4-byte "instruction" is not an LX7 fetch. The adapter will invent a 4-byte I-side access.

### 36. Panel `frequencyCalibrated: true` has no `evidence` field

`timing/timing.json:209-214`: `busHz: 40000000`, `frequencyStatus: "measured"`, `frequencyCalibrated: true`, `throughputCalibrated: false`. No evidence path. STATUS's hardware queue still lists the logic-analyzer capture as gating panel timing-accuracy claims. Derived payload 20 MB/s from 40 MHz 4-lane 16 bpp is arithmetic, not a receipt.

### 37. Panel `model.ts` / `consumer.ts` scheduler is a different machine with guessed producer cost

`consumer.ts:1039-1073`: `producerCyclesPerByte` defaults to `1/1`, `queueDepth` is hardcoded 3, calibration is "uncalibrated" but a complete schedule is still emitted. Not the execution ledger. Must not mix into the measured-mode gate. This is leftover puck shadow-ledger / strip scheduler (`consumer.ts` also decodes `emu_timing_*` Wasm exports, SNAPSHOT_V1 144 bytes).

### 38. Same-cycle tie-break flips when `memoryIssueSchedule` is present

`machine.ts:926-930`. Explicit `issueOrder` uses `"input-order"` only if there is no ready-clock schedule. With both, core-0-wins returns. Two traces that differ only by whether ready clocks were attached can change same-cycle order.

### 39. `evidence/README.md` a91d1d7 decompressed log hashes do not match receipt `bootLogSha256`

README lines 77-80 claim decompressed SHA-256:

- `396a188e4eea3663cc3c90e6e647264ce9684cb68c4c51a39591022ddf8f5c74`
- `f7f23872490223437313b00a12b9833802ae51481fab4505389092db6ad0fb33`
- `6cbf1b1c8380bbf57850c272be86a6f47a303eab8668939329c56729b9796ecd`

Receipts (example boot-2 `icache_flash_burst_1_lines_cold_single_core.json:34`):

- boot-1 `396a188e1cb4843a723801013e6fea6d881e099dcb9164958d634e7becdff962`
- boot-2 `f7f23872797e2c2545f1c71c37903a260993ead6e43c605e5d70d6fbb77662be`
- boot-3 `6cbf1b1c4e9fdc58065e13631944e5c144268d645d8e3d390263ba4a0ecee7a4`

Same 8-hex prefix, different rest. Beqz adoption binds decompressed capture hash to `boot.bootLogSha256`. Cache-burst has no such test. The hardware receipt parser does not hash `bootLogSha256` against a log file. That is left to adoption tests, and cache-burst never does it.

### 40. IDF 6.1 README says receipts `report pass: true`

`parseHardwareCalibrationReceipt` uses `exactKeys` on a fixed key set (`calibration.ts:496-510`) and has no `pass` field. Extra keys fail. Either the tarball JSON is a different schema, or "pass: true" is tinydraw assembler language (same phrase in `mmio-slope-e8a9f0e/README.md:21`). Unverified: tarballs were not extracted in this pass.

### 41. Hot-hit two-boot is weaker than the cohort helper

`hot-hit-adoption.test.ts` checks 16 receipts and 2 per kernel, but does not call `aggregateCalibrationCohort` and does not require distinct `bootId`s per kernel. Beqz / MMIO / ROM tests do check the boot-id set.

### 42. `sdkconfig.path` in real receipts is a machine-local tmp path

Only the sha256 is the pin. Fine if everyone knows. Easy to treat the path as provenance. Parser accepts any non-empty `espIdfVersion` string (`calibration.ts:283`). Fixtures and burst tests still mint `v6.0.2`. Easy to miss a v6.1 mix later.

### 43. No root LICENSE on the fork

Workspace `license = "MIT"` (`Cargo.toml:8`). README declares MIT twice. No root `LICENSE`. Third-party: ArduinoJson LICENSE in `boards/atech14/hostsim/third_party/`. F-001 was accepted as "in-repo declaration is enough; ask the author for a file" (0011 risks section, fork `PROVENANCE.md`). Residual: GitHub and some distros look at `LICENSE`. This program-office repo also has no root LICENSE.

### 44. Untrusted ELF can panic

Fork `elf.rs` around 117-123 and 157-161: slice `d[nstart..]` after `shstr_off + name_off` with no `nstart <= d.len()` check. Wasm `kind=0/4` feeds visitor bytes here. `image.rs` is bounded; ELF is not fully.

### 45. `examples/wifi-station/build/` is a full IDF tree

Bootloader ELFs, objects, `CMakeCache` pointing at another machine's Espressif install. Redistribution and reproducibility hazard in the product home. Not CORE's job. It sits in the product home.

### 46. Three branch conventions

`lanes/README.md:26-28` wants `core/`, `board/`, `speed/`, `ship/` from `puck/base`, or from `main` when upstream-shaped. `README.md:43` says `lane-*/`. `STATUS.md:31-32,54` still cites live `lane-g/ci-spec` (`6ba6a6d`), `lane-g/upstream-ci` (`3b58cc6`) and `lane-a/gp-spi-device-hook` (`246c699`). Fork `PROVENANCE.md` still says `lane-*/` and "the puck repository's `docs/roadmap.md`."

### 47. Cloud vs local

`lanes/COORDINATOR.md:14-16`: no cloud agents; all agents run locally; the board is attached overnight. `roadmap.md:114-130` and `BOARD.md:8` still describe cloud-viable lanes (BOARD modeling yes, captures no; CORE fully cloud-viable). Not a contradiction if "cloud-viable" means "could run without the board." It will confuse a coordinator who reads both.

### 48. Overnight report does not exist

`COORDINATOR.md:74-77` requires a dated "Overnight report" section on `STATUS.md`. `BOARD.md:57` names the board owner there. `STATUS.md` has none.

### 49. Broken relative links in accepted decisions

- `decisions/0008:20` `evidence/esp32s3-rev02-tinydraw-e8a9f0e-mmio-write-adoption.json` resolves under `decisions/`. Real file: `timing/evidence/...`. Same for `0008:26` `evidence/receipts-bf169bc/`. Contrast `0008:15`, which correctly uses `../timing/evidence/rtc-boot-read-70cc31a/`.
- `decisions/0006:34` still names `packs/esp32-s3-touch-amoled-18/timing.json`.
- `decisions/0011:39` `docs/roadmap.md`.
- `decisions/0012:9` display text `docs/reviews/2026-08-31-external/`; href `../reviews/2026-08-31-external/` exists.
- `timing/README.md:12` href `timing.json` exists; display text `../timing.json` is stale.
- `timing/README.md:36` href `../experiments/esp32s3-flexe-wasm/` exists; display text `../../../experiments/...` is puck-era.

### 50. Letter lanes are still normative in binding records

Documented map is only in `lanes/README.md:8-10`: 0, A, E to BOARD; B, C to CORE; D to SPEED; F, G, H to SHIP. Binding records still assign work to letters:

- 0014:8,27,67-68,72,87,93,97,110-111: lane B, C, 0, A, E, H
- 0013:11,44,52: Lane B, lane A, lane B and lane F
- 0012:70-73: Lane B, lane F
- RESPONSE.md:37,43-45,50,55,67: lane B, new CI lane, boundary lane, lane A, lane F
- `lanes/requests/A-01`: lane E / lane A
- `lanes/requests/E-01-frontloaded-board-batch.md`: lane 0, E, A, C, B throughout
- `lanes/receipts/E-01-jtag-lockstep.md:4,39,49`: lane E
- `experiments/esp32sim-adoption/README.md:7,45`: lane A

A CORE agent who reads 0014 as current waits for "lane 0" to unblock first-line. RESPONSE.md's "accepted as a new CI lane" / "boundary lane" would mint lanes a coordinator following RESPONSE might try to create. META FREEZE forbids that. Current map folds those into SHIP.

### 51. Hardware queue vs BOARD open list

`STATUS.md:82-89` queues only the logic-analyzer capture. STATUS lane-state (`STATUS.md:59-68`) and `BOARD.md:95-107` also list first-line diagnosis, six weak identities, PSRAM long-window, arbitration probes and CCOUNT lock-step. The queue is the serial-port rule. The open list is the work. An overnight coordinator who only reads the queue under-serves BOARD.

### 52. `puck/base` pin wording

`lanes/README.md:24-25`: "pinned base (`2114ffc`) plus fork-carried commits." `STATUS.md:32-33`: rustfmt-clean at `puck/base` (`3051793`). Compatible if 2114ffc is the upstream pin and 3051793 is branch HEAD. An agent can treat them as conflicting pins. Fork `PROVENANCE.md` states the pin history clearly.

### 53. Lane-b draft still contradicts the cut list if anyone implements from it

`/Users/sarah/src/a/esp32s3-lane-b/esp32sim` branch `lane-b/design-spike` off `aa85124` (pre-rustfmt). Extra files:

- `docs/browser-emulator-adapter-scheduler-decision-draft.md`
- `docs/browser-emulator-measured-mode-adapter-spike.md`

0014 called those historical. They still name CST820, `ChipIdentity` with efuse layout and a 152-byte filter receipt, crates `browser-backend` and `timing-model`, exhaustive `MAX_*` quotas, hash-chain / per-call delta hashes and chunked artifact loading constants. Those types are not in lane-b Rust. CORE must not read those two markdown files as the contract. 0014 is the contract.

### 54. Overlapping `PT_LOAD` permissions are OR-merged

`full-elf-runner.ts:257-261`. W^X is not enforced. Combined with no ELF byte cap (finding 30) this is a local-experiment hazard, not a product ABI, but it is the loader CORE's flexe gate would use.

### 55. Oracle memory wrap

`experiments/esp32s3-qemu-oracle/oracle.ts:203-207`: `assertNonOverlappingMemory` uses `>>> 0` on `address + length`. A span that wraps 32-bit can sort as non-overlapping.

### 56. ISA inventory is static coverage, not execution coverage

README says so (`experiments/esp32s3-flexe-wasm/README.md:144-147`). `supportedByFlexeDecoder` is still advertised in JSON as decoder support. `.byte` rows are excluded from markers (`isa-inventory.ts:414`) but counted in unsupported row totals. Inventory `elfSha256: "4e121a3642a6f18766cfe96c2be6adc8a0017fba4afa82105d642168ea40e2c8"` matches STATUS's gate-harness pin. Tests need local ELF plus objdump plus flexe.

### 57. Non-US spelling in accepted decisions

"behavioural" in `0006:8,22,70`, `0007:98`, `0011:84`. `AGENTS.md:78` requires US English. Load-bearing because these are the accepted records.

Stale verify commands in evidence READMEs still say `bun puck/timing/verify_calibration_receipt.ts` or `packs/esp32-s3-touch-amoled-18/timing/...`. That script does not exist in this repo. Parser coverage is `calibration.ts` plus tests.

`timing.json` `PROFILE_SOURCE` and several evidence READMEs are archive-bannered as puck-era. CORE is pointed at `timing/README.md` as background. Harmful because of that pointer.

## P3

### 58. `useSequence` / `lineFillSequence` never wrap

Hardware LRU counters do. Irrelevant for short traces.

### 59. `atomic` and `cache-op` exist on the scheduler and are never produced by address-map, cache or runtime-trace

Dead surface for the gate.

### 60. `timingMachineJson` stringifies bigints as base-10

`runtime-report.test.ts:34-37` already compares `"2"`. Rust JSON that emits numbers or hex will mismatch even when cycles agree. Do not `JSON.parse` into `number`.

### 61. Hit-after-fill always emits a hit

`cache.ts:797-809`. Documented: the local hit emitted for a newly filled segment does not break the burst candidate. Harmless while hit additive is 0. If anyone puts a nonzero hit cost on a miss path, miss cost is fill plus hit.

### 62. F-031 line pins are stale after rustfmt

Update CORE.md / 0012 notes to `block.rs:235,314`, `jit/mod.rs:736-816`, `esp32s3/src/bus.rs:1097`. Do not hunt `bus.rs:622`.

### 63. `Bus::tick` returns 0 or 1, not a deadline

Name collision with 0014 device time.

### 64. Block `resume` is a cache cursor, not a pending instruction

It exists so quantum cuts do not fragment the arena (`docs/decisions.md:153-154` upstream). 0014's pending instruction is a priced transaction that persists across `run_until` calls.

### 65. Em dashes

None in current `decisions/`, `lanes/`, `STATUS.md`, `README.md`, `roadmap.md`, `AGENTS.md`, `timing/*.md`, `experiments/*/README.md`. Many in the frozen 2026-08-31 review archive. Project rule is "anywhere." Archive is the only hit in this tree.

### 66. Puck-era names leftover in experiments

Schema strings still say `puck/esp32s3-qemu-corpus@v1`. Temp dirs named `puck-flexe-wasm-`, `puck-esp32s3-qemu-`. `browser/index.html:12` still says "Puck experiment." Harmless.

### 67. Calibration fixtures still mint `espIdfVersion: "v6.0.2"`

Fine for historical receipts. Parser accepts any non-empty version string. Easy to mix v6.1 later.

### 68. SPEED 109 vs 105

0010 and SPEED.md quote the README table. The committed Chrome JSON is the confirmation run the README already flags as 102 to 105. Host-side numbers vary 10 to 15 percent. Conclusions rest on 4x to 10x margins. Not a false receipt. Easy to over-quote the table.

# Part 2. Adversarial architecture review

## Architecture model used

The program is four repositories:

| Repository | Role now |
| --- | --- |
| this one | Program office: decisions, roadmap, lanes, receipts, reviews, timing lab, experiment records |
| `aliceisjustplaying/esp32sim` | Product: Rust machine, measured mode, adapter, wasm bridge, web shell. `main` mirrors upstream. `puck/base` is the fork. |
| `aliceisjustplaying/puck` branch `codex/esp32s3-timing-model` | Frozen archive and donor |
| `aliceisjustplaying/tinydraw` | Probe firmware and capture tooling |

The engine should be two modes sharing one core: fast mode is upstream, never taxed; measured mode is this project's addition, interpreter-first, costs from this lab under decision 0008. Observation is at the CPU backend, never a `Bus` wrapper. One versioned adapter is the only product entry. The TypeScript timing machine is a one-shot differential gate, then donor history.

That model is sound. The implementation of it has not started. The documents that describe it still contain puck-era paths, letter lane names and at least one known-false technical sentence (Bus).

## Attacks, prosecuted

| Attack | Result |
| --- | --- |
| 1. Product identity split | **Defect.** Map is almost usable (`README.md`, `lanes/README.md`, `PROVENANCE.md`, fork `PROVENANCE.md`). Kickoff, puck-era paths, letter lanes and three branch conventions will send an agent to the wrong tree or the archive. Fork has no `backend-api`. |
| 2. 0010 JIT-first vs 0014 interpreter-first | **Defect in the DoD.** Two-mode split is documented and accepted. The product sentence is a conjunction. SPEED is not dispatched. Interpreter cannot hit dual-core real time (0010's own receipts). |
| 3. Schema 2 | **Defect. CORE is blocked on an unwritten format.** 0014 rejects schema 1. Nothing specifies schema 2. Freeze vs coordinator vs importer is a deadlock. |
| 4. TypeScript machine as last oracle | **Defect in the donor.** Gate-then-retire is accepted. The machine is two-core, schema-1, no window/loop/CCOUNT, first-line "calibrated," SRAM replays labeled calibrated. After retirement the only executable cost model is gone and it was already missing the silicon headlines. |
| 5. F-031 observation | **Open in the fork.** Papered in 0012/0014. No seam. `fast_mem()` always `Some` on `SocBus`. |
| 6. Demo-first BOARD | **Accepted milestone, identity risk.** BOARD can demo without CORE. The demo will look like the product because the README does not say "fast mode, uncosted, interpreter, panel hook." Adoption receipt already shows TinyDraw boots through dual-core idle, then dies on unanswered panel ID. |
| 7. Claim vocabulary ahead of implementation | **Accepted as current state.** Becomes a defect the first time someone says cycle-accurate. Correlation suite does not exist. Importer does not exist. First-line class is blocked. Six identities below two-receipt recovery, four of them contended-core1. |
| 8. Trust model vs SHIP after CORE | **Accepted for research. Unsafe if the demo or Pages is public.** Validator/WASI/quotas are SHIP. Wasm ABI is already the browser surface. |
| 9. META FREEZE missing decisions | **Process defect.** Necessary decisions that do not exist: schema 2 (cross-lane vocabulary), dual-core interleave policy (CORE phase 2; 0014: "Lane C must propose"), chip-identity artifact formats (cut; A-01 captures exist), snapshot schema (cut; puck freeze was how the old product proved pixels), measured-JIT observation (0010 wanted inlined accounting; 0014 forbids JIT until conformance). Schema 2 is the one that bites CORE tomorrow. Dual-core can wait for freeze lift after first instruction. |
| 10. External review P0s | **Paper vs code.** See table below. |
| 11. Dual-repo CI | **Defect, documented as SHIP.** Proof matrix is a markdown file. Nothing runs it. Timing tests cannot even be invoked from this repo without reconstructing puck scripts. |
| 12. ROM/ELF provenance | **Mix.** 0009 is sound. Pages fetches latest. Fixture ELFs and E-01 raw bundles are machine-local. F-003 asset inventory is not in this repo. |
| 13. Dual-core vs v1 | **Accepted for measured v1. DoD gap.** Product firmware is dual-core idle. Measured v1 must refuse core 1. Contended identities are exactly the weak six. |
| 14. 0014 cut list | **Mostly holds.** Cuts that hold for v1: byte-exact quota catalog, hash chains, wasm chunked protocol as contract, snapshots, `browser-backend` crate, naming the touch controller. Clawbacks that will come: chip identity formats (captures exist), wasm protocol (today's C ABI injects `unix_ms` as sim time, which 0014 says must not drive virtual time), snapshots (BOARD pixel acceptance has no freeze). Clawbacks need a decision. Freeze forbids that. |

## External review P0s, now

| ID | 2026-08-31 disposition | Code today |
| --- | --- | --- |
| F-001 licensing | README plus crate MIT accepted; author will be asked for a root LICENSE | Still no root LICENSE. Residual P2. |
| F-031 JIT Bus bypass | Interpreter-first in 0012/0014 | Bypass still in `jit/mod.rs`. No observation backend. Open P0. Cited line numbers drifted after rustfmt. |
| F-047 proof matrix CI | Accepted as CI lane, folded into SHIP | This repo: no CI. Fork `puck/base`: Pages only. Real CI frozen on `lane-g/*`. Open. |
| F-048 fail-open decoder | Accepted; fork CI makes corpus absence an error | `objdump_diff.rs` still skips if env unset. Mandatory corpus is on unmerged `lane-g`. Open on the product branch. |
| F-011 / F-012 validator / WASI | Boundary lane, now SHIP, scoped by 0012 | Product fork: none. Puck `lane-h` has extra guard files; that is the archive, not the product. Open, deferred. |

RESPONSE.md's "compressed Gate 0 is completed" is true as paperwork (license recorded in 0011, provenance on fork, trust model ADR, V2-first). The review's P0s were about code.

## 0014 adapter vs actual types

| 0014 | Actual on `puck/base` |
| --- | --- |
| `run_until(cycle deadline)` | `Machine::run(max_insns: u64)` |
| `TimingBlocked` | nothing; unknown devices tick after 256 |
| pending insn across calls | block `resume` tuple is a fast-path cache, not a priced transaction |
| capabilities | public fields on `Machine` |
| `NetworkPolicy::None` | wifi off, NAT default `nat` if wifi on |
| single core | both cores always scheduled |
| CCOUNT = wrapping projection of virtual cycles | CCOUNT = insn count |
| opaque hash-pinned identity | CLI text overlays into `init_regs` |
| product board Waveshare AMOLED 1.8 | `atech14` / `waveshare-lcd4b` / `waveshare-cam` / `none` |
| product boot = real ROM plus full flash | CLI default `--boot app` |
| wasm is a later bridge | today's C ABI is the browser product surface |

## What is actually strong

The hardware receipt parser is fail-closed on extra keys, dirty git, sample count < 100, CCOUNT wrap, mixed cache-counter presence, ibus misses not exceeding accesses, dbus flash+psram misses not exceeding accesses and two-boot cohorts (`timing/calibration.ts`). Beqz, MMIO read, write-slope intercept, ROM callback, XTOS and RTC-exclusion adoption tests reparse receipts and recompute the integers. Changing receipt cycles without editing those tests fails.

IDF 6.1 `SHA256SUMS` is intact. Burst OLS matches the committed analysis. D-cache analysis correctly dropped incomplete boot-1. The execution scheduler has no default chip costs (`execution.ts:269-271`, `cache.ts:330`, `machine.ts:734-735`). Address resolution is atomic (`address-map.ts:320-324`). MMU demands all 512 entries and no reset maps (`mmu.ts:292-295`). I-cache is per-core, D-cache is shared (`cache.ts:19-22`). Burst restart on DMA is tested (`execution.test.ts:209-223`) and `sequenceId` makes cache SRAM breaks visible to MSPI. Runtime traces are labeled `cycleAccurate: false` and `countsOnlyInstrumentedEvents: true`. `runtime-report` refuses MMIO with an unknown rather than the flattened 3 (`runtime-report.ts:245`). Full-ELF timing replay (when it could run) refused unknown MMIO/ROM (14 unknown MMIO, 20 unknown ROM durations) and did not emit a total.

That culture is real. It is also why the schema-1 scalar 3, the `costCalibration: "calibrated"` SRAM fixture and the STATUS headlines without exploded receipts are worse here than they would be in a sloppy repo: they violate this project's own rule.

The two-mode split is the right engine shape. Fast mode must stay bit-identical. Measured mode must be a parallel interpreter `run_until`, not a tax on `run_block` / `jit::run` / `Machine::run`. 0014's cut list is still the right instinct. Do not implement the lane-b draft.

QEMU carries no QEMU source or GPL implementation. Browser-speed README is careful that the JIT probe is a ceiling, not a JIT. Decision 0009 (real ROM) is implemented upstream. V2-first / CO5300-class / unnamed touch controller is the right board order, even though A-01 leaked a family name.

## What actually fails first if CORE starts tomorrow

Not "no `backend-api` crate." That is a `Cargo.toml` line. The spike already sketched it.

**First process stop.** Agent invents schema 2 in the fork so the importer can reject schema 1. Coordinator rule: timing vocabulary needs a decision before merge. META FREEZE: no new decisions until the adapter executes the first instruction. First instruction needs the importer. Park. Overnight report. No code.

**First engineering stop if they ignore process and type the crate anyway.**

- Differential gate and "flexe boot replay" exit criteria (`lanes/CORE.md`) import `packs/esp32-s3-touch-amoled-18/timing/...`. Those paths do not exist in this repo. This repo has no `package.json` to run the donor tests. Archive `timing/README.md` says not to run those commands here.
- Shared traces need the TinyDraw ELF. Hashes are in `STATUS.md`. Bytes are on the maintainer's disk.
- Even with ELF and retargeted imports, the gate compares a two-core schema-1 machine that lost the affine intercept and never modeled window/loop costs against a single-core measured interpreter that CORE.md requires to implement those costs. The ledgers will not match. 0014 says that comparison is the first correctness claim.

**First silent wrongness if they skip the gate.** Implement `advance_ccount(1)` with a schema-1 scalar 3 for MMIO writes. Totals will be `3n` not `3n-8`. 0014 already called that a reject. Unknown first-line cache will either block every flash/PSRAM kernel (honest, no totals) or get a guessed 204 that IDF 6.1 already shifted by one (`toolchain-delta.json`). Fail-closed means the first real firmware trace never produces a complete ledger. That is correct behavior and looks like a bug to anyone reading the README.

**Unsafe CORE shapes, named so they can be rejected on sight:** wrapping `SocBus`; instrumenting JIT helpers only; driving measured mode through `esp32sim_run`; copying the lane-b draft types; taxing `run_block` / `jit::run` / `Machine::run` (that violates "fast mode never taxed"); putting 0014 types onto `Machine` in place.

**Safe CORE shape:** new crate `backend-api` plus fake backend; new measured modules beside, not inside, `run_block` / `jit::run` / `Machine::run`; interpreter only, core 0, wifi unset, JIT off, `--boot rom` with a hash-pinned ROM, no NAT; device models that cannot name a deadline refuse with `TimingBlocked`; schema 2 as 0014-authorized implementation, not a new decision; donor comparison on refused names and unknown sets, not schema-1 totals.

## Residual test and coverage risk

- 164 of 170 timing tests pass. The six failures are the extraction path. Scheduler, cache, MMU, address-map, calibration parser and most adoption tests were not shown failing.
- No test in this repo parses the IDF 6.1 recovered-receipt tarballs. 802 / 210 / 204 is paper arithmetic plus `toolchain-delta.json`.
- No cache-burst adoption test. Profile first/subsequent line costs can drift.
- Window 35 and loop +1 have no named kernel in exploded receipts.
- 1.000 issue is derived from an unreviewed candidate, not machine-checked.
- Flexe, browser-speed and live QEMU were not executed. They cannot run here without puck, ELFs, Zig, objdump and (for QEMU) a pinned binary.
- Fork `cargo test` was not run in this pass. Sibling fork review: 13 pass, objdump diffs skip.
- Pages workflow, wasm ABI and JIT fast path were inspected, not executed in a browser.
- JSON.parse duplicate keys collapse; `exactKeys` then sees one key. Receipt hash is over raw text, so a duplicate-key file would not match a pinned hash unless the pin was updated. Not exploited. Residual.

## Claims vs receipts (STATUS headlines)

| STATUS headline | Receipt / path | Verdict |
| --- | --- | --- |
| 35-cycle window pair | Only `toolchain-delta.json:36`. No named kernel | Unverified in this tree. Summary field, not a two-boot receipt identity |
| 1.000 cycles per straight-line instruction | Narrative in `timing/evidence/README.md:42-47`. Profile cites unreviewed `bf169bc-counters-candidate.json`. `sram_instruction_issue_single_core` is 9234 cycles exact over 3 boots | Derived, not machine-checked. Candidate is explicitly `not-claimed` |
| +1 loop alignment at +3 mod 4 | Only `toolchain-delta.json:38` | Unverified in this tree |
| MMIO affine `3n - 8` | `e8a9f0e-mmio-write-adoption.json:95-100` plus 20 receipts under `mmio-slope-e8a9f0e/`. Tests recompute slope and intercept | Evidence holds. Profile stores scalar 3. Schema 1 rejected for totals (0014) |
| IDF 6.1 IRQ 227/143 and 222/139 | `toolchain-delta.json:47-64` | Delta file only. Not adopted into the profile |
| 802 / 210 / 204 | README plus `toolchain-delta.json:19-33`. Tarballs pinned by `SHA256SUMS` (verified this pass) | Counts are consistent on paper. Not enforced by tests. Tarballs not parsed here |
| First-line cache probe shift blocked | `toolchain-delta.json:74-84` `adopted: false`. STATUS agrees | Delta says blocked. `timing.json` still marks first-line calibrated at v6.0.2 204/115/82 |
| Six identities below strict recovery | Named in README and `toolchain-delta.json:24-33` | Named. Recovery of the other 204 is not re-checked in this repo |
| Fixture ELF SHA-256s | Hashes in `STATUS.md:72-77` | Hash-as-receipt is OK. Files are maintainer-local |
| Chip identity captured and accepted | Points at request A-01 | Not a receipt |
| JTAG 8,000-step lock-step | Markdown receipt E-01 with hashes | Raw bundle is a laptop path, not in this repository |
| 109 MIPS / about 4,600 JIT ceiling | Table in browser-speed README. Chrome JSON is 105.0 / 4393 | Table vs confirmation run. SPEED/0010 quote the table |
| Panel 40 MHz `frequencyCalibrated: true` | `timing.json:209-214` | No evidence field on `panel` |
| `puck/base` rustfmt-clean `3051793`; `lane-g/*`, `lane-a/*` SHAs | `STATUS.md:31-33,54` | Fork-only; nothing in this tree |

## Experiments vs README claims

| Claim | What tests actually assert |
| --- | --- |
| Flexe wasm executes real Xtensa in puck's loader | `puck-loader-test.ts` imports missing `../../src/wasm`. Cannot run. |
| Full ELF reaches 940-step INTLEVEL refuse | `full-elf-runner-test.ts:838` would, if loader plus ELF existed. |
| RGB565 replay completes at 451 cycles | Test pins 655 in `esp32s3-timing-replay-baseline.json`. README is stale. |
| Replay is not cycle-accurate | Test sets `cycleAccurate: false` (`timing-replay-test.ts:492-498`). That part matches. |
| Adjacent PIE encodings fail closed | Tests pass an explicit unsupported marker. Decoder fallthrough is untested. |
| ISA inventory 65,896 rows / 12 gap mnemonics | `isa-inventory-test.ts` pins hashes and baseline JSON. Needs local ELF plus objdump plus flexe. |
| QEMU is an external oracle | Default test compares corpus to a copied fixture. Live is skippable. |
| JIT-first real time is feasible at 8-10x 480 MIPS | No test. JSON receipts are 4.4k-8.6k MIPS on one machine one day. Ceiling caveat in README is careful. |
| Browser page never uploads the ELF | True (File API). `core.test.ts` only checks control parsing. Bundle would fail here on `../../src/wasm`. |
| Freestanding has no WASI | Would be asserted after a successful build. Cannot start. |
| Full-ELF timing replay makes no total cycle claim | Would assert `machine.status === "blocked"`, 14 unknown MMIO, 20 unknown ROM durations (`full-elf-timing-replay-test.ts:440-476`). Matches the README's "no total" for the boot trace. The RGB565 README total does not. |

## Docs consistency extras (not already numbered)

`roadmap.md:12-13` glosses 0013 as "puck is the donor, evidence, and decision repository." 0013:25-27 still says that in the decision body. The 2026-08-31 amendment at 0013:36-38 and `README.md:39-45` move the journal here and freeze puck. An agent that reads the 0013/roadmap sentence and skips the amendment writes in the archive.

`0013:25-27` "Puck is the donor, evidence, and decision repository" is the original decision text, retained for the record after the amendment. Living onboarding docs should not quote it as current.

META FREEZE vs docs that still grow meta: `CORE.md:57-59,72` and `roadmap.md:68` say phase 2 dual-core policy is "proposed as a decision record." That is after phase 1, so after freeze if the first instruction lands. It still reads as "write 0015." `AGENTS.md:34-36` amendment-first for cut material is the allowed exception, not a freeze hole, if parked for the maintainer (`COORDINATOR.md:84`).

`timing/README.md` is archive-bannered and still describes "Puck's default portable-C emulator" and `bun run pack:esp32:timing:test`. CORE.md:7-8 points at it as background.

## Readiness for CORE (fork)

Safe to start CORE on `puck/base` if the agent treats this tree as an unmodified fast engine and believes 0014, not 0011's graft story:

- New crate `backend-api` plus fake backend.
- New measured modules beside, not inside, `run_block` / `jit::run` / `Machine::run`.
- Interpreter only, core 0, wifi unset, JIT off, `--boot rom` with a hash-pinned ROM, no NAT.
- Device models that cannot name a deadline refuse with `TimingBlocked`.
- Schema 2 as 0014-authorized implementation, not a new decision.
- Donor comparison on refused names and unknown sets, not schema-1 totals.

0011's "graft points already exist" is half true (`advance_ccount` takes a delta) and half false (`Bus` is not a complete memory oracle; blocks have no cost fields). Believe 0014.
