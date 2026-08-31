# Lane H: boundary review

Home: the esp32sim fork. Scope folded per decisions 0012, 0013, and
0014: hardening lands where third-party material actually executes, and
the product's untrusted-input seam is the adapter, not a separate layer.

Work, in order:

1. Review lane B's primary validated-output seam as it lands: decision
   0014 places guest-output validation, quota enforcement, and bounded
   construction inside the adapter before `BackendEvent` creation.
   Hostile-input tests at that boundary (malformed artifacts, oversized
   payloads, quota edges, truncation) belong here.
2. When lane F builds the product's public web shell, harden its
   surfaces: input validation on the thin TypeScript client (defense in
   depth, the Rust seam remains primary), memory-view refresh after
   growth, panic-free untrusted paths with typed errors.

Background reading in the puck archive (`aliceisjustplaying/puck`,
branch `codex/esp32s3-timing-model`): the original tick-loop findings
(`docs/findings-first-adversarial-pass.md`) and the donor armor
(`src/abiGuard.ts`, `src/wasiLite.ts`), which inform but do not bind the
fork's implementation.

Exit: the hostile corpus produces the same typed failure in every host
mode with no crash, hang, allocation spike, or partial artifact.
