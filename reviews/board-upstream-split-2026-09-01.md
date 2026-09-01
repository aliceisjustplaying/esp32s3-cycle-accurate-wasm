# BOARD upstream split, 2026-09-01

Reviewed esp32sim branch `board/tinydraw-v2-maintained` through `b7c9b87`.
This is a classification for maintainer testing and a later upstream decision.
No upstream pull request has been opened.

## Generic esp32sim infrastructure

1. Synchronous GP-SPI device transactions and MISO return. Commit `98be7de`
   adds the generic `BoardModel::spi_transfer` contract, controller phase
   splitting, synchronous completion, MISO word writeback, GPIO-before-SPI
   ordering, IRQ behavior, and tests.
2. GP-SPI2 DMA data delivery. The generic parts of `6938a65` add data offsets
   and lengths, descriptor-chain consumption, GDMA owner/status updates, and a
   controller test. They contain no TinyDraw, Waveshare, CST820, or CO5300
   assumptions.
3. Board-driven GPIO input. The generic parts of `57b397e` add
   `BoardModel::input_changes` and feed those levels into GPIO and interrupt
   state during a machine tick.

The generic lint and formatting work in `daab89f` belongs folded into those
three changes, not offered as an independent feature.

## Reusable device models

- The `Cst820` implementation and identity/coordinate tests in `893b149` are
  reusable. The identity bytes came from captured V2 hardware, not TinyDraw
  behavior.
- The `Co5300` command/window decoder, RGB565 GRAM writes, and tests in
  `6938a65` are a reusable minimal display model. It should move out of
  `board.rs` into a device or display module before an upstream review.

## Waveshare board support

The Waveshare-specific layer is the board composition and wiring in `893b149`
and `6938a65`, the CLI board name, browser layout and touch surface in
`57b397e`, and the device table in `a799c41`. It wires CST820 at `0x15`,
TCA9554 at `0x20`, AXP2101 at `0x34`, PCF85063A at `0x51`, QMI8658 at `0x6b`,
and the CO5300 panel on GP-SPI host 2.

The GPIO13 tear pulse in `57b397e` is an approximate compatibility signal for
the demo. Its 60 Hz cadence is not a measured hardware cadence, phase,
scan-out, or tearing result. Keep it isolated and explicitly approximate, or
withhold it until the analyzer receipt exists.

## TinyDraw-only integration

These pieces are not prerequisites for the generic emulator or board model:

- `scripts/tinydraw-v2.sh` from `81217c0` and `f13fe5f`;
- the product marker checks and crash checks in that script;
- `examples/tinydraw-v2/touch.script` and its multi-sample assertions from
  `1c1d910`;
- the TinyDraw quickstart and paced-stroke prose in `a799c41` and `5f61d5f`.

They can form an optional third-party integration example after the emulator
and board changes.

## Suggested upstream series

1. Generic synchronous GP-SPI and MISO.
2. Generic GP-SPI2 DMA delivery, based on item 1.
3. Generic board-driven GPIO input.
4. Reusable CST820 and CO5300 models and tests.
5. Waveshare AMOLED 1.8 V2 wiring, CLI and browser support, and board docs.
6. Optional TinyDraw launcher and paced-stroke example.

BOARD's emulator implementation is done. TinyDraw `2643aa7` passed the paced
multi-sample browser stroke on esp32sim `b7c9b87`, and the same normal product
source passed on physical hardware. See the
[normal-product receipt](../lanes/receipts/board-tinydraw-v2-normal-2026-09-01/README.md).
The continuing evidence service remains open. Hardware timing claims remain
blocked on a hash-pinned analyzer capture of QSPI, GPIO13 TE, I2C, and GPIO21
touch interrupt with firmware hash, board revision, pin map, operating state,
edge count, and reported timing statistics.
