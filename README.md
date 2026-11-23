# Supermon+64 at $C000

Supermon+64 is a machine-language monitor for the Commodore 64. This repository contains a Kick Assembler build of the Jim Butterfield Supermon+64 V1.2 source, relocated and assembled to start at `$C000`.

The original source repository is [jblang/supermon64](https://github.com/jblang/supermon64).

## Repository contents

- `Supermon64_C000.asm` - relocated Supermon+64 source
- `Supermon64_C000.sym` - assembled symbol file
- `smonc000.prg` - ready-to-run Commodore 64 PRG

The PRG uses `$C000` as its C64 load address. The monitor entry point is also `$C000` (`SYS 49152`).

## Requirements

- Commodore 64, C64 emulator, or C64-compatible emulator
- Kick Assembler with Java
- A disk or other emulated storage medium for loading the PRG

## Building

Install Kick Assembler and Java, then run:

```powershell
java -jar KickAss.jar Supermon64_C000.asm -o smonc000.prg -symbolfile
```

Run the command from the repository directory, or replace `KickAss.jar` and the source path with the full paths on your system.

The source contains:

```asm
.label ORG = $C000
* = ORG
```

The generated PRG has a C64 load-address header of `$00,$C0` and loads to `$C000`.

## Loading and starting

### VICE or another emulator

1. Attach a disk image containing `SMONC000.PRG`, or mount the repository directory as a host filesystem if supported.
2. Enter:

```basic
LOAD "SMONC000",8,1
SYS 49152
```

For a tape or emulator drag-and-drop workflow, load the PRG at its embedded address and use `SYS 49152`.

To call the monitor from BASIC:

```basic
SYS 49152
```

The screen should display `..SYS 49152` before entering the monitor.

### From inside the monitor

Use `X` to return to BASIC through the C64 warm-start vector.

> **Note:** `SYS 49152` only has the intended effect after the PRG has been loaded. Supermon+64 installs a BRK handler and replaces the C64 break-vector setup while active.

## Quick command guide

Enter commands at the Supermon+64 prompt. Addresses and values are normally entered as hexadecimal digits; prefixes such as `$` are optional.

| Command | Syntax | Action |
|---|---|---|
| `A` | `A C000` | Assemble machine code beginning at an address |
| `C` | `C C000 C010 C100` | Compare memory ranges |
| `D` | `D C000 C020` | Disassemble memory |
| `F` | `F C000 C010 00` | Fill a range with a byte |
| `G` | `G C000` | Run from an address |
| `H` | `H C000 CFFF 'TEXT'` | Hunt for an ASCII string |
| `H` | `H C000 CFFF 20 41 54` | Hunt for hex byte values |
| `J` | `J C000` | Call a subroutine and return to the monitor |
| `M` | `M C000 C020` | Display memory as hex and ASCII |
| `R` | `R` | Display saved 6510 registers |
| `T` | `T C000 C010 C100` | Transfer a memory range |
| `X` | `X` | Exit to BASIC |
| `L` | `L "NAME",8,C000` | Load a named file |
| `S` | `S "NAME",8,C000 C100` | Save a memory range |
| `V` | `V "NAME",8,C000 C100` | Verify a named file |

### Common examples

View 32 bytes of memory:

```text
M C000 C020
```

Disassemble 14 bytes from the entry address:

```text
D C000
```

Fill eight bytes with zero:

```text
F C000 C007 00
```

Search for the PETSCII string `TEXT`:

```text
H C000 CFFF 'TEXT'
```

Continue to the next memory line or disassembled instruction by pressing the appropriate monitor command again. Press the C64 STOP key to interrupt a memory dump or search.

### Editing memory

After `M`, Supermon+64 displays a memory line in the form:

```text
> C000 00 01 02 ...
```

Enter a new address and bytes on a memory line, or use `A` to assemble instructions interactively.

## Break and register use

Executing a 6502 `BRK` instruction enters Supermon+64 through the break handler. The monitor displays the saved program counter, status, accumulator, X and Y registers, and stack pointer. The register display can be edited, and `G` resumes execution using those saved values.

## License and provenance

This repository does not add a license to the historical Supermon+64 source or PRG.

The original [jblang/supermon64 repository](https://github.com/jblang/supermon64)
states:

> To the best of my knowledge, this software is in the public domain. I claim no ownership.

> The comments in `supermon64.asm` and `relocate.asm`, as well as the entirety of
> `build.py`, are my own. I hereby place them in the public domain. However, I
> would greatly appreciate attribution if you make use of them.

These statements concern J.B. Langston's modernized comments and build tooling;
they do not provide a formal legal determination of every historical source
component or every contributor's rights.

## Credits

- Original Supermon+64 source: Jim Butterfield
- V1.2 source reformatting and annotations: J.B. Langston
- `$C000` relocation and Kick Assembler build: this project
