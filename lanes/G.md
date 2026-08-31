# Lane G: CI as the executable specification

Home: the esp32sim fork's workflows, with an upstream-shaped variant
prepared for contribution. Cloud-viable.

Current state: largely built and locally verified (see
[`../STATUS.md`](../STATUS.md)): local fork branches `lane-g/ci-spec`
and upstream-shaped `lane-g/upstream-ci` carry pinned actions, the
Rust fmt/test/clippy matrix, and mandatory fail-closed decoder
conformance corpora with visible case counts and hashes; deliberate
boundary defects were proven to fail. Nothing is pushed yet.

Binding: fail-closed conformance per decision 0014 and the rules in
AGENTS.md. Background, on demand: roadmap lane G row; review findings F-047, F-048, F-052,
F-053, F-054 and their dispositions in
[`../reviews/2026-08-31-external/RESPONSE.md`](../reviews/2026-08-31-external/RESPONSE.md).

Remaining work:

1. The four branches live only in the maintainer's local working tree
   and are not on the fork remote; your first action is to request that
   the maintainer push them (escalate through the coordinator, do not
   rebuild the work).
2. Await the maintainer's disposition on the pre-existing fork-wide
   rustfmt debt (893 hunks across 39 files at base `aa851249`); lane G
   formats what it touches but does not rewrite the tree unilaterally.
3. Once pushed and dispositioned, open the fork's required-checks
   configuration.
4. Golden discipline everywhere: semantic assertions and provenance
   sidecars accompany fixtures; a conformance test whose corpus is
   missing fails, never skips.

Exit: a deliberately injected defect in each boundary fails its required
job on the fork's CI; conformance case counts and corpus hashes are
visible in required logs; a clean checkout reproduces documented
artifacts.
