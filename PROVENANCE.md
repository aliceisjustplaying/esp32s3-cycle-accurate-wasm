# Provenance

This repository was extracted on 2026-08-31 from the puck fork,
https://github.com/aliceisjustplaying/puck, branch
`codex/esp32s3-timing-model` at commit
`b10b0e56` (the frozen archive of this project's incubation). The
initial import copied files verbatim with path retargeting; this
repository's own git history records everything that evolved afterward
(the lane collapse, the current-state rewrites). The archive branch
retains the pre-extraction history.

What came from where:

- `decisions/` from the archive's `docs/decisions/` (0006 through 0014;
  0001 through 0005 are puck-the-instrument's own decisions and remain
  only in the archive).
- `research/` from the archive's `docs/research/` (the execution-core
  survey cited by decision 0007).
- `roadmap.md` from `docs/roadmap.md`, `lanes/` from `docs/lanes/`,
  `reviews/` from `docs/reviews/`.
- `STATUS.md` from `experiments/esp32s3-flexe-wasm/STATUS.md`.
- `timing/` from `packs/esp32-s3-touch-amoled-18/timing/` (the timing
  lab: receipts measured on the physical board, evidence, and the
  TypeScript timing machine retained for decision 0014's one-shot
  differential gate).
- `experiments/` from the archive's `experiments/` (the esp32s3 subset).

Related repositories: the product is
https://github.com/aliceisjustplaying/esp32sim (fork of
https://github.com/joakimeriksson/esp32sim, pinned base and branch
conventions in its own `PROVENANCE.md`). Hardware calibration probe
projects live in https://github.com/aliceisjustplaying/tinydraw.
Fixture ELFs referenced by some receipts are machine-local to the
maintainer (tinydraw `out/fixtures/`), a known cloud-lane limitation
recorded in the roadmap.
