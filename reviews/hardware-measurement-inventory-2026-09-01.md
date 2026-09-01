# Hardware measurement inventory, 2026-09-01

Goal: front-load useful silicon and board evidence before longer emulator work
so later agents can work from committed, hash-pinned receipts without needing
the device.

This is an inventory and proposed capture order. No new capture has started.

## Already complete, do not repeat without a concrete reason

- Chip, flash, efuse, strap, reset-state, board revision, and touch-controller
  identity. The V2 touch controller returned CST820 identity bytes.
- Two independent 8,000-step architectural JTAG lock-step sessions with no PC
  divergence.
- The ESP-IDF 6.1 timing rebaseline: 802 valid receipts across 210 identities,
  with 204 identities meeting the strict two-boot rule.
- Conditional branch, hot instruction and data cache hit, cold flash and PSRAM
  burst, MMIO, ROM callback, and reset-state cohorts already recorded in the
  timing evidence tree.
- Normal TinyDraw product boot and stability on the physical board.

## Can capture with the connected board and existing or reviewed PR assets

1. **Close the six IDF 6.1 receipt gaps.** Run additional independent boots of
   the existing strict timing image until the six named RTC and reset-reason
   identities have at least two complete receipts, or preserve a reproducible
   transport failure. The existing firmware has no selective rerun mode, so
   these are full-suite boots.
2. **Complete the core-timing IDF 6.1 cohort.** TinyDraw pull request 4 review
   fixes landed at `0835e5b`, and one complete hardware validation boot is
   archived in the program office. Capture at least one more independent boot
   of window overflow and underflow, straight-line issue, loop alignment, and
   dispatcher interrupt entry and resume. Preserve raw logs, ELF, sdkconfig,
   objdump verification, toolchain versions, and hashes. The older written
   cohort is from IDF 6.0.2 and its raw logs are not in the pull request.
3. **Boot-to-product markers.** Use the corrected boot-time probe for a reset
   cohort against the normal product, retaining each reset-to-ROM,
   reset-to-bootloader-entry, reset-to-READY, and reset-to-LIVE_SETTLE result.
   Thirty successful resets gives a useful distribution without treating it
   as an acceptance bound.
4. **Diagnostic TE telemetry across cold boots.** Preserve the normal
   product's internally measured `te_period_us` and `te_high_us` values across
   the same reset cohort. This is useful diagnostic evidence, but interrupt
   timestamp latency means it does not replace an electrical analyzer capture.
5. **PSRAM long-window disposition.** First assemble the existing IDF 6.1
   `psram_cold_sequential` and `psram_cold_random` cells offline. Repeat on
   hardware only if fewer than two eligible boots survive for any required
   single-core or contended cell.

## Needs reviewed probe code before capture

Build one unified timing image where practical, then capture at least two
clean independent boots:

- matched internal-only, flash, and PSRAM arbitration aggressors with a start
  barrier, aggressor progress counts, and attributable cache counters;
- an isolated hot external-cache store-hit probe;
- matched clean-versus-dirty writeback ladders for 1, 2, 4, 8, and 16 lines,
  with post-write verification;
- instruction-PSRAM hot and cold fetch probes;
- a focused first-line cache pooling probe to explain the systematic one-cycle
  IDF 6.1 shift;
- a selective cohort mode for the six RTC and reset identities so future USB
  truncation recovery does not require another full timing suite;
- an optional normal-product DMA descriptor and submission marker hook to
  correlate internal GP-SPI2/GDMA activity with a later electrical capture.

Probe source, ELF instruction windows, cache-state preconditions, receipt
schema, and offline analysis must be reviewed before flashing. Captured values
remain candidates until separately adopted under decision 0008.

## Needs an external logic analyzer and physical interaction

The connected USB board is visible at `/dev/cu.usbmodem101`, but no Saleae,
DSLogic, sigrok-compatible analyzer, or analyzer application is currently
detected on this host.

When a suitable analyzer is attached and wired, use the existing A-01 request
to capture ten signals simultaneously:

- QSPI chip select, clock, and four data lines;
- GPIO13 panel TE;
- I2C SDA and SCL;
- GPIO21 touch interrupt.

The analyzer must resolve the approximately 40 MHz QSPI bus. Capture cold reset
through panel initialization and one complete known frame, at least 120 TE
rising edges, decoded I2C, and the normal product's serial markers. Preserve
the native analyzer session and raw export.

Touch evidence additionally needs held contacts at four corners and center,
a slow diagonal stroke, releases, landmark notes or photos, and at least two
controller polls per hold. This part needs a person or a physical fixture; an
agent cannot honestly manufacture those landmarks from USB alone.

## Blocked until emulator work exists

- CCOUNT-aware hardware versus emulator lock-step needs CORE's measured-mode
  comparison interface. The current lock-step deliberately resynchronizes
  timed loops and proves architectural agreement only.
- A true mid-execution hardware checkpoint for a differential would need a
  defined capture mechanism for complete architectural and relevant machine
  state. The preferred CORE recovery path should boot current esp32sim
  deterministically from reset and avoid making such a hardware snapshot a
  prerequisite.

## Proposed hardware-agent stopping point

After maintainer approval, one fresh BOARD evidence agent should:

1. verify all existing artifact hashes and perform the offline PSRAM assembly;
2. finish the TinyDraw PR-dependent core-timing and boot-probe validation;
3. capture the immediately available IDF 6.1 gap, core-timing, boot, and TE
   diagnostic cohorts;
4. commit raw logs, normalized receipts, provenance, and a coverage ledger;
5. report the exact remaining gaps;
6. stop before inventing new probe code, analyzer evidence, manual touch
   landmarks, or CORE interfaces.

A later explicitly approved probe-development agent can prepare the unified
image. A later hardware session can then capture that image in one batch.
