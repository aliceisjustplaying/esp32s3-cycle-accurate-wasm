# CORE phase 1 recovery brief

This brief governs the fresh CORE agent after the 2026-09-01 rejected exit.
Read [`CORE.md`](CORE.md), decision 0014, and this file before touching code.

## Branch construction

Use a fresh clone of `aliceisjustplaying/esp32sim`. Create
`core/measured-phase1-recovery` from `maintenance/rust-safeguards` at
`b138473`. Replay only the effective CORE work through `8367594`, preserving
the provenance commits from `puck/base`. Do not replay `009b76b` or its revert
`516b1ad`; their net code is intentionally absent. Resolve all safeguard
diagnostics, run `scripts/pre-commit.sh`, and commit that integration before
new functional work.

The effective CORE commits, in order, are `88f526d`, `bab791e`, `8dcf27e`,
`b2aed7f`, `a96a4e0`, `21edb94`, `8d7bc78`, `cdfc693`, `04f6813`, `49fb685`,
`a4340d5`, `fe7cd05`, `e5dea08`, and `8367594`.

## First functional gate

The first new functional result must execute at least one recorded Flexe
boundary through all of these concrete production components:

1. `Esp32SimBackend` with real machine state;
2. the measured interpreter scheduler;
3. `Esp32TimingSource` and the schema-2 importer;
4. the canonical product ledger.

The test must fail if any one of those components is replaced by a fake,
classifier, hard-coded cost table, or test-local timing implementation. A fake
backend remains useful for deterministic adapter and scheduler contracts, but
it cannot provide this result and cannot satisfy the phase exit.

The maintainer authorizes generation of a new hash-pinned replay bundle. The
bundle must contain or deterministically reconstruct every input the real
backend needs at the named boundary, including architectural and relevant
machine state. The existing 1,228 observations and 30 callback records may be
retained as evidence, but their counts are not executable state. Use the
connected device when silicon capture is required, following BOARD hardware
receipt rules.

The existing TypeScript timing machine is a frozen, read-only reference for
decision 0014's one-shot comparison. Do not edit it, port it, reimplement its
rules in Rust, or create new TypeScript execution code. The real measured path
must work before the one-shot comparison is run.

## Review and stop rule

Every commit is small, reviewed against the phase-1 exit, and pushed only after
the full safeguard gate passes. Park only if the real-path proof needs an input
or interface that cannot be derived. State the exact missing bytes, state, or
decision. Phase 2 remains blocked until the production-path differential and
the rest of the phase-1 exit are complete.
