# Lane BOARD: the exact board and its silicon evidence

Home: esp32sim fork (`board/*` branches off `puck/base`; upstream-shaped
device models branch from `main`) plus tinydraw for probe firmware. This
lane owns the physical board: hardware sessions are maintainer-scheduled
and serial, never opportunistic. A second board, when present, serves
this lane first. Capture and probe work is LOCAL; device modeling
against committed captures is cloud-viable.

Binding: decisions 0014 (naming and capture-first rules) and 0008
(receipt and tier rules). Background, on demand: 0013 (product scope),
the boot-gap receipt
(`../experiments/esp32sim-adoption/README.md`), upstream's JTAG
lock-step flow (`hw/difftest.sh`, `hw/compare.py`), and the probe
methods in tinydraw `calibration/`.

Current state (see `../STATUS.md`): chip identity is captured and
accepted (request record:
`requests/A-01-v2-controller-and-identity.md`). Two independent
8,000-step JTAG
lock-step sessions against upstream esp32sim passed with no PC
divergence, zero timing resynchronizations, and one persistent register
difference at step 15
([receipt](receipts/E-01-jtag-lockstep.md)). The ESP-IDF 6.1 flag day is
complete
([evidence](../timing/evidence/idf61-rebaseline-3db3985/README.md));
one flag day, no toolchain mixing, historical v6.0.2 receipts stay
scoped to their pins. A synchronous GP-SPI board-response hook is pushed
as an upstream-shaped candidate at `lane-a/gp-spi-device-hook`
(`246c699`).

## Device modeling

A `BoardModel` for the Waveshare ESP32-S3-Touch-AMOLED-1.8 at the
maintainer's revision, exactly: CO5300-class QSPI panel device (GRAM, TE
timing, scan-out position), the board's touch controller as an I2C
device (it stays unnamed in every schema until the on-device ID probe
identifies it), QMI8658, PCF85063A, and TCA9554 wiring (the device
model exists upstream). The GP-SPI2 master is already modeled upstream;
do not rebuild it. Modeling proceeds firmware-contract-first per the
demo-first section above; the logic-analyzer capture (about 40 MHz
QSPI, TE, I2C, touch interrupt) gates timing-accuracy claims, not demo
modeling.

Known contracts to satisfy: the firmware's own
`te_edge=rising clock_mhz=40` line, the measured 40 MHz receipts, and
tinydraw's tearing classifiers as acceptance tooling.

Out of scope: measured-mode timing (lane CORE), radio, battery analog,
and the other board revision (interfaces ready, no speculative
branches).

## Silicon evidence service

Everything in this section runs on maintainer-scheduled sessions or
after the demo milestone; the demo outranks all of it. Every capture
becomes a committed, hash-pinned receipt in the established format;
two-boot cohorts for anything adopted; the board is never reflashed
while a request is mid-capture. Open work, in rough order:

1. Diagnose the systematic one-cycle first-line cache probe shift (a
   pooling-probe diagnosis) and commit an adoption disposition; until
   then that cost class stays blocked in every importer.
2. Recover the six receipt identities still below the strict
   two-independent-receipt criterion (two with zero receipts, four with
   one; repeated USB capture truncation).
3. The logic-analyzer bus capture gating panel and touch modeling.
4. Long-window PSRAM cells: strict two-boot assembly before reuse.
5. Arbitration-discrimination and cache store/writeback probe families
   (blocked on reviewed probe code).
6. CCOUNT-delta comparison in the lock-step flow (blocked on lane CORE's
   measured mode).

Exit for device modeling: the TinyDraw gate-harness image boots with the
panel drawing and scripted touch working (`TINYDRAW_LIVE_FAIL` reports
presenter=1 touch=1), with visual and TE acceptance against hardware
captures recorded as receipts. The evidence service does not exit; it
operates, and its milestones are receipts.
