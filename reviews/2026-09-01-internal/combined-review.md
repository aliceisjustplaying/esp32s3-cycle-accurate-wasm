# Combined internal review

This document mechanically combines `reviewer-one.md`, `reviewer-two.md`, and `reviewer-three.md` after fact-checking their findings against the cited repository snapshots. Invalid findings are omitted. Partially supported findings retain their original text and carry an editorial fact-check note; all other retained source text is unchanged.

---
<!-- BEGIN reviewer-one.md -->

# Combined thermonuclear code review and adversarial architecture review

**Review snapshot:** 2026-09-01
**Reviewer:** Grok 4.6, internal pass labeled reviewer-one
**Workspace:** `esp32s3-cycle-accurate-wasm` on `main` (HEAD at review time: `96e4bb8`)
**Product fork inspected:** `/Users/sarah/src/a/esp32sim` on `puck/base` at `3051793` (rustfmt; pin history `2114ffc` upstream, then provenance, then program-office pointer, then rustfmt)
**Related local trees read, not modified:** `/Users/sarah/src/a/puck`, `/Users/sarah/src/a/tinydraw`, `/Users/sarah/src/a/esp32s3-lane-b/esp32sim` (design-spike leftovers), `/Users/sarah/src/a/esp32s3-lane-e` (E-01 raw path cited)
**Prior review this follows:** [`../2026-08-31-external/puck-esp32s3-adversarial-review-2026-08-31.md`](../2026-08-31-external/puck-esp32s3-adversarial-review-2026-08-31.md) and [`../2026-08-31-external/RESPONSE.md`](../2026-08-31-external/RESPONSE.md)
**Sibling slices in this directory:** [`timing-lab-review.md`](timing-lab-review.md), [`fork-rust-review.md`](fork-rust-review.md). This file is the combined pass: code quality of this repo's executable surface plus architecture of the four-repo program.

> **Fact-check note: partially supported.** The original count predates filtering. With finding 35 removed, the retained distribution is 8 P0, 22 P1, 26 P2, and 11 P3.

**Finding distribution:** 8 P0, 22 P1, 27 P2, 11 P3.

## Executive decision

> **Fact-check note: partially supported.** The detailed finding qualifications below govern this synthesis, especially the schema-2/freeze claim: the schema has no concrete wire file or Rust type, but decisions 0008 and 0014 authorize its implementation and META FREEZE does not prohibit implementation files.

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

> **Fact-check note: partially supported.** The profile structurally distinguishes first-line and subsequent-line cycle values, but applies one calibrated status to both; only the first-line class is stale and blocked by decision 0014.

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

> **Fact-check note: partially supported.** No concrete schema-2 wire file or Rust type is specified, but decisions 0008 and 0014 already authorize its tier semantics and implementation; META FREEZE blocks new decisions, not implementation of the accepted schema.

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

> **Fact-check note: partially supported.** The headline summary and receipt archives are SHA-256 pinned in this tree, but several headline values are not independently derived from named kernels by any checked-in test or exploded receipt.

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

> **Fact-check note: partially supported.** The code waits only stores and fences on the one-entry buffer; this is a real gap for atomics and ordering-sensitive accesses, but not every load or cache fill is proven to require a blanket drain.

`timing/execution.ts:478-480`: `waitsForStoreBuffer` is `store || fence` only. A same-core load issues while the prior store is still draining. Tests lock CPU-overlaps-drain (`trace-adapter.test.ts:424-434`) and never ask a load to wait.

Opt-in buffering also demands exactly one cache `store` emission (`machine.ts:851-907`). Line-split stores, dirty writeback, write-through and MMIO throw instead of a named 0008 unknown. ESP32-S3 D-cache is write-back allocate. A buffered store miss that evicts dirty, or a word that straddles a line, aborts the whole run. `machine.test.ts` has no `storeBuffer` cases.

### 14. Dual-core is first-class here. Measured v1 is core 0 only

`machine.ts:54-57` requires two core streams. Default tie-break is core 0 wins (`execution.ts:542-552`, `machine.test.ts:654-677`). Decision 0014: capability absence is a typed refusal. A shared dual-core trace is a false fail (Rust refuses, TypeScript runs) or a false pass (compare only core 0 and ignore TypeScript core-1 MSPI contention).

TinyDraw already runs both cores on esp32sim (`experiments/esp32sim-adoption/README.md`: both cores, FreeRTOS, dual-core idle loop). BOARD demo of the real image is dual-core fast mode with 64-instruction quanta.

### 15. Default issue order is not program order

Omitted `issueOrder` dumps all memory, then all CPU, then fences, then DMA (`machine.ts:68-69,558-576`). The runtime recorder always fills `issueOrder` (`runtime-trace.ts:343-350`). A gate that calls `runTimingMachine` directly without it serializes execute after the entire memory stream.

### 16. CCOUNT is unbounded bigint. 0014 wants a wrapping u32 projection

> **Fact-check note: partially supported.** The TypeScript machine has unbounded bigint virtual clocks and no modeled CCOUNT register; the defect is absence of the required wrapping u32 projection, not an implemented unbounded CCOUNT.

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

> **Fact-check note: partially supported.** The default checked-in oracle test is a fixture self-check, but an optional configured live path does invoke QEMU and GDB; the live path still inherits the review's width, step-count, and pinning limitations.

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

> **Fact-check note: partially supported.** No BOARD TinyDraw demo exists on puck/base yet; the unhardened ABI risk becomes a current trust-boundary defect only if a future public demo accepts visitor-controlled artifacts or otherwise treats untrusted firmware as input.

Decision 0012: untrusted always includes firmware images, Wasm modules and anything a browser visitor uploads. The public gallery and any path that executes third-party material get the full boundary treatment. Hardening is SHIP, which waits for CORE's validator seam, and SHIP is not dispatched.

RESPONSE.md declined pausing implementation for the foundation gate. Gate 0 (license note, provenance file, trust ADR, V2-first) is "complete." The P0 validator/WASI findings are not in the fork. A BOARD browser demo of real TinyDraw is firmware on `esp32sim_run`. Safe only if that demo is never public. `pages.yml` publishes `web/` on push to `main`. Public by design.

`parseXtensaElf32WithDigest` copies the whole file with no byte cap (`elf-image-core.ts:51-121`). A huge ELF can OOM a tab before the runner's 768-page check. Browser `MAX_STEPS = 1024` (`browser/core.ts:6`). Server is `127.0.0.1` only (`browser-server.ts:21`). Decision 0012 treats local own-firmware as out of threat scope. Still no size quota. `Bun.serve({ development: true })` (`browser-server.ts:23`). No CSP.

## P2

### 31. Silent "ready at 0, calibrated" when `earliest` is omitted

`timing/execution.ts:506-507`: `earliestCalibration` defaults to `"calibrated"`. Missing readiness is not unknown. Mixed traces can look calibrated because the scheduler filled in a calibrated zero.

### 32. Cache tags physical offsets, not window virtual addresses

> **Fact-check note: partially supported.** The model demonstrably collapses virtual aliases onto physical-offset cache tags, but the cited local evidence does not prove the ESP32-S3's alias/tag behavior; treat the hardware mismatch as an unverified architectural risk pending a TRM-backed or silicon alias test.

`address-map.ts:401-408`, `mmu.ts:462-471`, `cache.ts:470-476`. ESP32-S3 MMU sits behind the cache: miss fill uses physical, tags are in the IROM/DROM virtual windows. Set index bits sit inside the 64 KiB page so identity-free injective maps hit the same set. Aliases (two DROM pages, one physical) collapse to one line here and would not on a virtually tagged cache. Remap without invalidate would miss here and hit in hardware.

### 33. Shared D-cache bursts are still per-core clients

`cache.ts:598-623`. `clientId` is `cache:${core}:${cache}:${memory}` and continuation requires `previous.core === core`. Sequential shared-D misses from both cores always pay first-line again. Fine for v1 single-core. Poison for any dual-core miss burst comparison.

DMA on MSPI correctly nulls `previousMspiBurst` (`execution.ts:730-732` plus `mspiBurstFor(dma) === null`), matching the documented "intervening DMA restarts the fill cost."

### 34. `literal-load` to MMIO would be classified as a write

> **Fact-check note: partially supported.** The branch is incorrectly typed for literal loads, but it is unreachable while MMIO is non-executable; this is a latent invariant-dependent defect, not a current misclassified access.

`address-map.ts:418-424`: `operation` is read only if `kind === "load"`. `literal-load` falls through to write. Unreachable today because MMIO cannot be executable (`address-map.ts:269-272`) and literal-load uses execute permission. Still a type lie if that guard moves.

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

> **Fact-check note: partially supported.** The archived receipts contain no pass field; the README appears to refer to an external parser report with pass: true, but that report or command is not checked in, so the claim is locally unverifiable rather than a receipt-schema conflict.

`parseHardwareCalibrationReceipt` uses `exactKeys` on a fixed key set (`calibration.ts:496-510`) and has no `pass` field. Extra keys fail. Either the tarball JSON is a different schema, or "pass: true" is tinydraw assembler language (same phrase in `mmio-slope-e8a9f0e/README.md:21`). Unverified: tarballs were not extracted in this pass.

### 41. Hot-hit two-boot is weaker than the cohort helper

`hot-hit-adoption.test.ts` checks 16 receipts and 2 per kernel, but does not call `aggregateCalibrationCohort` and does not require distinct `bootId`s per kernel. Beqz / MMIO / ROM tests do check the boot-id set.

### 42. `sdkconfig.path` in real receipts is a machine-local tmp path

> **Fact-check note: partially supported.** Real receipts contain machine-local sdkconfig paths, but integrity is pinned by sdkconfig.sha256; this is a provenance-clarity issue, not a broken receipt.

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

> **Fact-check note: partially supported.** “Cloud-viable” describes technical independence from local hardware, while the coordinator still mandates local execution; the documents are compatible but the terminology can mislead.

`lanes/COORDINATOR.md:14-16`: no cloud agents; all agents run locally; the board is attached overnight. `roadmap.md:114-130` and `BOARD.md:8` still describe cloud-viable lanes (BOARD modeling yes, captures no; CORE fully cloud-viable). Not a contradiction if "cloud-viable" means "could run without the board." It will confuse a coordinator who reads both.

### 48. Overnight report does not exist

`COORDINATOR.md:74-77` requires a dated "Overnight report" section on `STATUS.md`. `BOARD.md:57` names the board owner there. `STATUS.md` has none.

### 49. Broken relative links in accepted decisions

> **Fact-check note: partially supported.** The 0008, 0006, and 0011 paths are broken or stale; the 0012 and timing README hrefs resolve correctly and only their displayed text is stale.

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

> **Fact-check note: partially supported.** STATUS intentionally separates all open BOARD work from the narrower physical-hardware queue; the risk arises only if a coordinator treats the queue as the complete BOARD backlog.

`STATUS.md:82-89` queues only the logic-analyzer capture. STATUS lane-state (`STATUS.md:59-68`) and `BOARD.md:95-107` also list first-line diagnosis, six weak identities, PSRAM long-window, arbitration probes and CCOUNT lock-step. The queue is the serial-port rule. The open list is the work. An overnight coordinator who only reads the queue under-serves BOARD.

### 52. `puck/base` pin wording

> **Fact-check note: partially supported.** 2114ffc is the upstream base commit and 3051793 is the fork branch HEAD; the wording can be misread, but the pins do not conflict.

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

> **Fact-check note: partially supported.** The JSON support field is static mnemonic/objdump coverage and can be misread, but the experiment README explicitly disclaims execution coverage.

README says so (`experiments/esp32s3-flexe-wasm/README.md:144-147`). `supportedByFlexeDecoder` is still advertised in JSON as decoder support. `.byte` rows are excluded from markers (`isa-inventory.ts:414`) but counted in unsupported row totals. Inventory `elfSha256: "4e121a3642a6f18766cfe96c2be6adc8a0017fba4afa82105d642168ea40e2c8"` matches STATUS's gate-harness pin. Tests need local ELF plus objdump plus flexe.

### 57. Non-US spelling in accepted decisions

"behavioural" in `0006:8,22,70`, `0007:98`, `0011:84`. `AGENTS.md:78` requires US English. Load-bearing because these are the accepted records.

Stale verify commands in evidence READMEs still say `bun puck/timing/verify_calibration_receipt.ts` or `packs/esp32-s3-touch-amoled-18/timing/...`. That script does not exist in this repo. Parser coverage is `calibration.ts` plus tests.

`timing.json` `PROFILE_SOURCE` and several evidence READMEs are archive-bannered as puck-era. CORE is pointed at `timing/README.md` as background. Harmful because of that pointer.

## P3

### 58. `useSequence` / `lineFillSequence` never wrap

> **Fact-check note: partially supported.** The internal sequence counters do not wrap, but no current correctness defect follows; they are model bookkeeping IDs rather than exposed hardware counters.

Hardware LRU counters do. Irrelevant for short traces.

### 59. `atomic` and `cache-op` exist on the scheduler and are never produced by address-map, cache or runtime-trace

> **Fact-check note: partially supported.** atomic is currently unproduced by address-map/cache/runtime-trace adapters, but cache-op is produced by cache maintenance and is not dead surface.

Dead surface for the gate.

### 60. `timingMachineJson` stringifies bigints as base-10

> **Fact-check note: partially supported.** Base-10 bigint strings are the intentional, test-pinned TypeScript wire form; this is an integration requirement for Rust, not a current serializer bug.

`runtime-report.test.ts:34-37` already compares `"2"`. Rust JSON that emits numbers or hex will mismatch even when cycles agree. Do not `JSON.parse` into `number`.

### 61. Hit-after-fill always emits a hit

> **Fact-check note: partially supported.** A fill is followed by a hit emission, but current hit additive costs are zero and no defect is demonstrated; any future nonzero hit cost must define whether post-fill access service is additive.

`cache.ts:797-809`. Documented: the local hit emitted for a newly filled segment does not break the burst candidate. Harmless while hit additive is 0. If anyone puts a nonzero hit cost on a miss path, miss cost is fill plus hit.

### 62. F-031 line pins are stale after rustfmt

