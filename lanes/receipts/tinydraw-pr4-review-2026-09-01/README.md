# TinyDraw PR 4 review audit, 2026-09-01

Base: `2643aa7f7b3097300d3cfb002bf7432e299a6d95`

Reviewed and pushed TinyDraw head:

- `76ccfb9` Fix boot timing probe failure semantics
- `0835e5b` Correct core timing calibration contracts

Pull request: <https://github.com/aliceisjustplaying/tinydraw/pull/4>

## Review classification

| Thread | Finding | Result |
| --- | --- | --- |
| CodeRabbit depth 32 | 1,478 conflicts with the linear calculation | Valid documentation ambiguity. Hardware confirms 378,358 / 256 = 1,477.961, which rounds to 1,478. Documented. |
| Cubic depth 32 | Duplicate of CodeRabbit depth finding | Valid duplicate, fixed once. |
| CodeRabbit main-task affinity | Internal software interrupt lifetime can cross cores | Valid. Main task explicitly pinned to CPU0. |
| CodeRabbit RUNS/MARKER | Nonpositive runs and empty marker succeed | Valid. Rejected before opening serial. |
| Cubic RUNS/MARKER | Duplicate of CodeRabbit argument finding | Valid duplicate, fixed once. |
| Cubic usage | Missing or malformed arguments produce traceback | Valid. Stable usage and error exits added. |
| CodeRabbit timestamps | A 50 ms batched read delays timestamps | Valid. Nonblocking reads poll at 1 ms and timestamp received line batches. |
| Cubic timestamps | Duplicate of CodeRabbit timestamp finding | Valid duplicate, fixed once. |
| CodeRabbit missing marker | Missing marker exits successfully | Valid. Every failed run is reported and final exit is nonzero. |
| Cubic missing marker | Duplicate of CodeRabbit failure finding | Valid duplicate, fixed once. |
| Cubic serial open | Default DTR/RTS states apply before assignment | Valid. Port is configured closed, control states set, then opened. |
| Cubic sdkconfig reference | Removed memory-calibration path is cited | Valid. Configuration is described inline. |
| Cubic README reference | Removed memory-calibration path is cited | Valid. Dangling comparison removed. |
| Cubic restore reference | Removed run.sh is prescribed | Valid. Normal Vector V2 restore command documented. |
| Cubic compile flags | C-only optimization flags apply to assembly too | Valid maintenance finding. Flags are scoped to core_timing.c. |
| Cubic ISR boundary | Resume metric does not start at the last ISR instruction | Valid. Metric and notes now name the actual handler-body final timestamp boundary and included tail. |
| CodeRabbit docstring coverage | Add generic docstrings to reach an 80 percent AI threshold | Invalid. The repository has no such policy, and generic coverage text does not improve these C, assembly, and Python probe contracts. |

## Validation

- `./scripts/dev format-check`: pass
- host debug: 31 of 31 pass
- host release: 31 of 31 pass
- ASan/UBSan: 13 of 13 pass
- clean ESP-IDF 6.1 calibration build: pass, zero warning/error matches
- final ELF disassembly: all four loop target residues preserved; r3 remains intentionally unaligned
- boot probe success: three of three observed `CAL_RECORD`, 0.4425 to 0.4439 seconds
- boot probe deliberate missing marker: `firstAppOutput: None`, exit 1
- hardware calibration: `CALIBRATION_DONE`, no crash or failure markers
- normal TinyDraw product restored and reached `TINYDRAW_VECTOR_V2_READY`
- `/dev/cu.usbmodem101` released after capture

## SHA-256

```text
1a394f7e6730bfeedbfd79373956a34773e81e579dd41fdc03867070a8465a75  tinydraw-pr4-boot-probe-failure-2.log
c8ffecdf3ebd1d0a10603705f06a7c36b99480d3a3b8d067e567d6801b8229a9  tinydraw-pr4-boot-probe-success.log
d33e7aefe4c8f42c694dc1f85b60f9fdce664492bef765d69f5e3c10aaff2b12  tinydraw-pr4-calibration-clean-final.log
8f3d3782f8450fc2d5dc8be33e7238bb339df6ea2e33b23d37301190b9b1c599  tinydraw-pr4-calibration-final-disassembly.txt
5a181d7281717f1463d0abdf07d96700d0a10143e3e3f221246a28f83c354718  tinydraw-pr4-calibration-review.log
e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855  tinydraw-pr4-format-check.log
35f23f43e99708f74e932a522a1b214d06cd943335a2e917feb8f6cb9f76f0ae  tinydraw-pr4-host-asan.log
4044f8811c9ff996b404f45d532c90ed6c9bb67b531446a5d55d06b4851fd96a  tinydraw-pr4-host-debug.log
f3ca1a519e128c712b4195cb19a731807eaf180110051923030756149f34f29a  tinydraw-pr4-host-release.log
1750192146b3b0e15d49187250c2cddf07ae23d1ed06dcb76c25b62723f4ab0e  tinydraw-pr4-normal-product-restore-serial.log
8bc8a81a722c850576cc96173839980df204c1bd56d279b77ae76e6fd37f8891  tinydraw-pr4-normal-product-restore.log
```
