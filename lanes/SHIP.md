# Lane SHIP: boundaries, shell, release

Home: esp32sim fork. Two workstreams in dependency order; neither is
dispatched until lane CORE's seam exists.

Binding: decision 0013 (product identity) and the fail-closed rules in
`AGENTS.md`. Background, on demand: decision 0014 (the adapter owns
validation and quotas), the external review's dispositions and its
"Definition of done for the first credible beta"
(`../reviews/2026-08-31-external/RESPONSE.md` and the archived review),
and, in the puck archive, the original tick-loop findings and donor
armor (`src/abiGuard.ts`, `src/wasiLite.ts`), which inform but do not
bind.

The tree is rustfmt-clean; keep it that way (`cargo fmt --check`,
`cargo clippy`, `cargo test` before pushing). GitHub CI and the
decoder-conformance material on the `lane-g/ci-spec` and
`lane-g/upstream-ci` branches belong to the release workstream; leave
those branches untouched until then.

## 1. Boundary review (as lane CORE's seam lands)

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

## 2. Shell and release (blocked by lanes CORE and SPEED)

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
