# Working rules

These rules are the project's culture, carried in the repository so no
external skill or tool configuration is required to follow them.

## Claims and evidence

- Every measured or adopted number carries a receipt: a committed file,
  a hash, or a path into this repository's evidence. No receipt, no
  claim.
- Cost claims use the tiered vocabulary of decision 0008 (exact, affine,
  interval, distribution, unexplained). Refusals name their tier
  candidate.
- Fail closed, always: unknown costs stay unknown and block totals,
  missing corpora fail tests rather than skipping, unsupported
  operations are refused by name, never faked.
- Do not claim "cycle-accurate" without the decision-0008 qualifiers.

## Decisions

- Architectural choices are recorded in `decisions/` as numbered
  markdown records: context, decision, consequences, dated, statused.
- Accepted records are amended with dated notes, never silently
  rewritten. Superseded records stay in place and say what superseded
  them.
- A lane that needs cut or rejected material back proposes a decision
  amendment first, with the concrete need named (see decision 0014's cut
  list for the precedent).

## Scope and simplicity

- No gold-plating: anything not required by a lane's exit criteria or
  its governing decision is out. No speculative config surface, no
  formats for artifacts that do not exist yet, no contracts for
  forbidden implementations.
- TypeScript work is minimized: the product's web shell is a thin
  transport and UI client. No TypeScript execution engine is ever built
  (decision 0013). The product is Rust; C belongs to firmware.

## Where things go

- If a test executes it, it belongs in the esp32sim fork (code, test
  corpora, fixtures the build consumes). If it justifies a claim or a
  decision, it belongs here (receipts, evidence, decisions, reviews).
- Probe firmware belongs in tinydraw. The puck archive receives nothing
  new, ever.

## Process

- One agent per lane, one clone or worktree per agent.
- Granular commits with plain, specific messages; push at milestones.
- Stop and report at your exit criteria, on any blocked decision, or
  when a finding contradicts a decision record. Do not start another
  lane's work.
- The physical board has one owner at a time; hardware requests go
  through the maintainer (lane E). Never open the serial port or JTAG
  opportunistically.
- Upstream-first for esp32sim: fixes and capabilities upstream would
  want are built as upstream-shaped pull requests; the fork carries only
  what upstream declines.
- Update `STATUS.md` when a milestone lands or authority changes.

## Prose

- US English spelling only.
- No em dashes anywhere, including code comments: use commas, colons,
  parentheses, or periods.
- No ASCII art, no badges in markdown.
