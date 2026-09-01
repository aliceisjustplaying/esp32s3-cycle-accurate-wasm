# BOARD TinyDraw V2 browser demo receipt

Date: 2026-09-01
Owner: lane BOARD
Board: Waveshare ESP32-S3-Touch-AMOLED-1.8 V2
Port: `/dev/cu.usbmodem101`

## Result

The ESP-IDF 6.1 TinyDraw gate-harness image booted in esp32sim at
interpreter speed with the Waveshare V2 board model. The CO5300 panel
displayed the TinyDraw canvas and controls in the browser. A scripted browser
drag traversed the modeled CST820 touch device, produced a visible blue stroke,
and committed it through the live TinyDraw presenter.

The acceptance output for that stroke was:

```text
TINYDRAW_LIVE_STROKE revision=5616 operations=103 samples=2709 append_us=3 publication=operation refresh_compose_us=0 refresh_complete_us=0 ink_samples=3 read_submit_avg_us=2049 read_submit_max_us=6086 read_complete_avg_us=3658 read_complete_max_us=6837 submit_over_16ms=0 complete_over_33ms=0 presentation_failures=0 poll_max_us=6958 touch_errors=0 touch_overflows=0 touch_resyncs=0 touch_moves_coalesced=0 touch_events=22 touch_down=1 touch_up=1 touch_events_ge_8ms=0 touch_event_age_max_us=970 free_psram=949652 largest_psram=933888 authority_match=0
TINYDRAW_LIVE_STROKE_DONE committed=1 refresh=1 commit_failed=0
```

The complete automated run reached:

```text
TINYDRAW_VECTOR_V2_GATE_HARNESS_DONE pass=1
TINYDRAW_VECTOR_V2_READY ... te_edges=2896 te_period_us=16667 te_high_us=8334 ...
```

## Model provenance and limits

- esp32sim repository: `aliceisjustplaying/esp32sim`
- esp32sim branch: `board/tinydraw-v2-demo`
- esp32sim commit: `2c0b32088d0f40d4589946bc4577d69dcd3b96d1`
- TinyDraw source branch: `codex/lane-0-idf61-probes`
- TinyDraw fixture source: `632c966075de978d266f8ba53bde465961e1a201`
- ESP-IDF: `v6.1.0`
- execution: interpreter mode, `--no-jit`

The board model covers the TCA9554 wiring, AXP2101, PCF85063A, QMI8658,
CST820 identity and reports, GP-SPI2 GDMA transfers, CO5300 commands and GRAM,
browser touch, and the GPIO 13 TE input.

The TE implementation toggles GPIO 13 each `CPU_HZ / 120` cycles, producing a
60 Hz rising-edge cadence. This is a non-timing demo cadence used to exercise
the firmware's rising-edge contract. It is not an adopted hardware timing
number and makes no scan-out or tearing-accuracy claim. The logic-analyzer
capture required by the BOARD brief remains open before any timing-accuracy
claim.

The BOARD exit wording says successful exit is reported by
`TINYDRAW_LIVE_FAIL presenter=1 touch=1`. A passing run emitted the positive
`TINYDRAW_LIVE_STROKE` and `TINYDRAW_LIVE_STROKE_DONE` records above and did
not emit a `TINYDRAW_LIVE_FAIL` record. This literal mismatch is recorded for
maintainer clarification; the explicit visible-drawing and scripted-touch demo
milestone passed.

## Emulator command

```text
target/release/esp32sim --boot rom --flash-image gate-harness-physical-flash-16MiB.bin --elf tinydraw_esp32.elf --elf bootloader.elf --efuse-regs openocd-efuse-strap.log --regs-init openocd-reset-regs.log --strap 2b --board waveshare-amoled18-v2 --flash-mb 16 --psram-mb 8 --no-jit --console usb --no-dump --web 8123 --web-dir web
```

The browser touch was injected after `TINYDRAW_VECTOR_V2_READY` with an
eight-point drag from `(105,185)` through `(285,320)` in viewport coordinates.

## Verification

```text
cargo test -p esp32s3
17 passed; 0 failed

cargo build --release -p esp32sim
finished release build successfully
```

## Hardware session close

The physical port was enumerated immediately before the final flash. The
required IDF 6.1 gate-harness image was restored with:

```text
eim run "python -m esptool --chip esp32s3 --port /dev/cu.usbmodem101 --baud 460800 write-flash 0x0 gate-harness-idf61-merged.bin" v6.1
```

esptool identified ESP32-S3 revision v0.2, MAC `1c:db:d4:7b:85:c8`, wrote
797088 bytes, verified the data hash, and reset the board. The follow-up raw
serial capture booted `ESP-IDF v6.1`, detected 16 MB flash and 8 MB PSRAM,
started `tinydraw_esp32`, and reached a passing startup presentation with
`tear_edge_observed=1`, `te_edge=rising`, and `clock_mhz=40`. The port is
released with the gate harness running.

## Artifact hashes

```text
f2a8856a69b41ff6b5d88e060ca4067cff34517d7bd1a9efda3c60aab221656c  gate-harness-physical-flash-16MiB.bin
4e121a3642a6f18766cfe96c2be6adc8a0017fba4afa82105d642168ea40e2c8  tinydraw_esp32.elf
16d2df25336b28d4bfeb74e184ef46b24e1309f720c20a782e5e2944d9e2a3c3  bootloader.elf
a5b94aa0a2820e98231e5d5cbd32449835359993a128134c59d82c47e735041c  openocd-efuse-strap.log
cbd82a689f34c583f2ab6ce966caed0a23fcc6feb4b8d9144b2030768b00b107  openocd-reset-regs.log
02b9ab3ee0ea71ba49c946154cea9eb17ef109198274707a0e3e4bc524a1dab5  gate-harness-idf61-merged.bin
5494eade5a47408e334269f67dacf046189dbd67813a1b8f0245b8a6cd09abad  restore-flash.log
a403170662e3431269d386b00bb08e2e51c2fc90b4ec152fdcaf2f4c199771f2  restore-serial.log
```

The firmware and emulator input artifacts remain machine-local. The hardware
restore logs are committed beside this receipt.
