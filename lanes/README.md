# Lane briefs

One file per roadmap lane. The complete handoff to an implementing agent
is: a checkout of this repository (branch `main`),
one lane letter, and the instruction to read that lane's brief and
everything it links before writing code.

Read order for every lane: the repository `AGENTS.md`, then
[`../roadmap.md`](../roadmap.md) (revision 4),
then the decisions it cites (0008 through 0014), then your brief.

## Repositories

- **this repo**: `aliceisjustplaying/esp32s3-cycle-accurate-wasm`,
  branch `main`. The program office: decisions, roadmap, lane briefs,
  reviews, timing lab and receipts, experiment records.
- **esp32sim fork**: `aliceisjustplaying/esp32sim`. `main` is a clean
  upstream mirror, never committed to. `puck/base` is the pinned base
  (`2114ffc`) plus fork-carried commits; see its `PROVENANCE.md` for the
  branch conventions. Lane work branches: `lane-<letter>/<topic>`, from
  `puck/base`, or from `main` when the work is an upstream-shaped pull
  request.
- **tinydraw**: `aliceisjustplaying/tinydraw`. Calibration probe projects
  and capture tooling only.
- **puck archive**: `aliceisjustplaying/puck`, branch
  `codex/esp32s3-timing-model`, frozen. The project's incubator and the
  donor codebase (UI, recorder, harness); read-only, ported from
  selectively with provenance per decision 0013.

## Rules that bind every lane

- Every measured or adopted number carries a receipt (file path or
  committed evidence); refusals name their decision-0008 tier candidate.
- The physical board has one owner at a time. Lanes that need hardware
  file a request with the maintainer; they do not open the serial port or
  JTAG opportunistically.
- Fail closed: unknown costs stay unknown, missing corpora fail tests,
  unsupported operations are refused, never faked.
- Upstream-first for esp32sim changes upstream would want; the fork
  carries only what upstream declines.
- No em dashes in this repository's files. TypeScript for puck tooling,
  Rust for the emulator, C only for firmware.
- Stop and report at your exit criteria, on any blocked decision, or when
  a finding contradicts a decision record. Do not start another lane's
  work.
- Update `../STATUS.md` when your lane
  reaches a milestone or hands off.

## Lanes

| Lane | Brief | Home |
| --- | --- | --- |
| 0 | [0.md](0.md) | tinydraw + this repo (LOCAL ONLY, needs the board) |
| A | [A.md](A.md) | esp32sim fork |
| B | [B.md](B.md) | esp32sim fork |
| C | [C.md](C.md) | esp32sim fork |
| D | [D.md](D.md) | esp32sim fork (upstream-shaped) |
| E | [E.md](E.md) | tinydraw + esp32sim fork (board-owner lane) |
| F | [F.md](F.md) | esp32sim fork (web shell) |
| G | [G.md](G.md) | esp32sim fork |
| H | [H.md](H.md) | esp32sim fork, after lane B's seam |
