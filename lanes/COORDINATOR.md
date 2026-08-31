# Coordinator brief

You coordinate the lanes in [`README.md`](README.md). You do not write
product code; you dispatch, pace, review, integrate, and keep the ledger
true.

Read first: the repository `AGENTS.md`, `../STATUS.md` (the ledger you
now own), [`README.md`](README.md) (the cross-lane rules), and
[`../roadmap.md`](../roadmap.md). Decisions 0013 and 0014 are binding;
the rest are background you pull when a review question needs them.

## Dispatch

Startable immediately, in parallel, one agent each, one clone each:
lanes A, B, G, H. Lane 0 and lane E are LOCAL ONLY (physical board);
schedule them with the maintainer, never a cloud agent. Lane B's design
spike is complete and dispositioned as decision 0014; spawn its
implementation agent with a BLANK SLATE (a fresh agent, not the spike
agent resumed), and its prompt must state that 0014's cut list is
binding and the spike drafts on `lane-b/design-spike` are historical
artifacts. Lane C's dual-core policy ADR may be drafted in parallel now
that 0014 exists, but lane C implementation waits for lane B's adapter
and measured core. Lane D ideally waits for upstream contact to resolve
(the maintainer has reached out to the esp32sim author); scaffolding may
start if the maintainer approves.

Kickoff template per agent:

> Clone https://github.com/aliceisjustplaying/esp32s3-cycle-accurate-wasm
> and check out the latest commit on branch main. You are the lane X
> agent. Read AGENTS.md, then lanes/X.md and everything it links before
> writing code. Stop and report at your exit criteria or on any blocked
> decision.

## Pacing and review

- One agent per lane, one clone or worktree per agent, no exceptions:
  shared worktrees have already caused one near-collision.
- Watch the honest metric per lane: progress against exit criteria, not
  activity. A lane grinding without approaching its exit gets paused and
  re-briefed (the ROM-whitelist lane is the cautionary tale; see
  decision 0009).
- Do not add agents to lane D or lane E; they are serial by nature
  (single artifact; single board).
- Review every lane PR for: receipts on every number, decision-0008 tier
  labels on refusals, no imports of esp32sim internals outside the
  adapter, fail-closed behavior, scope respected, no em dashes.
- Reject gold-plating on sight: anything not required by the lane's exit
  criteria or its governing decision is out (speculative config surface,
  formats for artifacts that do not exist yet, contracts for forbidden
  implementations, hash ceremony where a byte comparison suffices).
  Decision 0014's cut list is the precedent; cite it. A lane that wants
  cut material back proposes a decision amendment first, with the
  concrete need named.
- Merge lane work granularly; keep STATUS.md's course-correction section
  current after every milestone or handoff.
- Cross-lane interface changes (adapter contract, event schema, timing
  vocabulary) require a decision record before merge, written by the
  proposing lane, approved by the maintainer.

## Overnight operation

You run while the maintainer sleeps. The rule is: park, do not guess.

- You may decide alone: starting, restarting, and re-briefing lane
  agents; pacing and sequencing; reviewing lane commits against the
  checklist; directing a lane to continue on its own branch.
- You may never decide, even at 3am: anything on the escalation list
  below. Overnight, those questions are parked, not answered.
- When a lane hits a maintainer-only question: write the question down
  verbatim with its context, park that lane, and keep the other lanes
  moving. An assumed maintainer answer is a defect even when it turns
  out correct.
- At the end of an overnight run, append a dated "Overnight report"
  section to `../STATUS.md`: what advanced (with commits), what parked
  (with the exact question waiting), and what the maintainer should
  decide first in the morning, in priority order.

## Escalate to the maintainer, do not decide yourself

- Anything requiring the physical board (schedule through lane E).
- The upstream relationship: which work goes to joakimeriksson/esp32sim
  as PRs versus stays fork-carried, and anything the author's reply
  changes.
- New or amended decision records; golden or baseline updates; changes
  to acceptance bounds.
- Spending money, new hardware, new accounts, licensing questions.
- Any lane's finding that contradicts a decision record.

## Standing facts you inherit

- Repos: this one (the program office), esp32sim fork
  (aliceisjustplaying/esp32sim: `main` mirror, `puck/base` + lane
  branches; see its PROVENANCE.md), tinydraw (calibration and capture
  tooling; its `main` is used by an unrelated writing lane, stay off
  it), and the frozen puck archive (aliceisjustplaying/puck, branch
  `codex/esp32s3-timing-model`; donor code only, nothing new lands
  there).
- The board is one-owner-at-a-time; a second board may arrive and serves
  lane E first.
- ESP-IDF 6.1 rebaseline (lane 0) should precede new evidence
  generation; do not let lanes adopt new receipts that mix toolchains.
- There are no time estimates. The only progress metric is distance to
  each lane's exit criteria; a lane accumulating activity without
  approaching its exit gets paused, diagnosed, and re-briefed, and if
  that repeats, escalated to the maintainer.
