# Lane briefs

Four lanes, one file each. The complete handoff to an implementing agent
is: a checkout of this repository (branch `main`),
one lane name, and the instruction to read that lane's brief and
everything it links before writing code.

Older documents (decisions, reviews, receipts) reference lanes by
letter; the letters map to the current lanes as: 0, A, E to BOARD; B, C
to CORE; D to SPEED; F, G, H to SHIP. The `requests/` and `receipts/`
directories here hold committed hardware-session records.

Read order for every lane: the repository `AGENTS.md`, then
[`../STATUS.md`](../STATUS.md), then your brief and the decisions it
names as binding. The roadmap and remaining decisions are background,
pulled on demand.

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
| CORE | [CORE.md](CORE.md) | esp32sim fork; measured execution, phases 1 and 2 |
| BOARD | [BOARD.md](BOARD.md) | esp32sim fork + tinydraw; the physical board, its devices and evidence (captures are LOCAL) |
| SPEED | [SPEED.md](SPEED.md) | esp32sim fork, upstream-shaped; waits on upstream contact |
| SHIP | [SHIP.md](SHIP.md) | esp32sim fork; CI now, boundary review with CORE, shell and release last |
