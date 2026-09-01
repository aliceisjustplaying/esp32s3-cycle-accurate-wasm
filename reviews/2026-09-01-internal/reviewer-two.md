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

`machine.ts:958` and `calibration.ts:720,779` order canonical JSON
content with localeCompare, which is ICU-collation dependent. The
gate's receipt handle is one SHA-256 over canonical ledger bytes.
Until these become codepoint comparisons (the codebase already owns
two: lexicalCompare in execution.ts, compareText in
calibration-report.ts), any ledger hash is a property of the host's
ICU version. Small, behavior-preserving fix, no decision needed, land
before the gate.

## A4. The extraction from puck left the program office's own tooling red, contradicting the fail-closed rule

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

### F3 (High, measurement semantics): flash_instruction_cold is not cold, it is first-64-bytes-cold

- Receipt: `timing_probe.cpp:959-967` (prepare_flash_instruction_cold)
  invalidates `[aligned_start, address + 64)` of flash_instruction_body,
  which is 256 iterations of four inlined nop plus ALU work, several
  KB of code. Samples 2 through 100 of the flash_instruction_cold
  measurement therefore execute a body that is cold only in its first
  two I-cache lines and hot everywhere else, because each sample runs
  the whole body once and only 64 bytes get re-invalidated.
- The id string `flash_instruction_cold` (measurement table,
  `timing_probe.cpp:1954-1956`) over-claims what was measured. The
  STATUS.md note about a retained "systematic one-cycle first-line
  cache probe shift" suggests the effective behavior is known, but the
  artifact name does not say it and sample records carry no note field
  to warn the importer.
- Remedy: rename the identities to say what they measure (for example
  flash_instruction_first_lines_cold) or invalidate the full body
  (compute the end from the symbol size, the same pattern the 41-byte
  RGB565 oracle already uses, where 2 lines genuinely cover the
  target).

### F4 (Medium): Tick protection is inconsistent across measurement classes, and the receipts do not say so

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

1. Fix the extraction defects (A4): profile path resolution, the
   dangling puck import, README and usage-string paths, a package.json
   with typecheck and test scripts. The timing lab must be green
   before anything compares against it.
2. Determinism fix (A3): replace localeCompare at the three named
   sites with the codepoint comparators the codebase already owns.
3. Reproducibility of the receipt chain (A5): merge 3db39856 into
   tinydraw main, repoint the fixture worktree, record pinned-ELF
   commit provenance, and add the committed ELF verification step
   (tinydraw F1).
4. Parked decisions to make at the freeze's end (A1, A2, tinydraw F7):
   the gate's comparison domain, the schema-1/affine divergence
   disposition, and capture-rerun semantics.
5. Fork pre-CORE pass (fork findings 1, 2, 3, 7): split periph.rs into
   periph/, extract host concerns from machine.rs behind a hook seam,
   typed errors instead of Result<_, String>, make the objdump gates
   fail closed on missing corpora, and write the interpreter-versus-JIT
   differential test.
6. Probe hygiene that the planned first-line-cache diagnosis should
   absorb (tinydraw F3, F4, F6): honest identity names, uncertainty
   notes in metadata, documented priming counts.
7. Documentation truthing (A6, A9, fork finding 9): mark the
   dependency lint as a target state or add the lint; record the
   em-dash question for the fork as an explicit convention decision;
   add the root LICENSE to the fork.

# Part 6: overall verdict

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