Update CORE.md / 0012 notes to `block.rs:235,314`, `jit/mod.rs:736-816`, `esp32s3/src/bus.rs:1097`. Do not hunt `bus.rs:622`.

### 63. `Bus::tick` returns 0 or 1, not a deadline

> **Fact-check note: partially supported.** SocBus::tick returns a 0/1 device-update flag, but that is its documented fast-mode contract; it must not be confused with decision 0014 deadlines, though the current method is not itself broken.

Name collision with 0014 device time.

### 64. Block `resume` is a cache cursor, not a pending instruction

It exists so quantum cuts do not fragment the arena (`docs/decisions.md:153-154` upstream). 0014's pending instruction is a priced transaction that persists across `run_until` calls.

### 65. Em dashes

None in current `decisions/`, `lanes/`, `STATUS.md`, `README.md`, `roadmap.md`, `AGENTS.md`, `timing/*.md`, `experiments/*/README.md`. Many in the frozen 2026-08-31 review archive. Project rule is "anywhere." Archive is the only hit in this tree.

### 66. Puck-era names leftover in experiments

Schema strings still say `puck/esp32s3-qemu-corpus@v1`. Temp dirs named `puck-flexe-wasm-`, `puck-esp32s3-qemu-`. `browser/index.html:12` still says "Puck experiment." Harmless.

### 67. Calibration fixtures still mint `espIdfVersion: "v6.0.2"`

> **Fact-check note: partially supported.** The v6.0.2 fixture values intentionally represent historical cohorts; the actionable risk is that the parser has no allowlist/version policy for future mixed-toolchain adoption.

Fine for historical receipts. Parser accepts any non-empty version string. Easy to mix v6.1 later.

### 68. SPEED 109 vs 105

> **Fact-check note: partially supported.** 109 MIPS is the original table run and 104.99 MIPS is a disclosed confirmation run; quote the exact receipt/run when precision matters, but the difference does not invalidate the performance conclusion.

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

> **Fact-check note: partially supported.** Rows 3 and 9 overstate schema 2 as a freeze deadlock. Decisions 0008 and 0014 authorize schema-2 implementation; the missing concrete wire/type specification remains a real implementation risk.

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

> **Fact-check note: partially supported.** The extraction and comparison hazards stand, but META FREEZE does not formally block implementing the schema already authorized by decisions 0008 and 0014.

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

<!-- END reviewer-one.md -->

---

<!-- BEGIN reviewer-two.md -->

# Internal review, 2026-09-01: thermo-nuclear code quality plus adversarial architecture

Commissioned by the maintainer. Method: one adversarial architecture
review of the whole project performed directly, plus three independent
deep code reviews (each verified against the repositories by actual
build, test, and typecheck runs, and the highest-impact claims
spot-checked by the lead reviewer):

- The esp32sim fork, puck/base at 3051793 (about 29k lines of Rust).
- This repository's timing/ lab (about 13,600 lines of TypeScript,
  source plus tests).
- The TinyDraw probe and reference firmware, its capture tooling, and
  the receipt consumer interface.

Session transcripts with full tool trails:

- Fork review:
  `~/.pi/agent/sessions/--Users-sarah-src-a-esp32s3-cycle-accurate-wasm--/2026-09-01T11-45-40-927Z_1f1e19fd-6376c44b-839ab3ff-abc3.jsonl`
- Timing lab review:
  `~/.pi/agent/sessions/--Users-sarah-src-a-esp32s3-cycle-accurate-wasm--/2026-09-01T11-45-40-668Z_1f2b55ff-73836adf-b873f9a9-59c4.jsonl`
- Tinydraw review:
  `~/.pi/agent/sessions/--Users-sarah-src-a-esp32s3-cycle-accurate-wasm--/2026-09-01T11-45-41-008Z_e66c9d9c-20ff5cda-0a961598-8d64.jsonl`

This is a review record only: dispositions belong to the maintainer,
and nothing here grows the meta (the meta freeze stands; items that
would need decision amendments are listed as parked, not decided).

---

# Part 1: adversarial architecture review

## A1. The one-shot differential gate is load-bearing and under-specified (highest severity)

> **Fact-check note: partially supported.** The gate's comparison domain and divergence policy are under-specified, but schema-1 is not limited exactly as stated: the neutral TS adapter can take explicit DMA, literal-load annotations, and opt-in store-buffer timing outside the narrower runtime artifact.

Decision 0014 makes the differential gate do three jobs at once: it is
CORE phase 1's exit criterion, the receipt that retires a 15k-line
TypeScript asset, and the first correctness claim measured mode makes.
Its mechanics are not specified anywhere:

- The shared trace format is undefined. The TS side only accepts
  schemaVersion 1 traces (`timing/trace-adapter.ts:54,344`), and
  schema 1 explicitly refuses flash, PSRAM, MMIO, DMA, literal loads,
  store buffers, and pipeline dependencies ("refused as schema drift").
  Measured mode's ledger will contain exactly those classes.
- The comparison domain is undefined. What is compared, on what
  intersection of the two ledgers, and what happens to cost classes
  only one side can express (see A2), is not written down.
- The gate has a fail-closed paradox: "no new TypeScript execution
  code is written for this gate" (decision 0014) plus the TS side's
  own schema-drift refusal means the gate can only run on the
  intersection of the two schemas, which per timing/README is
  SRAM-confined, zero-additive-cost traces. On those traces, what the
  comparison actually proves about measured mode's cache, MSPI, and
  MMIO accounting is unclear.

This is not a call to grow TypeScript. It is a call to define, before
CORE phase 1 execution, the comparison domain and the divergence
disposition. Status: parked under the meta freeze (it wants a decision
amendment or an addendum to 0014). The maintainer should decide the
disposition when CORE starts, not when the gate fails.

## A2. The schema-1/affine divergence is a known, committed inconsistency that the gate will trip over

> **Fact-check note: partially supported.** The 3n - 8 versus scalar-3 mismatch is real, but an eight-cycle divergence is conditional on the shared gate containing an affected same-value MMIO run and comparing that affine cell without normalization; no current decision guarantees such a trace.

Decision 0014 rejects schema-1 timing.json for measured totals because
it flattens the affine MMIO claim (committed evidence: slope 3,
intercept -8). The TS machine still encodes the adopted cost as the
flattened scalar 3 (`timing/timing.json`; derivation verified and then
flattened in `mmio-write-slope-adoption.test.ts:100-111`), and the TS
cost vocabulary is two-valued (calibrated/uncalibrated/unknown);
decision 0008's consequence that the cost types grow tier fields never
landed in this tree. Left undispositioned, the gate produces a
guaranteed 8-cycle-per-cell structural divergence that looks like a
scheduler bug. Remedy options (timing-lab finding 1.1 below), none of
which require new TS code: (a) shared traces exclude same-value MMIO
write runs, (b) the gate comparison normalizes per-access MMIO costs
on both sides, or (c) the gate receipt records this as a declared,
tier-attributable divergence rather than a scheduler disagreement.
Same parking status as A1.

## A3. Determinism of the canonical ledger is environment-dependent today

> **Fact-check note: partially supported.** The TS JSON ordering is locale-sensitive for unrestricted identifiers; the evidence does not show that the future Rust canonical-ledger hash itself uses these TS sort sites or that the current fixed fixtures already hash differently across hosts.

`machine.ts:958` and `calibration.ts:720,779` order canonical JSON
content with localeCompare, which is ICU-collation dependent. The
gate's receipt handle is one SHA-256 over canonical ledger bytes.
Until these become codepoint comparisons (the codebase already owns
two: lexicalCompare in execution.ts, compareText in
calibration-report.ts), any ledger hash is a property of the host's
ICU version. Small, behavior-preserving fix, no decision needed, land
before the gate.

## A4. The extraction from puck left the program office's own tooling red, contradicting the fail-closed rule

> **Fact-check note: partially supported.** The timing lab has no root/timing TypeScript project configuration and the extraction defects reproduce, but the repository does contain experiment-local tsconfig.json files; “the repository has no tsconfig” is too broad.

- 6 of 170 timing tests fail (`bun test` in timing/, independently
  re-verified by the lead reviewer): profile path resolution assumes
  the puck-era `packs/esp32-s3-touch-amoled-18/` layout. Failing
  receipts: `timing/consumer.test.ts:253`,
  `timing/rtc-boot-read-exclusion.test.ts:8`,
  `timing/xtos-intlevel-adoption.test.ts:13`,
  `timing/runtime-report.ts:251`.
- `timing/report.ts:4` imports `../../../src/wasm`, which does not
  exist here; the shadow-ledger CLI is dead code in this checkout.
- timing/README.md instructs `bun run packs/.../report.ts` and
  `bun run pack:esp32:timing:test`; no package.json exists, the packs
  path is gone, so the documented commands cannot work.
- The repository has no package.json, no tsconfig, no CI, so nothing
  runs typecheck and the 9 existing strict-mode type errors in test
  files are invisible.

AGENTS.md says missing corpora fail tests rather than skipping. Right
now the lab fails tests for a boring reason (a moved file) and
documents commands that cannot run. These are extraction defects, days
of work, and they gate everything downstream: the differential gate
needs the TS side runnable.

## A5. Receipt reproducibility has single-machine chokepoints that are recorded and unrecorded

> **Fact-check note: partially supported.** The rebuild and verifier gaps are real, but commit provenance is committed: the rebaseline README binds the timing ELF to 3db39856, and STATUS records the other persistent fixture hashes under the same TinyDraw commit.

Recorded (fine): fixture ELFs are machine-local to the maintainer
(PROVENANCE.md, roadmap). Unrecorded and worse (tinydraw review F2):
the pinned v6.1 timing-probe source is stranded on an unmerged branch
(`3db39856` on codex/lane-0-idf61-probes), the fixture worktree sits
at the pinned commit's parent and does not build under v6.1, and no
committed record says which commit each pinned ELF came from. The
probe's "verified from the ELF by the capture tooling, not assumed"
claim has no committed verifier at all (tinydraw review F1). For a
project whose currency is receipts, the receipt-producing chain should
be rebuildable from the canonical checkouts, and every embedded
verification claim should name committed tooling.

## A6. Roadmap and STATUS prose states present-tense facts that are aspirational

> **Fact-check note: partially supported.** The dependency lint is absent and there is no test/format CI, but the fork does have GitHub CI: its Pages workflow builds the wasm module and deploys the web tree.

- Roadmap: "dependency lint enforces it" (no imports of esp32sim
  internals outside the adapter). The fork's Cargo.toml has no lint or
  deny configuration, and wasm/src/lib.rs is upstream's raw C ABI with
  web/ importing it directly. The rule is a target state, not a
  current one; the text should say so or the lint should exist.
- Roadmap: "GitHub CI" material waits on fork branches (accurate, but
  note the fork currently has zero CI, so the only thing keeping
  puck/base green is manual discipline; the vacuous objdump pass shows
  manual is not enough, see fork review finding 7).

## A7. Contract tensions in decision 0014 that CORE will hit, stated now so they are found now

> **Fact-check note: partially supported.** The pending-instruction/device-event tension is grounded in decision 0014, but current esp32sim does not store per-block base timing costs in BlockCache; that part describes a proposed design risk, not present code.

- Deferred completion-phase access requires an instruction's full cost
  and access shape to resolve before it starts. Phase 1 is
  single-core and networking-off, but DMA is not excluded. A device
  event landing inside a pending instruction's window can change the
  instruction's cost (MSPI contention) after resolution. 0014's
  same-cycle event order handles delivery, but the interaction between
  pending instructions and device completion mid-instruction is the
  hardest unspecified case; phase 2's arbitration decision will need
  it, and the answer may constrain phase 1's scheduler shape.
- Both modes share one engine and per-block base costs live in the
  block cache. The cache is shared and versioned; measured-mode
  annotations must not tax fast mode or interact badly with
  self-modifying-code invalidation. The fork review's HostHooks/seam
  recommendation (fork finding 1) and the existing page_ver scheme are
  the natural homes; the design should be explicit that measured costs
  ride the interpreter loop, not the shared block cache.

## A8. Process architecture: sound, with two sharp edges

The four-lane structure, strict dispatch, one-agent-per-clone, the
overnight park-don't-guess rule, and the receipts culture are unusually
good project architecture. Sharp edges:

