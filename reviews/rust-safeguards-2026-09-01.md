# Rust safeguard report, 2026-09-01

Repository: `aliceisjustplaying/esp32sim`

Branch: `maintenance/rust-safeguards`

Reviewed head: `b138473`

## Result

The fork pins Rust 1.98.0, keeps edition 2021, applies the requested rustfmt
policy, and inherits the strict workspace lint policy in all seven members.
Release builds retain overflow checks and debug assertions. The executable
`scripts/pre-commit.sh` runs formatting, check, strict Clippy, debug tests,
release tests, and warning-denied rustdoc with locked dependencies.

The full script passed from the repository root and by absolute path from the
program-office checkout. Debug and release tests both passed. The dedicated
release-profile test proves that debug assertions remain enabled and integer
overflow traps in the tested release profile. There are no compiler, Clippy,
rustdoc, unfulfilled-expectation, or undocumented lint-exception diagnostics.
No dependency, edition, GitHub Actions, or other CI change was made.

The requested `clippy::transmute_int_to_ptr` name does not exist in Rust
1.98.0. The policy uses rustc's `integer_to_ptr_transmutes` lint for the same
defect class.

Lint remediation preserved intentional wrapping arithmetic, narrowing casts,
index-based hardware tables, exact floating point, raw TLB pointers, JIT code,
and the WASM ABI. It also exposed and fixed a real NTP fractional-second
precedence error: the fraction is now `(nanoseconds << 32) / 1_000_000_000`.
Unreachable duplicate match cases were removed after inspection.

## Scoped `expect` annotations

- `esp32s3/src/bus.rs`: software TLB pointer into its owned buffer.
- `esp32s3/src/nat.rs`: the complete UDP datagram fields require the argument
  count.
- `wasm/src/lib.rs`: the C ABI exchanges owned buffers and emulator handles as
  raw pointers.
- `xtensa-lx7/src/block.rs`: a JIT call needs stable raw CPU-state pointers.
- `xtensa-lx7/src/bus.rs`, two annotations: `Send` and `Sync` for synchronized
  software-TLB raw buffer pointers.
- `xtensa-lx7/src/decode.rs`: the decoder constructor mirrors compact hardware
  instruction fields.
- `xtensa-lx7/src/jit/mod.rs`, native: executable memory and generated-code raw
  pointers.
- `xtensa-lx7/src/jit/mod.rs`, portable: preserves the native JIT's unsafe
  public contract.

Three pre-existing scoped `allow` annotations also have explicit reasons:
Xtensa mnemonic variant spelling, the complete JIT emitter argument sets, and
the platform-specific AArch64 encoder surface. An obsolete dead-code shim was
removed.

## Commits

- `c6463b5` begin strict Rust quality remediation and workspace formatting
- `88b88a5` web server diagnostics
- `fa3a1ce` crypto diagnostics
- `dd3d8f9` device and image diagnostics
- `205b04e` peripheral diagnostics
- `133ee84` ESP32-C3, CLI, and WASM diagnostics
- `6bab3b2` timing and network diagnostics
- `a7533f5` rustdoc diagnostics
- `04f1d38` release-safety verification
- `b138473` document remaining scoped exceptions

## Changed files

Root: `Cargo.toml`, `README.md`, `clippy.toml`, `rust-toolchain.toml`,
`rustfmt.toml`, `scripts/pre-commit.sh`.

Manifest inheritance: `cli/Cargo.toml`, `cli-c3/Cargo.toml`,
`esp32c3/Cargo.toml`, `esp32s3/Cargo.toml`, `riscv-rv32/Cargo.toml`,
`wasm/Cargo.toml`, `xtensa-lx7/Cargo.toml`.

CLI and WASM: `cli/src/main.rs`, `cli-c3/src/main.rs`, `wasm/src/lib.rs`.

ESP32-C3: `esp32c3/src/bus.rs`, `esp32c3/src/machine.rs`,
`esp32c3/src/periph.rs`.

ESP32-S3: `esp32s3/src/board.rs`, `esp32s3/src/bus.rs`,
`esp32s3/src/crypto.rs`, `esp32s3/src/elf.rs`, `esp32s3/src/host.rs`,
`esp32s3/src/i2c.rs`, `esp32s3/src/image.rs`, `esp32s3/src/lib.rs`,
`esp32s3/src/machine.rs`, `esp32s3/src/nat.rs`, `esp32s3/src/net.rs`,
`esp32s3/src/periph.rs`, `esp32s3/src/picture.rs`, `esp32s3/src/web.rs`,
`esp32s3/src/wifi.rs`.

RISC-V: `riscv-rv32/src/bus.rs`, `riscv-rv32/src/decode.rs`,
`riscv-rv32/src/disasm.rs`, `riscv-rv32/src/exec.rs`,
`riscv-rv32/src/state.rs`, `riscv-rv32/tests/objdump_diff.rs`.

Xtensa: `xtensa-lx7/src/block.rs`, `xtensa-lx7/src/bus.rs`,
`xtensa-lx7/src/decode.rs`, `xtensa-lx7/src/disasm.rs`,
`xtensa-lx7/src/exec.rs`, `xtensa-lx7/src/jit/a64.rs`,
`xtensa-lx7/src/jit/mod.rs`, `xtensa-lx7/src/lib.rs`,
`xtensa-lx7/src/pie.rs`, `xtensa-lx7/src/pie_table.rs`,
`xtensa-lx7/src/state.rs`, `xtensa-lx7/tests/objdump_diff.rs`, and
`xtensa-lx7/tests/release_profile.rs`.
