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

The phase-1 gate names the flexe boot replay's event accounting on shared traces ([lanes/CORE.md:49](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/lanes/CORE.md:49)). The actual replay regenerates a trace from `DEFAULT_TINYDRAW_ESP32S3_FULL_ELF`, then asserts a hard-coded digest ([full-elf-timing-replay-test.ts:155](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/experiments/esp32s3-flexe-wasm/full-elf-timing-replay-test.ts:155), [full-elf-timing-replay-test.ts:176](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/experiments/esp32s3-flexe-wasm/full-elf-timing-replay-test.ts:176)). The raw trace with digest `e025823c...` is not committed. Only its digest and a summary baseline are present. The fixture ELF is explicitly machine-local ([STATUS.md:79](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/STATUS.md:79)), while the roadmap calls CORE fully cloud-viable and unblocked ([roadmap.md:68](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/roadmap.md:68)).

The gate therefore cannot be reproduced by the blank-slate CORE agent described in STATUS. Commit a bounded neutral trace with its exact producer provenance, or select an already committed trace as the gate corpus. The manifest must identify the precise input bytes, producer version, digest algorithm, expected projection, and comparison implementation.

#### 3. Blocker: the one-shot ledger comparison has incompatible cost semantics

Decision 0014 rejects schema-1 `timing.json` because it flattens the affine MMIO model `3n - 8`, and it blocks first-line cache costs pending diagnosis ([decision 0014:64](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/decisions/0014-measured-scheduler-and-adapter-contract.md:64)). Yet the required TypeScript oracle still marks cache-line fills calibrated and stores first-line values 204, 115, and 82 ([timing/timing.json:52](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/timing/timing.json:52)). It also represents same-value writes as scalar `3` cycle entries ([timing/timing.json:99](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/timing/timing.json:99), [timing/timing.json:136](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/timing/timing.json:136)).

The full-ELF replay converts those first-line values to calibrated known costs ([full-elf-timing-replay-test.ts:101](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/experiments/esp32s3-flexe-wasm/full-elf-timing-replay-test.ts:101)) and explicitly requires three known 204-cycle fills ([full-elf-timing-replay-test.ts:542](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/experiments/esp32s3-flexe-wasm/full-elf-timing-replay-test.ts:542)). A conforming schema-2 Rust ledger must preserve the affine cost and block the disputed first-line costs, so byte-for-byte ledger comparison would require Rust to reproduce semantics its governing decision forbids.

Define the differential projection before implementation. The old oracle can validate stable event identity, order, address classification, and count. Schema-2 tests must independently validate tiered costs, affine terms, receipt binding, and blocked totals. Never compare cost claims that the donor format cannot represent correctly.

#### 4. High: every evidence path in the canonical timing profile is stale

All six evidence fields in `timing/timing.json` point into the removed `packs/esp32-s3-touch-amoled-18/timing/evidence/` tree, for example [timing/timing.json:16](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/timing/timing.json:16), [timing/timing.json:54](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/timing/timing.json:54), and [timing/timing.json:161](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/timing/timing.json:161). The matching basenames are under `timing/evidence/`, but none of the recorded paths resolves. The runtime report also emits the obsolete pack path as `PROFILE_SOURCE` ([runtime-report.ts:20](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/timing/runtime-report.ts:20)).

This violates the repository rule that every measured claim has a committed path into this repository ([AGENTS.md:16](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/AGENTS.md:16)). Retarget the references and make the gate manifest verify path existence plus content hashes. A profile parser that accepts arbitrary evidence strings is not an evidence boundary.

#### 5. High: the QEMU oracle fixture presents synthetic provenance as an external receipt

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

The product claims the complete ESP32-S3 SoC ([roadmap.md:5](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/roadmap.md:5)), and decision 0013 says radio and omitted blocks are deferred, not excluded ([decision 0013:40](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/decisions/0013-product-identity-fork-owns-the-product.md:40)). The definition of done covers boot, dual core, board devices, speed, and timing bounds, but does not enumerate remaining SoC capabilities ([roadmap.md:21](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/roadmap.md:21)). BOARD explicitly excludes radio ([lanes/BOARD.md:84](/Users/sarah/src/a/esp32s3-cycle-accurate-wasm/lanes/BOARD.md:84)), and no current lane owns the deferred SoC remainder.

Publish a capability matrix with an owner and acceptance test for every claimed block, or narrow the product claim to the supported SoC subset. “Complete” cannot remain an untestable future adjective outside the lane plan.

#### 11. Medium: the retired TypeScript runner is a second execution stack disguised as a fixture

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

