# Lane SPEED: the wasm JIT backend (formerly lane D)

Home: esp32sim fork, upstream-shaped: branch from `main`, built as a
contribution upstream's own roadmap asks for (its item 4 ends with "the
wasm backend for the browser build"). This lane does not wait on
upstream: contact with the author is open as a courtesy, and if he
replies, scope is coordinated; otherwise the work proceeds here,
upstream-shaped so it can be offered later. Dispatch order is set by
the coordinator's sequence.

Binding: decision 0012 (the interpreter is mandatory everywhere; JIT
observation is gated by the cross-mode conformance program). Background,
on demand: decision 0010 (the real-time requirement and its measured
basis), the browser-speed receipts
(`../experiments/esp32s3-browser-speed/README.md`: 109 MIPS interpreted
in V8, about 4,600 emulated-MIPS JIT ceiling, negligible block-compile
cost), upstream `docs/speed-plan.md`, and the existing AArch64 JIT in
`xtensa-lx7/src/jit/`.

Scope: runtime wasm codegen for guest basic blocks in the browser
build, preserving upstream's invariant that `--no-jit` output is
bit-identical. Design for later measured-mode participation (inlined
accounting seams), but correctness and speed first.

Constraints: re-measure against the browser-speed probes as guards
accrue; decision 0010's checkpoint applies (a first measured version
under about 500 emulated MIPS stops the lane for profiling before
features); performance gates run on the maintainer's M1-class machine,
cloud numbers are directional only.

Exit: browser real time on the hot-path workload (the boot that
currently runs at about 65 Minsn/s interpreted); bit-identity with the
interpreter across the conformance corpus; numbers recorded with
receipts.
