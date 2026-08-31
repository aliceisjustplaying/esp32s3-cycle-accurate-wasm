# Lane F: integration and ship

Home: the esp32sim fork (the web shell and release). Blocked by lanes C
and D; last to start.

Binding: decision 0013 (product identity). Background, on demand:
roadmap lane F row; the
external review's "Definition of done for the first credible beta" in
[`../reviews/2026-08-31-external/`](../reviews/2026-08-31-external/)
(adopted as this lane's checklist) and the deferred release-gate items in
its `RESPONSE.md`.

Scope: the fork-owned thin web UI shell over the versioned Wasm
interface, reusing selected UI and browser pieces from the puck archive
with provenance (decision 0013); the correlation suite
passing at decision-0008 bounds as the release criterion; then the
release battery: SBOM, attestations, secret scanning, CSP and isolation
headers, branch-policy audit, capability matrix, docs, publishing.

Exit: the review's definition-of-done checklist is green, every deferred
item either satisfied or explicitly waived by the maintainer with a
recorded reason.