- The meta freeze blocks exactly the decision amendments this review
  shows are needed (A1, A2, tinydraw F7's rerun semantics). That is
  the freeze working as intended, but the maintainer should treat the
  list in this review as the queue of amendments to make the moment
  measured mode executes its first instruction.
- The fork carries a rustfmt flag-day commit against unformatted
  upstream code; every upstream sync will diff against 21k lines of
  formatting noise. Acceptable while upstream is quiescent (verified:
  0 commits between the pin 2114ffc and origin/main at review time);
  it becomes a real cost the day upstream moves.

## A9. Open follow-through from the prior external review

F-001's disposition: "the author will be asked to add the root LICENSE
file." The fork (and upstream, at the pin) still has no root LICENSE
file. The disposition accepted the manifest declaration as the basis,
so this is not a blocker, but the follow-through has not happened and
the fork could carry its own LICENSE file if the author does not.

## Verified pin and branch state (lead reviewer receipts)

- `2114ffc..origin/main` is 0 commits: puck/base is current with
  upstream, not stale. The fork carries only 3 commits (provenance
  twice, rustfmt).
- `advance_ccount` checks CCOMPARE per delta
  (`xtensa-lx7/src/exec.rs:167-179`), so 0014's "computed against
  every CCOUNT delta" has a real hook.
- The run loop: `QUANTUM = 64` (`machine.rs:635`), idle-skip chunks of
  `QUANTUM * 8`, block-batched `advance_ccount` (`machine.rs:676-719`),
  confirming decision 0011's caveat that device-time delivery and
  CCOUNT batching are wrong for measured mode and must be deliberately
  tightened.

---

# Part 2: code quality review, the esp32sim fork

Reviewer: fork review agent. Standard: the thermonuclear code quality
review skill. Focus: soundness as the foundation for measured mode
(decision 0014).

## Build, test, format receipts (actual outcomes)

| Command | Result |
| --- | --- |
| `cargo check --workspace` | exit 0, 2 warnings (unused parens: `riscv-rv32/src/exec.rs:182`, `esp32s3/src/periph.rs:636,1156`) |
| `cargo test --workspace` | exit 0, 13 tests pass, 0 fail (10 crypto unit tests, 1 a64 encoder diff, 2 objdump decoder tests) |
| `cargo fmt --check` | exit 0, clean (matches STATUS.md's claim at 3051793) |

Caveat on the green run: both objdump differential tests report ok in
0.00s while executing zero instructions when `XTENSA_DIS_FILES` is
unset (`xtensa-lx7/tests/objdump_diff.rs:33-36`). That is a
fixture-gated pass, not coverage (finding 7).

## F-031 containment assessment (known finding, not re-reported)

The hazard is more contained than the original finding implies. The JIT
bypasses the Bus trait only for accesses resolved through
`SocBus::fast_mem()` (`esp32s3/src/bus.rs:1097-1101`), which returns
the software TLB covering RAM/flash/PSRAM buffers only. All MMIO goes
through the generated-code helpers `h_read8/16/32` and
`h_write8/16/32` (`xtensa-lx7/src/jit/mod.rs:141-175`), which call the
trait methods. Since the affine MMIO intercept hooks the trait path,
the JIT cannot silently lose MMIO timing. Residual risk: enforcement
of interpreter-only measured mode today is a public mutable bool
(`BlockCache::jit_enabled`, `xtensa-lx7/src/block.rs:92`) toggled by
the CLI (`cli/src/main.rs:273-275`). The backend-api adapter needs a
typed interpreter-only construction path, not a flag some caller
forgot to set.

## Findings, prioritized

### 1. machine.rs (1,425 lines) fuses orchestration, tracing, transport, and host pacing (structural, highest priority)

> **Fact-check note: partially supported.** The fused responsibilities and duplicated control paths are present, but the console-drain wrap test does not require QUANTUM to divide 65,536; for bounded increments it detects a 16-bit boundary crossing for any quantum.

The run loop (`machine.rs:634-755`) is reasonable: quantum scheduling,
dual-core, idle fast-forward, block versus single-step selection. But
the same type also contains:

- Hand-rolled RFC 6455 WebSocket frame serialization in the mk/mkb
  closures (`machine.rs:955-1000`) plus per-feature frame assembly
  (`machine.rs:851-950`).
- A PNG encoder with private crc32, adler, chunk (`machine.rs:1366-1420`)
  while `picture.rs` exists in the same crate.
- Real-time wall-clock pacing and resync logic (`machine.rs:790-835`).
- A script/action interpreter (`machine.rs:1176-1288`).

Decision 0014 puts the measured scheduler and CPU observation in this
crate and demands explicit stop precedence and slice invariance. A
CORE agent will either wedge measured-mode checkpoints into this loop
(spaghetti growth) or fork the loop and watch drift. Code judo:
extract a HostHooks/observer seam (console drain, web push, pacing,
scripts) so run() is purely architectural; measured mode gets a clean
sibling loop. Also: stub-return and fn-probe blocks are duplicated
nearly verbatim between step_blocks (`machine.rs:448-478`) and
step_core (`machine.rs:510-545`); the irq_dirty to INTTYPE_LEVEL merge
block appears three times (`machine.rs:489-494`, `:612-617`,
`:762-767`), one helper deletes all three; the console-drain trigger
`n & 0xffff < chunk` (`machine.rs:691,748`) silently requires QUANTUM
to divide 65536 and breaks without complaint if the quantum changes.

### 2. Stringly-typed errors throughout the library (boundary/type contract)

> **Fact-check note: partially supported.** Thirteen loader, image, bus-loading, and script signatures are stringly typed, but not every fallible library API is: CPU and bus execution already use Trap/Fault, and decision 0014 does not classify ordinary loader errors as decision-0008 timing refusals.

Every fallible library entry returns `Result<_, String>`:
`machine.rs:165,244,256,1176`, `bus.rs:933`, plus elf.rs and image.rs
(13 sites in the core crates). Decision 0014 requires typed refusals
(`TimingBlocked` carrying a decision-0008 tier candidate) and
fail-closed semantics; strings cannot express why. A blank-slate CORE
lane would have to invent a parallel error taxonomy and translate
lossily at every call. Remedy, upstream-shaped: a small error enum per
crate (loader, bus, boot). Per decision 0008 these are unexplained
refusals today.

### 3. periph.rs at 3,780 lines is a directory in waiting (decompose before CORE adds device deadlines)

The file is 22 clean peripheral models in one file (struct/impl list
at `periph.rs:45-3557`) plus the Peripherals dispatcher
(`:1747-2386`). The split is mechanical and behavior-preserving:
`periph/mod.rs` keeps the struct, the read32_inner/write32_inner
dispatch, and tick/cycles_until_timer/source_status; one file per
device. Decision 0014 requires every active time-aware device to
answer deadline, none, or typed unknown: a per-device trait method,
about 22 surgical insertions. Inside a 3,780-line file that is merge
conflict territory; across files it is routine.

Hand-maintained-table drift receipts inside this file: `block_name`
has a dead duplicate arm `0x3a => "AES"` twice (`periph.rs:1907,1914`),
and env-var configuration is read inside library constructors
(`ESP_EMU_FAKE_READ` at `periph.rs:1826`, `ESP_EMU_DEBUG_I2C` at
`i2c.rs:45`), which wasm cannot honor and the adapter cannot observe.

### 4. Clock divisors are magic constants that silently hard-fork on CPU_HZ

`periph.rs:2178-2190` (`/ 15`, `/ 3`, `/ 1600`) and
cycles_until_timer (`* 15`, `* 3` at `:2304,2325`) encode 240 MHz
derived clocks as bare literals, correct only under CPU_HZ =
240_000_000 (`periph.rs:34`); esp32c3 duplicates the pattern under 160
MHz (`esp32c3/src/periph.rs:15`). Named constants or const-fn
derivations remove the drift class. Separately, the RTC slow clock =
150 kHz value carries no receipt comment and was not verified against
the S3 TRM in this session; for a receipts culture, an unverified
oscillator frequency feeding the RTC WDT needs a TRM-cited comment or
correction before measured mode adopts RTC-slow timing.
`periph.rs:2190` divides a cumulative total by 1600, exact only
because st_done-style accumulators are kept; that subtlety deserves a
doc line since measured device deadlines lean on these accumulators.

### 5. GPIO register knowledge leaked into SocBus (wrong-layer logic)

`esp32s3/src/bus.rs:338-341`: the bus knows GPIO's output-register
window `0x6000_4004..=0x6000_4018` by raw address. This is the shape
decision 0014's MMIO hooks will multiply. Remedy: let Gpio::write (or
an `affects_lines(addr) -> bool` capability on the owning device)
answer the question so the bus stays address-agnostic.

### 6. Panic hygiene in library code fails open at the wasm boundary

> **Fact-check note: partially supported.** The panic and mmap-lifetime sites are present, but an uncaught Rust panic at the wasm/C-ABI boundary fails stop by trapping or aborting; it does not fail open.

About 60 unwrap/expect/panic sites outside tests. The ones that
matter: `xtensa-lx7/src/pie.rs:246` (`o.r[k].unwrap()`, a decode-table
bug becomes a host panic, not a Trap); `i2c.rs:328,423,497`, web.rs
(about 15 sites), `board.rs:460,562` (lock().unwrap() on
poisoning-prone mutexes; use `unwrap_or_else(|e| e.into_inner())`
because the emulated state is the authoritative copy);
`net.rs:150,155` (unwrap guarded by an is_some() arm two lines up).
The JIT's unwraps (`block.rs:183,240,316,320`) are preceded by
jit_active()/is_some() invariants and are acceptable. CodeCache has no
Drop, so its mmap leaks per Machine (`jit/mod.rs:65-73`); harmless for
one long-lived machine, worth a Drop before the browser can
create/destroy many instances.

### 7. Test coverage is thin and one gate passes vacuously

13 tests across about 29k lines. The objdump differential decoder
tests (the most valuable asset) pass with zero work when their fixture
env var is unset (`xtensa-lx7/tests/objdump_diff.rs:33-36`, same in
riscv-rv32). Under the program-office fail-closed rule, a missing
corpus should not be a green pass. Bigger gap for CORE: there is no
interpreter-versus-JIT differential test. Decision 0012 makes the
cross-mode conformance proof a precondition for JIT observation; the
harness (same trace, jit_enabled on versus off, compare architectural
state) is a roughly 100-line integration test and writing it now
de-risks a decision-record gate. Also absent: any test of SocBus MMU
or tick deferral, and any test exercising Machine::run. The crypto
suite (10 tests, RFC vectors) is genuinely good.

### 8. JIT unsafe code: acceptable scope, some lint debt

All unsafe is concentrated where it belongs: CodeCache mmap/W^X
(`jit/mod.rs:56-113`), the generated-code trampoline (`jit/mod.rs:1036`),
and TlbEntry's Send/Sync (`xtensa-lx7/src/bus.rs:45-46`, justified).
Cleanup receipts: 9 function_casts_as_integer warnings
(`jit/mod.rs:212-218`), dead field exit (`jit/mod.rs:259`), 3 unused
parens.

### 9. Style-consistency note (low)

> **Fact-check note: partially supported.** The style-convention question stands, but the current count is 37 em dashes in esp32s3 plus xtensa-lx7 source comments, not 35, and it is higher across the full Rust workspace.

35 em dashes in fork Rust comments; the ban's home is the
program-office rules, so this is a fork-convention question, not a
violation. Recorded so it is a decision, not an accident.

## What is genuinely good (and load-bearing for measured mode)

- The Bus trait is the right seam and well documented
  (`xtensa-lx7/src/bus.rs:60-117`): note_pc, block_break,
  page_versions, fast_mem, tick(cycles) give the measured observer its
  natural hooks without touching the JIT.
- `cycles_until_timer()` already computes exact, conservative-early
  device deadlines (`periph.rs:2276-2340`, "never late" stated in its
  doc comment); the measured scheduler should grow from it.
- `flush_ticks()` before every MMIO access ("registers must show exact
  time", `bus.rs:299-301,318-319`) is exactly the guest-visible-time
  exactness the measured contract needs, already enforced.
- `pie_table.rs` (7,718 lines) is generated by the checked-in
  `tools/gen_pie_table.py` with the TRM extract (`pie_trm.json`);
  explicitly waived from the 1k-line rule.
- The versioned write-counter cache-coherency scheme (page_ver,
  `bus.rs:66-71`) solves the real S3 hazard (IRAM/DRAM sharing a 4 KiB
  page) and is well documented.
- esp32c3 reuses esp32s3's elf and image loaders
  (`esp32c3/src/machine.rs:6-7`).

## Fork verdict

> **Fact-check note: partially supported.** The decomposition and differential-test recommendations stand. Typed-error work should be scoped to stringly loader, image, script, and future TimingBlocked boundaries; CPU and bus execution already use typed Trap and Fault errors.

Sound foundation, with three things that must change first, all cheap
relative to what they unblock:

1. Split esp32s3/src/periph.rs into periph/ (one file per device,
   dispatch in mod.rs) and extract host concerns out of machine.rs
   (web frames to web.rs, PNG to picture.rs, pacing and scripts behind
   a hook seam). Do this before backend-api exists.
2. Replace `Result<_, String>` with typed errors in the esp32s3 and
   xtensa-lx7 public APIs.
3. Write the interpreter-versus-JIT differential test and fix the
   vacuous-pass test gates; it is the seed of the decision-0012
   conformance proof.

Non-blocking in the same pass: derive clock divisors from named
constants, pull the GPIO window out of SocBus, sweep hot-path
unwraps. After 1 through 3, a blank-slate CORE agent lands on a
codebase whose seams already point where the measured scheduler goes,
and fast mode stays untaxed by construction.

---

# Part 3: code quality review, the timing lab

Reviewer: timing lab review agent. Standard: the thermonuclear code
quality review skill, plus the binding rules (AGENTS.md; decisions
0008, 0013, 0014). Scope: all 16 source modules (about 9,300 lines)
and all 23 test files (about 4,300 lines).

## Current state (receipts)

| Check | Command | Result |
| --- | --- | --- |
| Tests | `bun test` in timing/ (bun 1.4.0) | 164 pass, 6 fail (170 tests, 23 files); independently re-verified by the lead reviewer |
| Typecheck | `tsc --noEmit --strict` with @types/bun (no tsconfig exists; one was constructed for the review) | 9 errors in 5 test files (Set overload inference in the adoption tests) plus `report.ts(4,29): Cannot find module '../../../src/wasm'` |

The 6 failures are one defect: the extraction from the puck tree broke
the profile path (see A4 above).

What is genuinely good: the fail-closed discipline is real, not
decorative. Unknown costs produce blocked or started-unknown-duration
results that stop dependent work (execution.ts:269-shaped latency,
blocked clocks in the scheduler loop), the scheduler contains no
latency table ("supplies no fallback", execution.ts doc block), every
parser uses exact-key validation, everything is frozen, costs are
exact bigints or explicit unknowns, and no path was found where an
unexplained or guessed cost leaks into a result. calibration.ts and
the burst analyses correctly refuse to claim anything
(`microbenchmarkToArchitecturalCost: "unreviewed"`,
`costAdoption: "none"`).

## Severity 1: findings that undermine the one-shot differential gate

### 1.1 The TS ledger cannot carry the affine MMIO claim (3n - 8) or any decision-0008 tier

Detailed under A2. Additional receipts: the cost vocabulary in the
entire TS machine is two-valued, `execution.ts:2-3`
(`CalibrationStatus = "calibrated" | "uncalibrated"`,
`TimingCertainty = CalibrationStatus | "unknown"`); grep for tier,
affine, interval, distribution across non-test timing/*.ts: zero hits.
Decision 0008 said the timing.json and the timing machine's cost types
grow tier fields; that never happened in this tree. Remedy: record the
disposition before the gate runs; do not add an affine latency variant
to the TS machine (that is schema-2 work on the Rust side, and 0014
forbids new TS execution code). Discovering this during the gate and
improvising is not acceptable.

### 1.2 Non-deterministic canonical ordering via localeCompare

> **Fact-check note: partially supported.** The TS JSON ordering is locale-sensitive for unrestricted identifiers; the evidence does not show that the future Rust canonical-ledger hash itself uses these TS sort sites or that the current fixed fixtures already hash differently across hosts.

Detailed under A3. The repeatability test (`machine.test.ts:770-781`)
only proves same-process stability and cannot catch cross-environment
drift.

### 1.3 The checkout is red, and the replay path the gate needs is broken here

Detailed under A4. The gate replays shared traces through
runtime-report.ts and the trace adapter; today that path crashes on
profile load. Fix path resolution and either fix or explicitly retire
report.ts's dangling puck import. Extraction hygiene, not new TS
capability.

## Severity 2: structural findings

### 2.1 consumer.ts is 1,158 lines and half of it is a frozen-snapshot validator

parseTimingProfile (roughly consumer.ts:500-1050) asserts the
checked-in file leaf by leaf (`literal(...)` on every constant), and
the TimingProfileV1 type (`consumer.ts:47-175`) hardcodes the same
constants again as literal types: two full copies of the same frozen
numbers, in a file that also contains the 144-byte snapshot layout,
the WASM export decoder, and the panel scheduler report builder. The
freeze is defensible (the profile is a claim boundary); the file size
and four unrelated responsibilities are not. Code judo: extract
profile parsing into timing-profile.ts and derive the type's literal
fields from the parse result. Keep it mechanical; schema 2 replaces
this parser anyway, and the goal is a smaller donor, not a richer one.

### 2.2 Five drifted copies of the latency validator, seven copies of requireNonEmpty

> **Fact-check note: partially supported.** The validator drift is real, but the helper count is six functions literally named requireNonEmpty, plus two equivalent stringAt spellings; “seven copies” is not the current count.

`machine.ts:210` (validateMachineCost), `cache.ts:223` (validateCost),
`execution.ts:269` (validateLatency), `address-map.ts:186`
(validateLatency), `trace-adapter.ts:95` (validateLatency) all
validate the same known/unknown cost shape, and they have drifted: for
an unknown latency, `trace-adapter.ts:113` requires source,
`execution.ts:281` makes it optional, `address-map.ts:198` ignores it,
`machine.ts:210` requires it via an unconditional requireNonEmpty at
the top. A caller-supplied unknown cost without source is accepted by
the scheduler and refused by the adapter: which validator is the
contract? requireNonEmpty is defined in six files
(`machine.ts:203`, `cache.ts:199`, `execution.ts:236`,
`address-map.ts:130`, `trace-adapter.ts:73`, `runtime-trace.ts:93`)
with stringAt in `consumer.ts:303` and `calibration.ts:224` as two
more spellings; objectAt/exactKeys/literal repeat in
`runtime-report.ts:27-45`, `consumer.ts:261-276`, `calibration.ts:201-216`.
One internal strict-parse module holding the latency validator, the
comparator, and the key-exactness helpers deletes roughly 200 lines
and leaves one answer to "what is a valid unknown cost?".

### 2.3 The line-fill cost class is decided twice, and one answer goes stale

- `cache.ts` #lineFillCost (around `cache.ts:560-590`): the cache
  tracks #previousLineFill and decides first-line versus
  subsequent-line at emit time, assigning sequenceIds; it cannot see
  DMA, MMIO, or other MSPI clients.
- `execution.ts` serviceLatency (around `execution.ts:570-585`)
  independently re-derives first-versus-subsequent at service time on
  the shared MSPI clock, where intervening DMA correctly restarts the
  fill cost.
- `machine.ts` applyServiceLatencies (`machine.ts:130-155`) then
  overwrites the emitted event's latency with the scheduler's answer.

So CacheEmission.cost and CacheStep.claim can carry a cost class the
scheduler later contradicts, and the retained cacheSteps keep the
stale copy while claim.costProvenance has the serviced one. Two
sources of truth for one claim, reconciled by a silent post-pass.
Cleaner model: the cache layer owns candidate classification only (it
already emits mspiBurst with sequenceId, line address, and both
candidate latencies) and stops attaching a resolved per-fill cost it
cannot finalize; the serviced cost and provenance come from the
execution result alone. That deletes the reconciliation pass and the
stale shadow copy.

### 2.4 icache-burst-analysis.ts and dcache-burst-analysis.ts are one analysis written twice

About 333 plus 353 lines with near-identical skeletons: gcd, rational,
cohortKey, bootKey, medianCycles, buildSeries including the identical
fixed-first-line least-squares fit, the boot/cell validation loop, and
the pooled/per-boot orchestration repeat; only the kernel regex and
counter-signature validation differ. One burst-analysis core
parameterized by cell descriptor removes roughly 300 lines and
guarantees the two estimators cannot drift.

## Severity 3: smaller but real

> **Fact-check note: partially supported.** The validation-by-side-effect calls are redundant and obscure intent, but each schedules a one-element array; together they add linear work, not quadratic work.

- Validation by scheduling side effect: `machine.ts:520,539` call
  scheduleExecution on single-event arrays purely to validate,
  discarding the result; `runtime-trace.ts:144,176,194` do the same.
  O(n) per event, O(n-squared) total, run before the full
  scheduleExecution validates everything again. A plain exported
  validateEvent states the intent.
- O(n-squared) lookup under the 65,536-record ceiling:
  `runtime-trace.ts:442,460` call find() inside the per-observation
  validation loop; at MAX_TRACE_RECORDS = 65,536
  (`runtime-report.ts:23`) that is on the order of 2 billion
  comparisons for a large trace. Build one id-to-event map, as
  machine.ts already does.
- machine.ts is at 994 lines with a roughly 500-line runTimingMachine
  body misindented by two extra spaces from `machine.ts:750` onward;
  validateInput (about 440 lines) is the obvious extraction if the
  file is touched. One review-relevant edit from crossing 1,000 lines
  unexamined.
- `runtime-report.ts:21` PROFILE_SOURCE and both CLIs' usage strings
  cite packs/esp32-s3-touch-amoled-18 paths that do not exist here;
  provenance strings printed into reports should name the real
  artifact path, or the gate receipt will contain a dead reference.

## Timing lab verdict

> **Fact-check note: partially supported.** The portability concern stands, but locale-sensitive TS sorting is not evidence that the future Rust ledger hash or current fixed fixtures already diverge across hosts.

Conditional approval as gate donor, with 1.1, 1.2, and 1.3 as
presumptive blockers. The engineering culture in this directory
(refuse-by-default, exact arithmetic, hashed provenance) is exactly
right, and the three gate-level defects are all fixable in days
without growing the TypeScript machine. Recommended order within the
no-new-TS-execution-code constraint: (1) fix extraction path defects
and decide report.ts's fate; (2) swap localeCompare for the canonical
codepoint comparators; (3) record the schema-1/affine divergence
disposition before gate execution; (4) optionally, the shared latency
validator and comparator. Defer the consumer.ts split, the
burst-analysis dedup, and the machine.ts decomposition: the machine is
about to be retired as donor history, and the goal is a trustworthy
comparison, not a beautiful corpse.

---

# Part 4: code quality review, TinyDraw probe and reference firmware

Reviewer: tinydraw review agent. Standard: the thermonuclear code
quality review skill, scaled to embedded probe firmware.

Scope: the ESP32-S3 timing probe (`esp32/main/timing_probe/`), the
panel probe (`esp32/main/panel_probe.cpp`), the core-timing and
memory-timing calibration probes (branch
`codex/esp32s3-memory-timing-calibration`), capture and parse tooling
(`tools/esp32-capture.py`, `calibration/*/tools/`), build wiring
(`scripts/esp32`, `esp32/main/CMakeLists.txt`), and the production
receipt consumer (`timing/calibration.ts` in the program office).

Environment receipt: ESP-IDF v6.0.2 and v6.1 installed. The timing
probe was built twice: probe source on
`codex/esp32s3-memory-timing-calibration`; main tinydraw checkout at
`0c0b2d9`, fixture worktree (`out/fixtures/esp32s3-timing`) at
`4b5385a`. Nothing was flashed; the board queue rules were respected
(no serial port opened).

## Verdict up front

The measurement harnesses are, with two exceptions, unusually rigorous
for firmware: raw CCOUNT samples retained with no on-device reduction,
checksums and hardware cache-counter signatures gating every strict
sample, encodings and offsets pinned with static_assert and
no-transform assembly regions, typed NDJSON error records with
pass:false on failure, and the production parser rejects any sample
whose cycles does not equal the uint32 CCOUNT delta
(`calibration.ts:453`). The two serious problems: a verification claim
with no committed tooling behind it (F1) and a reproducibility gap
where the pinned v6.1 timing ELF cannot be rebuilt from the fixture
worktree (F2). Neither poisons existing receipts, but both are exactly
the class of thing the fail-closed rules exist to catch.

## Findings, prioritized

### F1 (High): Calibration-probe ELF verification is claimed but not implemented in any committed tool

- Receipts: `calibration/esp32s3-core-timing/README.md:11` ("Encodings
  and loop-body alignments are verified from the built ELF with
  objdump before flashing"), `calibration/esp32s3-memory-timing/README.md:22`,
  and the claim embedded into every emitted configuration record
  ("Encodings are verified from the built ELF by the capture tooling,
  not assumed") and "confirmed from the ELF symbol size"
  (`memory_timing.c:256`).
- The committed tooling on that branch was read end to end: run.sh
  calls only tools/esp32-capture.py and tools/parse_capture.py;
  parse_capture.py parses JSON and computes statistics. Nothing
  invokes objdump or nm or checks any symbol size. The verification
  exists only as prose describing a manual step that left no receipt.
- Why it matters: these receipts back the 1.000-cycle-per-instruction
  and loop-alignment headlines. If the assembler narrows a window or
  re-aligns a loop body, the probe silently measures the wrong shape
  while the configuration record still asserts it was verified. This
  is a claims-versus-receipt gap, the poisoning class AGENTS.md warns
  about.
- Remedy: add an ELF verification step to run.sh or parse_capture.py
  (which already receives --firmware-binary): disassemble the
  issue-block symbols, assert expected encodings and the four
  loop-body residues mod 4, fail the run (exit nonzero, no
  result.json) on mismatch. Alternatively amend the embedded
  uncertainty notes to state who verified what and when. The big
  timing probe already shows the right pattern: timing_probe.cpp
  validates symbol ranges, alignment, and sentinels at runtime
  (`timing_probe.cpp:1352-1375`).

### F2 (High): The pinned v6.1 timing-probe source is not buildable from the fixture worktree, and the fix commit is unmerged

- Receipt: a clean build of the fixture worktree `4b5385a` (parent of
  the pinned commit) under the selected IDF v6.1 with xtensa-esp-elf
  15.2.0 fails to link: undefined reference to esp_rom_regi2c_read
  (`timing_probe.cpp:804,1593`) and to rom_i2c_writeReg
  (`rom_i2c_write_probe_esp32s3.S:38`). The same tree builds clean
  under v6.0.2.
- The fix exists: commit `3db39856` ("timing: update REGI2C probes for
  ESP-IDF 6.1") switches the probe to _regi2c_impl_read /
  _regi2c_impl_write and hal/regi2c_impl.h. That commit lives only on
  branch `codex/lane-0-idf61-probes` (present in the esp32s3-lane-0
  checkout). Neither the main checkout nor the fixture worktree
  contains it.
- Cross-checks that hold: the pinned timing ELF exists at
  esp32s3-lane-0/tinydraw/out/lane0/idf61/esp32-timing-probe/tinydraw_esp32.elf
  and matches the rebaseline README's pin; the pinned panel-probe ELF
  sha `143e9f51...` matches
  `out/fixtures/esp32s3-timing/out/build/esp32-panel-probe/tinydraw_esp32.elf`;
  the pinned sdkconfig sha `20ba6a91...` reproduces exactly from
  source today.
- Honest caveat: the pinned timing ELF was not bit-verified from
  `3db39856` (a rebuild from a different build directory would embed
  different paths, so a sha mismatch would prove nothing). The gap is
  that the canonical tinydraw checkout cannot rebuild the pinned probe
  at all without knowing to fetch a side branch.
- Remedy: merge `3db39856` into main, re-point the fixture worktree at
  the pinned commit (it currently sits at the parent), and record in
  the fixture README which commit each pinned ELF came from. The
  machine-local-ELF limitation is recorded in STATUS.md; the
  unmerged-fix-commit part is not.

### F4 (Medium): Tick protection is inconsistent across measurement classes, and the receipts do not say so

> **Fact-check note: partially supported.** Interrupt masking is inconsistent and metadata omits the distinction, but the pinned timing build uses a 1,000 Hz FreeRTOS tick, not 100 Hz.

- Receipt: MMIO, RTC, and xtos samplers mask interrupts
  (`rsil %0, 15`, `timing_probe.cpp:1151`; level 3 at `:1258`), but the
  plain measure_once path used for every sram/psram/flash/branch/
  dependent-load kernel (`timing_probe.cpp:1096-1110`) runs at task
  level, so a 100 Hz FreeRTOS tick can land inside a window. The
  calibration probes document this choice in their uncertainty_notes;
  the timing probe's metadata record carries no equivalent note, and
  the production consumer keeps min/max of cycles in each receipt.
- Mitigation is real (nearest-rank medians downstream, for example
  `dcache-burst-analysis.ts:176-188`, and 100 samples per
  measurement), so this is a documentation/consistency gap, not a
  correctness bug.
- Remedy: add an uncertainty note to the metadata record, or mask
  interrupts in measure_once as well (the kernels are IRAM-resident,
  so masking is safe there, unlike the contended pass).

### F5 (Medium): timing_probe.cpp is a 2,203-line monolith, and the cohort system triplicates every measurement name

- Receipt: `esp32/main/timing_probe/timing_probe.cpp` is 2,203 lines
  (108 KB), past the 1,000-line bar. It mixes the NDJSON printer,
  metadata, kernels, 11 sampler variants, 20-plus finalizers, the
  90-entry measurement table, six capture-mode cohort flags with six
  hand-written id lists (`timing_probe.cpp:2012-2105`), and the
  run-complete count expression, a five-deep nested ternary over the
  same six flags (`timing_probe.cpp:2163-2175`).
- The real structural cost is cohort bookkeeping: each measurement id
  appears in up to three places (the table, the is_*_measurement list,
  and the count expression), with only static_assert counts protecting
  against drift. Adding one cohort means touching four sites. Code
  judo: replace the six constexpr bool flags plus six id arrays plus
  the count ternary with one cohort descriptor table ({flag, count,
  ids}) that run_suite and the count expression both read. That
  deletes roughly 120 lines and two of the three sites where a typo
  currently fails silently.
- The rest decomposes along existing seams: measurement table,
  samplers, prepare/finalize functions, assembly-adjacent windows are
  already clean layers that live in one anonymous namespace. The
  90-entry table is data and its peer mirroring is deliberate
  matched-encoding design.
- Same threshold, product firmware: vector_v2_gate_harness_kernels.cpp
  (1,049) and co5300_panel_transport.cpp (1,034) also cross 1k.
  Scaled honestly: firmware tables of peer kernels are a weaker smell
  than the cohort triplication, and neither is measurement code.

### F6 (Medium): Unexplained magic in the RTC boot-read priming path

- Receipt: `timing_probe.cpp:1186-1193` caches a static
  Kernel primed_kernel and primes 1 or 2 times depending on wrapper:
  measure_rtc_date_boot_4096_once uses PrimeCount = 2U,
  measure_rtc_xtal_boot_4096_once and the 2048 pair use 1U
  (`timing_probe.cpp:1199-1211`).
- Nothing explains why the date register needs one extra priming pass,
  why the count is per-kernel-identity rather than per-register, or
  what breaks if the asymmetry is wrong. Empirically tuned priming
  counts baked into receipt-producing code with no recorded rationale
  is the magic class the review standard flags, and it is in the
  one-cycle first-line cache probe shift neighborhood STATUS.md marks
  as an open diagnosis.
- Remedy: a named constant with a comment stating the observed
  mechanism (or a decision-record reference), or unify the counts and
  re-verify the counter signatures. At minimum the asymmetry should
  survive the planned pooling-probe diagnosis rather than be
  rediscovered.

### F7 (Medium, process cost): Capture transport has no per-line integrity, and compile-time-only cohorts turn one dropped line into one lost boot

- Receipts: tools/esp32-capture.py writes timestamped lines with no
  JSON validation, no line-level checksum, and a fixed 1-second
  timestamp resolution; the rebaseline README
  (`timing/evidence/idf61-rebaseline-3db3985/README.md`) records that
  repeated USB capture truncation lost six identities and that the
  probe has compile-time cohorts and no runtime selective rerun, so
  another full-suite boot was not used after boot four.
- The firmware side does its part: every record is flushed with fsync
  and 5 ms inter-record delays to survive the USB Serial/JTAG FIFO
  (`timing_probe.cpp:1488-1494`). The host side trusts the transport
  completely.
- Remedy: validate each TINYDRAW_TIMING_NDJSON / CAL_RECORD line as
  JSON inside the capture script, fail (exit 2) on a malformed record,
  and print a per-measurement completeness tally at capture end so
  truncation is caught in-session. A runtime-selective rerun changes
  receipt semantics and is arguably a decision-record matter (the
  meta freeze blocks new decisions), so parser-side validation and
  whole-boot retry are the remedies that need no meta change.

### F8 (Low): Repo-root CMakePresets.json breaks bare idf.py invocations

- Receipt: `idf.py -B ... build` from the tinydraw root fails with
  "Cannot use hidden preset ... host-base" because idf.py auto-selects
  the first preset in the root CMakePresets.json. scripts/esp32 works
  around it by cd-ing into esp32/ and the calibration READMEs run from
  calibration/ subdirectories, so nothing documented is broken.
  One sentence in DEVELOPING.md, or moving the presets file, removes
  the trap.

## What is solid (receipts, not vibes)

- CCOUNT usage is correct everywhere checked: assembly windows read
  rsr.ccount with arguments fully materialized before the window and
  endpoints stored after it (`rgb565_call_window_esp32s3.S`,
  `flash_instruction_bursts_esp32s3.S`); the C side uses
  esp_cpu_get_cycle_count() around a single kernel call; store probes
  place their memw inside the window so completion is counted
  (`sram_microprobes_esp32s3.S`, `mmio_probes_esp32s3.S`).
- Fail-closed is real: checksum per sample, hardware cache-counter
  signatures with the DBUS flash-range registers written and read back
  (`timing_probe.cpp:1735-1746`), sentinel validation, runtime
  alignment checks, a final pass:false on any failure, and
  CALIBRATION_FAILED on interrupt allocation failure treated as a
  failure marker by the capture script.
- Machine-parseable stable output: TINYDRAW_TIMING_NDJSON with
  protocolVersion and typed records; the production consumer enforces
  exact key sets, cycles equal to the uint32 CCOUNT delta, core
  consistency, and at least 100 samples (`calibration.ts:417-453`).
- Alignment and encoding controls enforced where tooling exists:
  static_asserts pin every ProbeContext offset and SoC register used
  (`timing_probe.cpp:56-107`), loop-alignment probes derive residues
  arithmetically in a no-transform region (`core_timing_loops.S`),
  burst targets validate line-exactness at runtime.
- Cache warming is principled: hot prepares run the exact
  seed-dependent chain (`timing_probe.cpp:940-944`), cold prepares use
  esp_cache_msync invalidate with line-exact sizes, and pinned
  first-line flash-burst behavior is enforced by a warm sentinel check.
- Provenance is generated into the ELF (git commit, dirty flag,
  sdkconfig sha, compiler version: `esp32/main/CMakeLists.txt:134-170`)
  and the build script force-reconfigures so a post-commit build
  cannot ship stale metadata (`scripts/esp32:146-148`).
- Raw samples are retained everywhere (100 per measurement, full
  serial logs committed with SHA-256 pins).

## Tinydraw verdict

> **Fact-check note: partially supported.** F3 is invalid: the measured loop body is 37 bytes and the 64-byte invalidation covers it. The remaining F1, F2, F5, and F6 conclusions retain their factual standing.

Approve the probe designs as measurement instruments: the CCOUNT
discipline, cache controls, and fail-closed posture are well above
firmware norms, and no measurement-correctness bug was found in the
code that currently produces adopted receipts. The two items that
should not stand unaddressed: F1 (a "verified, not assumed" claim with
no committed verifier) and F2 (the pinned probe source stranded on an
unmerged branch). Both are cheap and both directly protect receipt
credibility. F3 and F6 are measurement-semantics hygiene the
already-planned first-line-cache diagnosis should absorb. F5 is where
the structural bar genuinely bites, and the cohort-descriptor judo
move is the single highest-leverage cleanup in the file.

---

# Part 5: synthesis and priority order

Everything below is within existing decisions; none of it grows the
meta. Order matters: 1 through 4 gate the differential gate and
receipt credibility; 5 through 7 gate CORE's landing quality.

> **Fact-check note: partially supported.** The extraction defects and missing root/timing TypeScript project configuration are present, but the repository does contain experiment-local tsconfig.json files.

1. Fix the extraction defects (A4): profile path resolution, the
   dangling puck import, README and usage-string paths, a package.json
   with typecheck and test scripts. The timing lab must be green
   before anything compares against it.
> **Fact-check note: partially supported.** The TS sorts are locale-sensitive for unrestricted identifiers; this is a portability risk, not evidence that the future Rust canonical-ledger hash or current fixed fixtures already diverge across hosts.

2. Determinism fix (A3): replace localeCompare at the three named
   sites with the codepoint comparators the codebase already owns.
> **Fact-check note: partially supported.** The rebuild and automated-verifier gaps remain, but the timing evidence README and STATUS already record the relevant TinyDraw commit provenance.

3. Reproducibility of the receipt chain (A5): merge 3db39856 into
   tinydraw main, repoint the fixture worktree, record pinned-ELF
   commit provenance, and add the committed ELF verification step
   (tinydraw F1).
> **Fact-check note: partially supported.** Gate-domain and affine-divergence decisions are needed, but schema-1 has more adapter capability than stated and the eight-cycle divergence requires an affected shared trace and unnormalized comparison.

4. Parked decisions to make at the freeze's end (A1, A2, tinydraw F7):
   the gate's comparison domain, the schema-1/affine divergence
   disposition, and capture-rerun semantics.
> **Fact-check note: partially supported.** Scope typed-error work to stringly loader, image, script, and future TimingBlocked boundaries; CPU and bus execution already use typed Trap and Fault errors.

5. Fork pre-CORE pass (fork findings 1, 2, 3, 7): split periph.rs into
   periph/, extract host concerns from machine.rs behind a hook seam,
   typed errors instead of Result<_, String>, make the objdump gates
   fail closed on missing corpora, and write the interpreter-versus-JIT
   differential test.
> **Fact-check note: partially supported.** Remove the F3 identity-renaming action because the measured loop body is only 37 bytes and is fully invalidated; retain the masking metadata and priming-rationale actions, using the correct 1,000 Hz tick rate.

6. Probe hygiene that the planned first-line-cache diagnosis should
   absorb (tinydraw F3, F4, F6): honest identity names, uncertainty
   notes in metadata, documented priming counts.
> **Fact-check note: partially supported.** The dependency-lint and license actions stand, but the fork has Pages build/deploy CI; it lacks test/format CI, and the current esp32s3 plus xtensa-lx7 em-dash count is 37.

7. Documentation truthing (A6, A9, fork finding 9): mark the
   dependency lint as a target state or add the lint; record the
   em-dash question for the fork as an explicit convention decision;
   add the root LICENSE to the fork.

# Part 6: overall verdict

> **Fact-check note: partially supported.** The governance and measured-mode seams are real, but the decision journal does not match implementation everywhere and fail-closed behavior is incomplete: dependency lint is unenforced, the timing checkout is red, and both objdump tests pass when corpora are missing.

The project's architecture is sound and unusually well governed: the
decision journal matches the code, the receipts chain is real, the
fail-closed culture is implemented rather than proclaimed, and the
execution foundation's seams (Bus, tick, cycles_until_timer,
flush_ticks) already point where measured mode must go. The code
quality findings are concentrated in three predictable places
(extraction residue, a pre-adapter Rust codebase with stringly errors
and fused host concerns, and probe provenance claims that outrun
committed tooling). None of the blockers is expensive relative to what
it protects: the differential gate and the receipts chain are the two
assets the whole program rests on, and both currently have small,
findable, fixable holes.

Honest limits: the lead reviewer personally verified the build, test,
and state receipts and spot-checked the highest-impact claims with
line receipts; the file-by-file depth comes from the three review
sessions whose transcripts are listed at the top of this document.
Nothing was flashed, no serial port was touched, no decisions or lanes
were added by this review.

<!-- END reviewer-two.md -->

---

<!-- BEGIN reviewer-three.md -->

# Thermo-nuclear code-quality and adversarial architecture review

Combined internal review of the program-office repository, retained TypeScript timing material, and the active Rust implementation branches. Existing review files were excluded from the analysis and were not opened or used.

- Part I: program-office architecture, evidence contracts, timing lab, and retained experiments
- Part II: Rust CORE and BOARD implementations, branch integration, scheduler, timing, board models, and verification

---

## Thermo-nuclear code-quality and adversarial architecture review

Repository: `/Users/sarah/src/a/esp32s3-cycle-accurate-wasm`  
Reviewed commit: `96e4bb8` on `main`, no tracked source diff from `origin/main`  
Review date: 2026-09-01

Scope note: this part reviews the program-office repository and archived TypeScript material. Part II reviews the branch-aware Rust implementation.

### Verdict

Do not treat the current repository as an executable or reproducible gate for CORE. The program-office architecture is directionally coherent, but its mandatory one-shot differential depends on a broken, partly extracted TypeScript system, an unavailable full-ELF corpus, and a timing profile whose semantics the governing decision explicitly rejects. The evidence boundary also accepts provenance-shaped strings without proving that they identify real evidence.

The thermo-nuclear standard materially changes the recommended remedy: compress the boundary and delete the duplicated execution stack. Do not rehabilitate thousands of lines of retired TypeScript into a second product. Put the executable gate with the Rust product, retain a small hash-pinned corpus and canonical expected projection here, then remove the donor engine after the one-shot receipt.

The product implementation itself lives in the esp32sim fork, so this report covers only this repository's program-office architecture, evidence contracts, executable timing lab, and retained experiments. Existing files under `reviews/` were excluded and were not opened or used.

### Findings

#### 1. Blocker: the mandatory CORE differential gate cannot run from this checkout

CORE requires replaying shared traces through the TypeScript timing machine and measured Rust mode before phase 1 may exit ([lanes/CORE.md:44](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/lanes/CORE.md:44)). The repository simultaneously says executable tests and build-consumed corpora belong in the esp32sim fork ([AGENTS.md:55](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/AGENTS.md:55)) and labels the timing lab and flexe experiment as archive-only material whose commands ran in another repository ([timing/README.md:1](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/timing/README.md:1), [experiments/esp32s3-flexe-wasm/README.md:1](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/experiments/esp32s3-flexe-wasm/README.md:1)).

This is operationally broken, not merely documentary drift:

- There is no root `package.json`, lockfile, or `tsconfig.json`, although experiment configs extend `../../tsconfig.json` ([experiments/esp32s3-flexe-wasm/tsconfig.json:2](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/experiments/esp32s3-flexe-wasm/tsconfig.json:2)).
- A static resolution scan found 22 missing relative imports. Examples target removed `packs/` and `src/wasm` trees ([trace-timing-adapter-test.ts:9](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/experiments/esp32s3-flexe-wasm/trace-timing-adapter-test.ts:9), [puck-loader-test.ts:3](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/experiments/esp32s3-flexe-wasm/puck-loader-test.ts:3), [timing/report.ts:4](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/timing/report.ts:4)).
- `bun test` produced 202 passes and 7 failures. Several tests and the runtime CLI resolve `timing.json` from the repository root even though it lives under `timing/` ([consumer.test.ts:253](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/timing/consumer.test.ts:253), [runtime-report.ts:251](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/timing/runtime-report.ts:251)).
- The documented flexe harness fails on the missing `../../src/wasm` import.

Remedy: make the esp32sim fork own the executable differential test, toolchain lock, and corpus. This repository should own a small manifest, input hashes, expected semantic projection, and resulting receipt. That restores the stated repository boundary and avoids rebuilding an archive into a second live system.

#### 2. Blocker: the declared gate corpus is not committed or cloud-reproducible

> **Fact-check note: partially supported.** The program-office checkout does not contain the raw trace or source ELF, but CORE commit 516b1ad does commit the complete 900,039-byte shared replay bundle at esp32s3/tests/fixtures/flexe-boot-shared-replay-v1.json. At that HEAD the bundle is unreferenced because its test was reverted, and blank-slate regeneration still depends on the machine-local ELF.

The phase-1 gate names the flexe boot replay's event accounting on shared traces ([lanes/CORE.md:49](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/lanes/CORE.md:49)). The actual replay regenerates a trace from `DEFAULT_TINYDRAW_ESP32S3_FULL_ELF`, then asserts a hard-coded digest ([full-elf-timing-replay-test.ts:155](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/experiments/esp32s3-flexe-wasm/full-elf-timing-replay-test.ts:155), [full-elf-timing-replay-test.ts:176](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/experiments/esp32s3-flexe-wasm/full-elf-timing-replay-test.ts:176)). The raw trace with digest `e025823c...` is not committed. Only its digest and a summary baseline are present. The fixture ELF is explicitly machine-local ([STATUS.md:79](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/STATUS.md:79)), while the roadmap calls CORE fully cloud-viable and unblocked ([roadmap.md:68](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/roadmap.md:68)).

The gate therefore cannot be reproduced by the blank-slate CORE agent described in STATUS. Commit a bounded neutral trace with its exact producer provenance, or select an already committed trace as the gate corpus. The manifest must identify the precise input bytes, producer version, digest algorithm, expected projection, and comparison implementation.

#### 3. Blocker: the one-shot ledger comparison has incompatible cost semantics

> **Fact-check note: partially supported.** The schema-1 versus schema-2 cost mismatch is real, but the binding gate does not expressly require byte-for-byte cross-implementation cost equality. CORE names “event accounting,” and decision 0014 says only to compare ledgers; this becomes a blocker only if the comparison projection includes the disputed schema-1 costs.

Decision 0014 rejects schema-1 `timing.json` because it flattens the affine MMIO model `3n - 8`, and it blocks first-line cache costs pending diagnosis ([decision 0014:64](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/decisions/0014-measured-scheduler-and-adapter-contract.md:64)). Yet the required TypeScript oracle still marks cache-line fills calibrated and stores first-line values 204, 115, and 82 ([timing/timing.json:52](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/timing/timing.json:52)). It also represents same-value writes as scalar `3` cycle entries ([timing/timing.json:99](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/timing/timing.json:99), [timing/timing.json:136](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/timing/timing.json:136)).

The full-ELF replay converts those first-line values to calibrated known costs ([full-elf-timing-replay-test.ts:101](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/experiments/esp32s3-flexe-wasm/full-elf-timing-replay-test.ts:101)) and explicitly requires three known 204-cycle fills ([full-elf-timing-replay-test.ts:542](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/experiments/esp32s3-flexe-wasm/full-elf-timing-replay-test.ts:542)). A conforming schema-2 Rust ledger must preserve the affine cost and block the disputed first-line costs, so byte-for-byte ledger comparison would require Rust to reproduce semantics its governing decision forbids.

Define the differential projection before implementation. The old oracle can validate stable event identity, order, address classification, and count. Schema-2 tests must independently validate tiered costs, affine terms, receipt binding, and blocked totals. Never compare cost claims that the donor format cannot represent correctly.

#### 4. High: every evidence path in the canonical timing profile is stale

All six evidence fields in `timing/timing.json` point into the removed `packs/esp32-s3-touch-amoled-18/timing/evidence/` tree, for example [timing/timing.json:16](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/timing/timing.json:16), [timing/timing.json:54](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/timing/timing.json:54), and [timing/timing.json:161](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/timing/timing.json:161). The matching basenames are under `timing/evidence/`, but none of the recorded paths resolves. The runtime report also emits the obsolete pack path as `PROFILE_SOURCE` ([runtime-report.ts:20](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/timing/runtime-report.ts:20)).

This violates the repository rule that every measured claim has a committed path into this repository ([AGENTS.md:16](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/AGENTS.md:16)). Retarget the references and make the gate manifest verify path existence plus content hashes. A profile parser that accepts arbitrary evidence strings is not an evidence boundary.

#### 5. High: the QEMU oracle fixture presents synthetic provenance as an external receipt

> **Fact-check note: partially supported.** The fake digest and weak validation are real, but this artifact is explicitly named .fixture.json, its path and version fields say fixture, and the README separates deterministic fixture checks from live QEMU verification. It should not be described as an actual external receipt unless another document elevates it to that status.

The default observation claims to be Espressif QEMU/GDB while using fixture paths, fixture version strings, and a SHA-256 value of 64 repeated `1` characters ([espressif-qemu-observation.fixture.json:3](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/experiments/esp32s3-qemu-oracle/fixtures/espressif-qemu-observation.fixture.json:3)). The parser checks only that `qemuSha256` is a string ([oracle.ts:387](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/experiments/esp32s3-qemu-oracle/oracle.ts:387)). The comparator then compares case payloads copied into two local JSON structures ([oracle.ts:488](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/experiments/esp32s3-qemu-oracle/oracle.ts:488)). The documented comparison passes and reports the fake digest.

This validates serialization consistency, not an independent QEMU oracle. Rename it as a synthetic parser fixture and remove external-evidence language, or replace it with an actual observation whose QEMU binary, GDB, firmware, command, and outputs are hash-pinned. Live-oracle tests may skip when tools are absent, but synthetic fixtures must never inherit evidence status.

#### 6. High: runtime-trace provenance can be forged at the exported boundary

`runRuntimeTimingTrace` accepts caller-provided provenance and validates only that it is an object whose `source` matches the claim ([runtime-trace.ts:361](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/timing/runtime-trace.ts:361), [runtime-trace.ts:391](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/timing/runtime-trace.ts:391)). It does not validate digest algorithm or hex, bounds consistency, overflow state, extension shape, or referenced access IDs. It returns the caller's provenance object by reference ([runtime-trace.ts:469](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/timing/runtime-trace.ts:469)).

The neutral adapter contains stricter validation ([trace-adapter.ts:287](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/timing/trace-adapter.ts:287)), but the public runtime function does not require input to pass through it. Put one canonical provenance parser at the exported boundary. Validate, normalize, deep-copy, and freeze the result. Internal adapters should produce that canonical input type, not a parallel looser shape.

#### 7. High: 32-bit range validation wraps and accepts memory crossing the address-space boundary

QEMU corpus overlap checking computes each range end with `>>> 0` ([oracle.ts:199](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/experiments/esp32s3-qemu-oracle/oracle.ts:199)). A two-byte entry beginning at `0xffffffff` wraps its end to `1` and is accepted. The runner uses the same wrapping model when visiting addresses, so the accepted range writes one byte at `0xffffffff` and another at `0x00000000`.

Compute range ends as safe integers or `bigint`, require `start + length <= 2^32`, then convert individual addresses to u32 only after validation. Apply the same checked-range primitive to corpus parsing, observed-memory parsing, and the GDB runner.

#### 8. High: uploaded ELF parsing copies unbounded input before any execution quota applies

The browser experiment accepts a caller-selected ELF. `parseXtensaElf32WithDigest` copies the complete input immediately ([elf-image-core.ts:47](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/experiments/esp32s3-flexe-wasm/elf-image-core.ts:47)) and copies each load segment again ([elf-image-core.ts:103](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/experiments/esp32s3-flexe-wasm/elf-image-core.ts:103)). It has structural range checks but no maximum input bytes, program-header count, load-segment count, or total segment bytes. The later instruction and page limits in `runSparseXtensaElf` do not prevent the initial allocation spike.

Enforce artifact size and segment quotas before copying or hashing, using the same public-boundary quota policy intended for the Rust adapter. Avoid duplicate segment copies where an immutable subarray or streaming digest suffices.

#### 9. High: strict lane serialization extends the critical path without an architectural dependency

The coordinator mandates BOARD, CORE phase 1, then CORE phase 2, one lane at a time, and excludes SPEED ([lanes/COORDINATOR.md:14](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/lanes/COORDINATOR.md:14)). The roadmap says CORE is fully unblocked and that CORE and SPEED are independent critical paths ([roadmap.md:68](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/roadmap.md:68), [roadmap.md:73](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/roadmap.md:73)). SPEED is required for the real-time definition of done ([lanes/SPEED.md:29](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/lanes/SPEED.md:29)). Only physical-board access is intrinsically serialized.

Dispatch CORE phase 1 and SPEED in separate clones alongside BOARD. Keep board sessions under one owner and keep CORE phase 2 dependent on phase 1. The current sequence delays both accuracy and performance discovery and postpones integration risk until after the demo.

#### 10. High: the complete-SoC promise has deferred scope with no owner or acceptance test

> **Fact-check note: partially supported.** SHIP owns a release capability matrix and a generic requirement that deferred items be satisfied or explicitly waived. It still does not assign an implementation owner or per-block acceptance test for the deferred SoC remainder.

The product claims the complete ESP32-S3 SoC ([roadmap.md:5](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/roadmap.md:5)), and decision 0013 says radio and omitted blocks are deferred, not excluded ([decision 0013:40](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/decisions/0013-product-identity-fork-owns-the-product.md:40)). The definition of done covers boot, dual core, board devices, speed, and timing bounds, but does not enumerate remaining SoC capabilities ([roadmap.md:21](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/roadmap.md:21)). BOARD explicitly excludes radio ([lanes/BOARD.md:84](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/lanes/BOARD.md:84)), and no current lane owns the deferred SoC remainder.

Publish a capability matrix with an owner and acceptance test for every claimed block, or narrow the product claim to the supported SoC subset. “Complete” cannot remain an untestable future adjective outside the lane plan.

#### 11. Medium: the retired TypeScript runner is a second execution stack disguised as a fixture

> **Fact-check note: partially supported.** The 1,023-line runner and its 701-line exported workflow are verified. The stated about 3,800 production lines before tests depends on an unstated file boundary; an obvious non-test set comprising the runner, ELF/trace/cache helpers, and direct-boot modules totals about 2,660 lines.

Decision 0013 permits the TypeScript timing machine for one final differential and forbids a TypeScript execution engine ([decision 0013:25](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/decisions/0013-product-identity-fork-owns-the-product.md:25)). `full-elf-runner.ts` is 1,023 lines, and `runSparseXtensaElf` alone spans lines 323 through 1,023. It owns the Wasm ABI, sparse ELF loading, page transfer, ROM callback contracts, cache bootstrap, system/RTC/REGI2C MMIO state, BBPLL behavior, trace decoding, and result projection ([full-elf-runner.ts:323](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/experiments/esp32s3-flexe-wasm/full-elf-runner.ts:323), [full-elf-runner.ts:917](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/experiments/esp32s3-flexe-wasm/full-elf-runner.ts:917)). The direct-boot TypeScript implementation is about 3,800 production lines before tests.

Do not refactor this into a cleaner second emulator. Freeze the smallest canonical trace and semantic projection needed by the differential, execute that gate in the Rust fork, then delete or permanently archive the runner and its peripheral whitelist.

#### 12. Medium: giant files merge unrelated change axes and hide duplicated contracts

`timing/consumer.ts` is 1,158 lines and combines the Wasm snapshot ABI, schema-1 profile validation, decoding, report construction, panel scheduling, and serialization ([consumer.ts:1](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/timing/consumer.ts:1), [consumer.ts:1123](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/timing/consumer.ts:1123)). `machine.ts` is 994 lines, `cache.ts` 932, `execution.ts` 870, and their main workflows carry many nested kind/status/resource branches. Test files reach 2,278 and 1,380 lines, often as top-level assertion scripts where an early failure suppresses the rest of the checks.

If the donor lab must remain live until the gate lands, split only at canonical boundaries: `profile-schema`, `wasm-abi`, `ledger`, and pure resolve/cache/schedule phases. Convert monolithic assertion scripts to independent parameterized tests. Delete the split modules with the donor engine after the one-shot receipt.

#### 13. Medium: timing certainty and provenance are represented by parallel, inconsistent types

Cache and execution layers define adjacent cost contracts with different optionality. Execution permits an unknown latency with optional `source` ([execution.ts:12](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/timing/execution.ts:12)); cache paths require and transform similar provenance separately. Validators such as `objectAt`, `exactKeys`, non-empty string checks, latency checks, and custom assertions are repeated across modules. `machine.ts` then translates between the shapes ([machine.ts:30](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/timing/machine.ts:30)). This is how receipt data gets lost or folded into free-form reasons.

One schema-2 cost algebra should be the canonical type from importer through ledger: exact, affine, interval, distribution, and unknown, each with a typed receipt reference or typed refusal. Cache, MMIO, CPU, and scheduler events should carry that type directly.

#### 14. Medium: shallow `readonly` claims expose mutable ledger state

Cache emission constructs a mutable `completeEvent`, places it inside a frozen wrapper, and returns cost and event references without deep-copying them ([cache.ts:504](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/timing/cache.ts:504)). Runtime results similarly return the caller's provenance object unchanged ([runtime-trace.ts:471](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/timing/runtime-trace.ts:471)). TypeScript `readonly` does not make runtime objects immutable, so a caller can mutate evidence-bearing nested data after scheduling and before hashing or serialization.

Normalize and own all boundary inputs. Deep-freeze the small canonical value objects, keep mutable machine state private, and serialize from owned ledger records. Hashing a graph that retains caller-owned references is not a trustworthy receipt mechanism.

#### 15. Medium: binding documents disagree about whether hardware capture blocks modeling

The roadmap lane table says captures gate panel and touch modeling ([roadmap.md:69](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/roadmap.md:69)). The BOARD brief says logic-analyzer capture gates timing claims but explicitly does not gate demo modeling ([lanes/BOARD.md:40](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/lanes/BOARD.md:40), [lanes/BOARD.md:75](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/lanes/BOARD.md:75)). STATUS agrees with the BOARD brief ([STATUS.md:61](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/STATUS.md:61)).

Amend the roadmap row to say that the touch identity probe gates naming, logic capture gates timing claims, and firmware-contract-first demo modeling is unblocked. This is a dispatch-level contradiction, not harmless wording.

#### 16. Medium: the normative gate still directs a write into the frozen archive

Decision 0014 says the differential comparison is archived as a receipt in puck ([decision 0014:95](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/decisions/0014-measured-scheduler-and-adapter-contract.md:95)), while the binding repository rule says the puck archive receives nothing new ([AGENTS.md:58](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/AGENTS.md:58)) and the CORE brief says to archive the receipt here ([lanes/CORE.md:44](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/lanes/CORE.md:44)). The amendment at the end of decision 0014 moves the numbered decision home but does not amend the gate's receipt destination ([decision 0014:110](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/decisions/0014-measured-scheduler-and-adapter-contract.md:110)).

Add a dated amendment naming this repository as the receipt destination and mapping old lane labels to current lanes. A blank-slate agent following the normative section should not be told to violate a binding repository rule.

### Structural cut line

1. Define one differential manifest with a committed neutral trace, producer provenance, event-only comparison projection, and expected digest.
2. Put the executable gate and its locked toolchain in the esp32sim fork, alongside the implementation it gates.
3. Implement schema-2's canonical cost and receipt algebra once, then use it through importer, scheduler, and ledger without adapter-specific variants.
4. Produce the one-shot receipt in this repository, retire the TypeScript donor, and delete the direct-boot runner, obsolete profile consumer, and redundant tests.
5. Run CORE and SPEED alongside BOARD, with only physical-board sessions and CORE phase 2 serialized by real dependencies.

### Verification performed

- `bun test`: 202 passed, 7 failed, 209 total across 31 files.
- `bun run experiments/esp32s3-qemu-oracle/test.ts`: passed fixture comparison; live QEMU path skipped because external tools were absent.
- `bun run experiments/esp32s3-qemu-oracle/compare.ts`: passed while reporting the synthetic all-`1` QEMU digest.
- `bun run experiments/esp32s3-flexe-wasm/test.ts`: failed on the missing `../../src/wasm` import.
- Static relative-import resolution scan: 22 unresolved imports outside `reviews/`.
- Markdown-link resolution scan: no broken Markdown links outside `reviews/`; the broken evidence references are JSON strings and therefore evade that check.
- Adversarial range probe: a two-byte memory entry at `0xffffffff` was accepted and wrapped through address zero.
- Repository remained unchanged. The pre-existing untracked `reviews/2026-09-01-internal/` directory was not opened or modified.

---

## Rust implementation: thermo-nuclear and adversarial architecture review

Repository: [`aliceisjustplaying/esp32sim`](https://github.com/aliceisjustplaying/esp32sim)  
Review date: 2026-09-01  
Review basis:

- CORE: [`core/measured-phase1` at `516b1ad`](https://github.com/aliceisjustplaying/esp32sim/tree/516b1ad5685f065eca9e226902149a4379a76cdd)
- BOARD: [`board/tinydraw-v2-maintained` at `b7c9b87`](https://github.com/aliceisjustplaying/esp32sim/tree/b7c9b87f6994b163e40c1deb23bd70a00a8f76ff)
- Safeguards: [`maintenance/rust-safeguards` at `b138473`](https://github.com/aliceisjustplaying/esp32sim/tree/b138473e839b5243fd97299a309746355ec1a9d4)
- Common ancestor: `main` at `2114ffc`

Existing review files were excluded and were not opened or used.

### Branch resolution

There is no single most-up-to-date branch containing the current work. `board/tinydraw-v2-maintained` is the newest remote head by commit time, but it does not contain `core/measured-phase1`; CORE does not contain BOARD either. They diverge at `2114ffc`. A trial merge reports 30 content-conflicted files, including the CLI, Wasm adapter, Xtensa execution/JIT, and most ESP32-S3 machine, bus, peripheral, board, and web modules.

The practical answer is therefore:

- BOARD is the latest board/demo head and includes the strict safeguard changes.
- CORE is the latest measured-scheduler head.
- Neither is the latest complete product because no integration head exists.

### Verdict

Do not release or accept phase completion from either head. BOARD has a credible safeguard pipeline and builds to Wasm, but it lacks the measured scheduler. CORE has substantial Rust implementation, but its mandatory one-shot differential was reverted at HEAD, it is not based on the safeguard line, and several scheduler/evidence invariants are split across duplicated real and fake implementations.

The thermo-nuclear cut is a shared scheduler transaction engine plus typed execution/timing events. The ESP32 machine and fake backend should supply device-specific hooks; they should not independently implement ordering, quotas, stop precedence, and ledger behavior. Rebase that engine over BOARD immediately, make board edges first-class deadlines, and delete the duplicated control paths before adding more peripherals or timing classes.

### Findings

#### 1. Blocker: no branch contains the product being reviewed

BOARD and CORE are sibling histories with a 30-file content-conflict surface. The conflicts cross the exact seams that must compose correctly: [`esp32s3/src/bus.rs`](https://github.com/aliceisjustplaying/esp32sim/blob/516b1ad5685f065eca9e226902149a4379a76cdd/esp32s3/src/bus.rs), [`esp32s3/src/periph.rs`](https://github.com/aliceisjustplaying/esp32sim/blob/516b1ad5685f065eca9e226902149a4379a76cdd/esp32s3/src/periph.rs), [`xtensa-lx7/src/exec.rs`](https://github.com/aliceisjustplaying/esp32sim/blob/516b1ad5685f065eca9e226902149a4379a76cdd/xtensa-lx7/src/exec.rs), and [`wasm/src/lib.rs`](https://github.com/aliceisjustplaying/esp32sim/blob/516b1ad5685f065eca9e226902149a4379a76cdd/wasm/src/lib.rs). BOARD passes its safeguards and CORE passes its tests only in isolation. Neither result says whether the intended product compiles or preserves cycle ordering.

Create an integration trunk now. Rebase CORE onto the safeguard/BOARD ancestry, resolve conflicts once with tests around each architectural seam, and require all product work to branch from that head. A release claim must name one immutable commit containing CORE, BOARD, Wasm, and safeguards.

#### 2. Blocker: CORE's mandatory one-shot differential is explicitly absent at HEAD

CORE HEAD is commit [`516b1ad`, “Revert Compare measured Flexe replay with TypeScript ledger”](https://github.com/aliceisjustplaying/esp32sim/commit/516b1ad5685f065eca9e226902149a4379a76cdd). It deletes the entire 623-line `esp32s3/tests/flexe_replay.rs` gate and its test dependencies. The 35,576-line [`flexe-boot-shared-replay-v1.json`](https://github.com/aliceisjustplaying/esp32sim/blob/516b1ad5685f065eca9e226902149a4379a76cdd/esp32s3/tests/fixtures/flexe-boot-shared-replay-v1.json) remains, but no source or test references it.

The branch cannot satisfy a phase-exit condition whose only executable gate is reverted. Repair the projection/schema mismatch, restore a bounded differential test, and make the fixture manifest and expected digest part of the test input. If the 35k-line corpus is not the accepted gate, remove it rather than retaining an unexercised evidence-shaped artifact.

#### 3. Blocker: BOARD's tear edge has no deadline representation for CORE

> **Fact-check note: partially supported.** The cited APIs are on separate branches, so late tear delivery and ledger omission are credible integration hazards, not bugs executable at either cited HEAD. They become present if the branches are mechanically combined without redesigning the board deadline interface.

BOARD added `BoardModel::input_changes(cycles)`, and the AMOLED model emits GPIO 13 tear edges after consuming a batch of cycles ([`board.rs` lines 796-805](https://github.com/aliceisjustplaying/esp32sim/blob/b7c9b87f6994b163e40c1deb23bd70a00a8f76ff/esp32s3/src/board.rs#L796-L805)). The bus applies every returned edge at the end of its deferred tick ([`bus.rs` lines 1220-1227](https://github.com/aliceisjustplaying/esp32sim/blob/b7c9b87f6994b163e40c1deb23bd70a00a8f76ff/esp32s3/src/bus.rs#L1220-L1227)). CORE's measured deadline model cannot query a board deadline because that API does not exist on its branch.

After a mechanical merge, the measured scheduler can advance across a tear edge, apply it late, and omit the edge from the ledger. Replace retrospective `input_changes` with `next_deadline` plus `advance_to`, or timestamp every returned transition. Feed the same typed deadline into both deferred native execution and measured scheduling.

#### 4. High: fake and real schedulers already disagree on atomic output quotas

The fake backend computes pending output bytes and checks the quota before committing an instruction ([`fake.rs` lines 230-266](https://github.com/aliceisjustplaying/esp32sim/blob/516b1ad5685f065eca9e226902149a4379a76cdd/backend-api/src/fake.rs#L230-L266), [`fake.rs` lines 500-538](https://github.com/aliceisjustplaying/esp32sim/blob/516b1ad5685f065eca9e226902149a4379a76cdd/backend-api/src/fake.rs#L500-L538)). The ESP32 backend commits guest state and the ledger first, then collects output and may return `BackendFault` ([`backend.rs` lines 632-662](https://github.com/aliceisjustplaying/esp32sim/blob/516b1ad5685f065eca9e226902149a4379a76cdd/esp32s3/src/backend.rs#L632-L662)). The two contract implementations therefore expose different atomicity for the same resource failure.

Put quota reservation, commit ordering, output publication, and ledger publication into one backend-neutral transaction. The fake should exercise that engine; it should not be a second scheduler specification.

#### 5. High: interrupt acceptance mutates CPU state outside measured pricing and the ledger

When no instruction is pending, the scheduler refreshes interrupts and calls `cpu.check_interrupts()` before planning ([`backend.rs` lines 683-686](https://github.com/aliceisjustplaying/esp32sim/blob/516b1ad5685f065eca9e226902149a4379a76cdd/esp32s3/src/backend.rs#L683-L686)). That call can change PC, processor state, exception state, and window state. There is no timing claim, pending transaction, or interrupt-accept ledger kind around it.

Timers and USB are active devices, so realistic execution can enter interrupts for zero measured cycles with no auditable event. Model interrupt acceptance as a typed execution transaction with an adopted cost claim, completion, architectural commit, and ledger entry.

#### 6. High: timing state commits even when the instruction traps

`complete_instruction` calls `commit_decoded` and then unconditionally commits all staged timing mutations, including on an exception ([`measured.rs` lines 358-370](https://github.com/aliceisjustplaying/esp32sim/blob/516b1ad5685f065eca9e226902149a4379a76cdd/xtensa-lx7/src/measured.rs#L358-L370)). A unit test explicitly locks this behavior in ([`measured.rs` lines 468-479](https://github.com/aliceisjustplaying/esp32sim/blob/516b1ad5685f065eca9e226902149a4379a76cdd/xtensa-lx7/src/measured.rs#L468-L479)). Staged mutations include successful-load producer state, so a faulting load can manufacture a dependency producer even though it never wrote the target register.

Split attempt-time effects from retire-time effects. Cache lookups may have attempt-time consequences; dependency-producer state requires successful retirement. Commit each typed mutation class according to the actual completion outcome.

#### 7. High: the cache model is an unbounded lifetime set, not a cache

`Esp32TimingSource` records instruction and data lines in `BTreeSet`s. Once observed, a line is a hit forever; there is no capacity, associativity, eviction, invalidation, per-core state, dirty/writeback behavior, MMU remap effect, or MSPI arbitration ([`measured.rs` lines 67-108](https://github.com/aliceisjustplaying/esp32sim/blob/516b1ad5685f065eca9e226902149a4379a76cdd/esp32s3/src/measured.rs#L67-L108)). “Subsequent line” is inferred from the last miss even after arbitrary intervening activity.

This architecture cannot grow into cycle-accurate cache/MSPI behavior. Introduce explicit cache sets/ways and an MSPI state machine behind typed accesses, or consume the canonical SoC cache model. Delete the lifetime-set shortcut once the real state exists.

#### 8. High: measured execution shadows the decoder/interpreter's opcode semantics

> **Fact-check note: partially supported.** Every load form recognized by load_target is mislabeled l32i, but four access-planned load forms (Lsi, Lsip, Lsx, and Lsxp) are not recognized as dependency producers at all. Thus “all load forms are labeled l32i” is overbroad, although the duplicated and incomplete opcode taxonomies are real.

`access_shape` re-encodes address generation and memory width for loads, stores, and atomics ([`xtensa measured.rs` lines 179-214](https://github.com/aliceisjustplaying/esp32sim/blob/516b1ad5685f065eca9e226902149a4379a76cdd/xtensa-lx7/src/measured.rs#L179-L214)). `reads_register` maintains a separate large opcode match for dependency behavior and blocks on any unreviewed consumer after a load ([`ESP32 measured.rs` lines 120-146](https://github.com/aliceisjustplaying/esp32sim/blob/516b1ad5685f065eca9e226902149a4379a76cdd/esp32s3/src/measured.rs#L120-L146)). All load forms are also labeled `l32i`, losing producer identity ([lines 110-118](https://github.com/aliceisjustplaying/esp32sim/blob/516b1ad5685f065eca9e226902149a4379a76cdd/esp32s3/src/measured.rs#L110-L118)).

Have decode produce canonical operand-use and memory-access metadata once. The interpreter, JIT, disassembler, and timing engine should consume the same typed semantic record. This removes two drift-prone opcode taxonomies and the fail-closed gaps they create.

#### 9. High: strings are used as an internal timing mutation language

`Price` and `PendingInstruction` carry `Vec<String>` mutations ([`xtensa measured.rs` lines 107-160](https://github.com/aliceisjustplaying/esp32sim/blob/516b1ad5685f065eca9e226902149a4379a76cdd/xtensa-lx7/src/measured.rs#L107-L160)). The hot path formats cache and load mutations, then commit splits, parses, and reaches `expect`/`unreachable` on those strings ([`ESP32 measured.rs` lines 260-295](https://github.com/aliceisjustplaying/esp32sim/blob/516b1ad5685f065eca9e226902149a4379a76cdd/esp32s3/src/measured.rs#L260-L295)). Cost bindings likewise allocate multiple free-form strings per instruction.

Replace the private mini-language with a `TimingMutation` enum and interned/static identifiers. Parse text only at the profile boundary; the execution hot path should be exhaustively typed and allocation-light.

#### 10. High: the caller chooses the timing-profile trust root

The public constructor accepts a caller-supplied `ReceiptManifest` and uses it to validate the supplied timing profile ([`backend.rs` lines 23-53](https://github.com/aliceisjustplaying/esp32sim/blob/516b1ad5685f065eca9e226902149a4379a76cdd/esp32s3/src/backend.rs#L23-L53)). No non-test product construction pins a canonical manifest. A future public adapter could therefore accept a forged profile when the same caller also supplies the matching forged trust set.

Make the product own a compiled or hash-pinned manifest, or require a verified signed manifest from a separately configured trust root. Build a canonical lookup map once; do not scan an arbitrary caller-owned vector for every profile receipt.

#### 11. High: the ledger hash does not identify what was executed

The backend returns a `LoadReceipt`, but `LoadedArtifacts` retains only artifact bytes and the imported profile ([`backend.rs` lines 15-21](https://github.com/aliceisjustplaying/esp32sim/blob/516b1ad5685f065eca9e226902149a4379a76cdd/esp32s3/src/backend.rs#L15-L21)). Ledger entries contain cycle, epoch, kind, and cost claims; they do not bind the artifact-set digest, firmware digest, mask ROM, boot mode, backend configuration, or timing-profile digest. A canonical ledger hash therefore cannot prove which run produced it.

Add a canonical `RunIdentity` header and include its hash in every ledger/delta receipt. It should bind exact artifact bytes, boot/config schema, profile/manifest digests, emulator commit, and input transcript identity.

#### 12. High: cost receipts are multiplied across every instruction and cache entry

Every `LedgerEntry` owns `Vec<CostClaim>`, and each claim repeats receipt-reference strings. The backend clones those claims into deltas; timing lookup clones them into each instruction; measured block caching clones a base-claim vector into cached payloads. With native cache capacity up to roughly one million entries, repeated provenance dominates useful timing state.

Create one canonical claim/receipt catalog per run and store compact claim IDs in ledger entries and block-cache payloads. Hash ledger entries incrementally and expose bounded/drainable deltas. Provenance belongs in one immutable table, not in every event.

#### 13. High: artifact loading performs avoidable full-image copies

`load` clones each selected artifact out of the input vector ([`backend.rs` lines 445-466](https://github.com/aliceisjustplaying/esp32sim/blob/516b1ad5685f065eca9e226902149a4379a76cdd/esp32s3/src/backend.rs#L445-L466)). `reset` then clones the entire `LoadedArtifacts` object before the machine copies ROM/flash bytes again ([`backend.rs` lines 499-536](https://github.com/aliceisjustplaying/esp32sim/blob/516b1ad5685f065eca9e226902149a4379a76cdd/esp32s3/src/backend.rs#L499-L536)). At public artifact limits, hostile input can cause hundreds of MiB of transient allocation before execution quotas matter.

Validate and partition the owned vector without cloning, keep large immutable blobs in `Arc<[u8]>` or move them into machine storage, and make reset borrow immutable images. Charge/limit allocation before parsing or duplication.

#### 14. High: guest-controlled GP-SPI DMA can hang or panic the host

> **Fact-check note: partially supported.** The zero-length self-referential descriptor hang is directly present. The unchecked u32 additions panic when overflow checks are enabled and wrap in ordinary optimized arithmetic; a release-mode host panic caused by those additions alone is not established by the cited code.

BOARD's new `spi2_dma_payload` walks guest descriptors with no descriptor budget or visited set ([`bus.rs` lines 362-411](https://github.com/aliceisjustplaying/esp32sim/blob/b7c9b87f6994b163e40c1deb23bd70a00a8f76ff/esp32s3/src/bus.rs#L362-L411)). A zero-length descriptor whose `next` points to itself leaves the payload empty and loops forever in a guest MMIO write. Guest-controlled `desc + 4`, `desc + 8`, and `buf + buf_pos + i` are unchecked; release overflow checks can turn address wrap into a host panic. Unmapped descriptor/data reads silently become zero, and descriptor ownership is ignored.

Use one bounded GDMA walker with checked addresses, a visited/step budget, owner/size/length validation, and typed DMA faults. Reuse it for GP-SPI, I2S, camera, and LCD paths.

#### 15. High: the product smoke gate neither runs the Wasm product nor verifies pixels

`scripts/tinydraw-v2.sh` builds the native CLI and opens its web server; it never builds or launches `esp32sim-wasm` ([lines 26-56](https://github.com/aliceisjustplaying/esp32sim/blob/b7c9b87f6994b163e40c1deb23bd70a00a8f76ff/scripts/tinydraw-v2.sh#L26-L56)). Its smoke assertion greps firmware log strings only ([lines 58-71](https://github.com/aliceisjustplaying/esp32sim/blob/b7c9b87f6994b163e40c1deb23bd70a00a8f76ff/scripts/tinydraw-v2.sh#L58-L71)). QSPI, CO5300 decoding, framebuffer export, or browser canvas rendering can all fail while the guest emits the expected logs.

Add a Wasm/browser-hosted smoke and assert a deterministic framebuffer hash or golden crop after the scripted stroke. Keep the native smoke as a lower-level diagnostic.

#### 16. High: the documented touch interrupt is not modeled

The BOARD model updates touch coordinates/state but `input_changes` emits only tear GPIO 13 ([`board.rs` lines 779-805](https://github.com/aliceisjustplaying/esp32sim/blob/b7c9b87f6994b163e40c1deb23bd70a00a8f76ff/esp32s3/src/board.rs#L779-L805)). The board documentation and TinyDraw wiring identify an active-low touch interrupt on GPIO 21. Interrupt-driven firmware will never see it, while polling code can conceal the omission.

Drive GPIO 21 from the touch controller's pending/release state with exact transition timestamps, and include interrupt-driven touch behavior in the smoke gate.

#### 17. Medium: a device deadline reserves 1,024 ledger entries speculatively

`current_cycle_ledger_entries` charges a fixed 1,024 entries whenever any device is due ([`backend.rs` lines 375-404](https://github.com/aliceisjustplaying/esp32sim/blob/516b1ad5685f065eca9e226902149a4379a76cdd/esp32s3/src/backend.rs#L375-L404)). A valid caller budget below 1,024 can stop forever at a device deadline even if processing it would emit one entry.

Stage the exact due-device batch or process one device completion as an atomic transaction. Budget estimation must be derived from real pending work, not a magic reservation.

#### 18. Medium: the AMOLED board wires a different panel controller into its IO expander

`WaveshareAmoled18V2` owns an `St7701State` and passes it to `Tca9554` ([`board.rs` lines 718-768](https://github.com/aliceisjustplaying/esp32sim/blob/b7c9b87f6994b163e40c1deb23bd70a00a8f76ff/esp32s3/src/board.rs#L718-L768)). That TCA9554 implementation is specifically a Touch-LCD-4B 9-bit ST7701 init-SPI decoder ([`i2c.rs` lines 389-455](https://github.com/aliceisjustplaying/esp32sim/blob/b7c9b87f6994b163e40c1deb23bd70a00a8f76ff/esp32s3/src/i2c.rs#L389-L455)). The AMOLED uses CO5300, and its CO5300 state never consumes this ST7701 state.

Split generic TCA9554 register/line behavior from board-specific wiring. Connect its real reset/power/backlight lines to CO5300 state and delete the dead ST7701 interpretation from this board.

#### 19. Medium: three named board devices are register RAM, not behavioral models

The PMIC, RTC, and IMU are generic `Reg8Device` instances that accept every access and retain arbitrary bytes ([`board.rs` lines 761-769](https://github.com/aliceisjustplaying/esp32sim/blob/b7c9b87f6994b163e40c1deb23bd70a00a8f76ff/esp32s3/src/board.rs#L761-L769), [`i2c.rs` lines 210-253](https://github.com/aliceisjustplaying/esp32sim/blob/b7c9b87f6994b163e40c1deb23bd70a00a8f76ff/esp32s3/src/i2c.rs#L210-L253)). This is enough for selected initialization reads but not for timekeeping, power rails, interrupts, reset behavior, or sensor samples.

Label these as initialization stubs in the capability surface and test the exact accepted firmware contract. Replace each with behavior only when the product claim includes that capability.

#### 20. Medium: CO5300 parsing depends on magic transaction shapes

> **Fact-check note: partially supported.** The exact four-byte command-shape dependency is real, but current TinyDraw explicitly configures 32 command bits and 8 parameter bits, so the review does not establish that combined or differently padded command/data transfers are required by the present firmware contract.

The board recognizes a command only when a transaction is exactly four bytes and begins with `0x02` or `0x32`, then expects payload in subsequent transfers. Valid combined command/data transfers or different padding can be silently ignored. The test mirrors the implementation's chosen shape, so it does not protect against real GP-SPI framing variation.

Represent command/address/data phases explicitly at the GP-SPI boundary, or make the panel parser consume a continuous framed stream. Add captured-sequence tests and fragmentation/combination variants.

#### 21. Medium: new feature logic is accumulating in existing giant modules

CORE adds a 1,207-line [`esp32s3/src/backend.rs`](https://github.com/aliceisjustplaying/esp32sim/blob/516b1ad5685f065eca9e226902149a4379a76cdd/esp32s3/src/backend.rs) that owns loading, reset, inputs, outputs, device deadlines, quotas, scheduler order, interpreter transactions, inspection, ledgering, and tests. BOARD adds GP-SPI DMA directly to a roughly 1,350-line bus, while the peripheral module is already roughly 3,900 lines. The fake backend repeats another 672-line scheduler.

Split by stable ownership boundaries: scheduler transaction engine, artifact/run identity, ledger/catalog, GP-SPI, GDMA walker, and board wiring. This is a deletion/refactoring gate: adding more device or timing cases before extracting those seams will multiply condition cross-products.

#### 22. Medium: canonical artifact hashing depends on enum declaration order

Artifact validation encodes `ArtifactKind` through `as u16`. Without explicit stable discriminants or a dedicated wire encoder, inserting or reordering a variant silently changes artifact-set digests across versions.

Define explicit canonical wire tags and version the encoding. Tests should lock exact bytes and digests, not only round-trip behavior.

#### 23. Medium: instruction completion accepts late commits

`complete_instruction` rejects `now < completion` but accepts `now > completion` ([`measured.rs` lines 358-370](https://github.com/aliceisjustplaying/esp32sim/blob/516b1ad5685f065eca9e226902149a4379a76cdd/xtensa-lx7/src/measured.rs#L358-L370)). The scheduler currently calls at equality, but the function is public and its contract permits cycle-skipping callers to commit late without an error.

Require equality, or make completion private to the scheduler transaction engine and encode lateness as an invariant failure.

#### 24. Medium: the contract fake and real backend validate different run budgets

The ESP32 backend rejects `max_output_events` above the hard limit ([`backend.rs` lines 561-565](https://github.com/aliceisjustplaying/esp32sim/blob/516b1ad5685f065eca9e226902149a4379a76cdd/esp32s3/src/backend.rs#L561-L565)); the fake omits that field from its validation ([`fake.rs` lines 347-360](https://github.com/aliceisjustplaying/esp32sim/blob/516b1ad5685f065eca9e226902149a4379a76cdd/backend-api/src/fake.rs#L347-L360)). This is a small instance of the duplicated scheduler contract already drifting.

Move request validation into `backend-api` as one canonical function used by every implementation.

#### 25. Medium: CORE is outside the repository's own Rust safeguard baseline

CORE's 58 tests pass, but strict workspace Clippy fails immediately in pre-existing RISC-V code with three denied warnings. BOARD passes formatting, `cargo check`, strict Clippy, debug tests, release tests, and rustdoc warnings through `scripts/pre-commit.sh`. The CORE branch does not contain the final safeguard history.

Rebase CORE over the safeguard head and make the complete script a required check on the integrated commit. Until that happens, isolated green tests do not meet the maintained repository standard.

### Structural cut line

1. Establish one integration head containing safeguards, BOARD, CORE, Wasm, and the restored differential gate.
2. Extract one typed scheduler transaction engine; run its conformance suite against fake and ESP32 hooks.
3. Make decoder-emitted operand/access metadata the single semantic source for interpreter, JIT, and timing.
4. Replace string mutations and repeated receipt objects with typed mutations plus a run-scoped claim catalog.
5. Give boards and devices exact deadlines, then integrate those deadlines with measured scheduling and ledger events.
6. Move GP-SPI/GDMA behavior out of the giant bus and validate hostile descriptor chains with bounded walkers.
7. Gate the actual browser-hosted Wasm product on deterministic framebuffer output and interrupt-driven touch.

### Verification performed

- Fresh remote fetch and commit-date ordering; branch ancestry checked in both directions.
- In-memory trial merge of BOARD and CORE: 30 content-conflicted files.
- CORE `cargo test --workspace --all-targets`: 58 passed, 0 failed.
- CORE strict Clippy with all targets/features and `-D warnings`: failed on 3 denied warnings before reaching the full workspace.
- BOARD `./scripts/pre-commit.sh`: passed formatting, check, strict Clippy, debug/release tests, and rustdoc.
- BOARD `./tools/wasm-build.sh`: passed; produced a 4,985,736-byte `wasm32-unknown-unknown` release module.
- TinyDraw smoke attempt stopped before simulator execution because the existing TinyDraw build directory was configured with ESP-IDF Python 6.0.2 while the active environment was 6.1.0. No user build artifacts were cleaned or changed.
- No source changes were made to either reviewed worktree.


<!-- END reviewer-three.md -->

---

