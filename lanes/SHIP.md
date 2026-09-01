# Lane SHIP: CI, boundaries, shell, release

Home: esp32sim fork. Cloud-viable. Three workstreams in dependency
order; the first is active now.

Binding: decision 0013 (product identity) and the fail-closed rules in
`AGENTS.md`. Background, on demand: decision 0014 (the adapter owns
validation and quotas), the external review's dispositions and its
"Definition of done for the first credible beta"
(`../reviews/2026-08-31-external/RESPONSE.md` and the archived review),
and, in the puck archive, the original tick-loop findings and donor
armor (`src/abiGuard.ts`, `src/wasiLite.ts`), which inform but do not
bind.

## 1. CI (active)

Current state: built and verified; branches are pushed to the fork
(`lane-g/ci-spec` at `6ba6a6d`, upstream-shaped `lane-g/upstream-ci` at
`3b58cc6`): pinned actions, the Rust fmt/test/clippy matrix, and
mandatory fail-closed decoder conformance corpora with visible case
counts and hashes (10 Xtensa, 9 RISC-V, zero mismatches); deliberate
boundary defects were proven to fail.

The fork-wide rustfmt reformat is approved: apply it as one isolated,
mechanical-only commit on its own branch (no hand edits mixed in), then
land the CI branches on top and configure the fork's required checks.
If required-checks configuration needs repository settings the agent
cannot reach, park that single step and finish the rest. Golden
discipline throughout: semantic assertions and provenance sidecars
accompany fixtures; a conformance test whose corpus is missing fails,
never skips. Nothing beyond this workstream until the coordinator
advances the sequence: boundary review waits for CORE's seam, shell and
release wait for CORE and SPEED.

CI exit: a deliberately injected defect in each boundary fails its
required job on the fork's CI; conformance case counts and corpus
hashes are visible in required logs; a clean checkout reproduces
documented artifacts.

## 2. Boundary review (as lane CORE's seam lands)

Review the primary validated-output seam decision 0014 places inside the
adapter (validation, quotas, bounded construction before `BackendEvent`
creation). Contribute hostile-input tests at that boundary: malformed
artifacts, oversized payloads, quota edges, truncation. When the web
shell exists, harden its surfaces: thin-TypeScript-client input checks
as defense in depth (the Rust seam stays primary), memory-view refresh
after growth, panic-free untrusted paths with typed errors.

Boundary exit: the hostile corpus produces the same typed failure in
every host mode with no crash, hang, allocation spike, or partial
artifact.

## 3. Shell and release (blocked by lanes CORE and SPEED)

The fork-owned thin web UI shell over the versioned Wasm interface,
reusing selected UI and browser pieces from the puck archive with
provenance (decision 0013); the correlation suite passing at
decision-0008 bounds as the release criterion; then the release battery
deferred by the review response: SBOM, attestations, secret scanning,
CSP and isolation headers, branch-policy audit, capability matrix,
docs, publishing.

Release exit: the review's definition-of-done checklist is green, and
every deferred item is either satisfied or explicitly waived by the
maintainer with a recorded reason.
