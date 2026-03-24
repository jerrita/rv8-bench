# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

rv8-bench is a benchmark suite for regression testing of the rv8 binary translation engine. It contains integer-centric C/C++ programs compiled for multiple architectures to compare performance across RISC-V, x86, and ARM platforms.

## Building

Build all benchmarks for all architectures:
```bash
make
```

Clean build artifacts:
```bash
make clean
```

### Build Requirements

The Makefile requires cross-compilation toolchains:
- `riscv32-linux-musl-*` and `riscv64-linux-musl-*` for RISC-V
- `i386-linux-musl-*` and `x86_64-linux-musl-*` for x86
- `arm-linux-musleabihf-*` and `aarch64-linux-musl-*` for ARM

Install via [musl-riscv-toolchain](https://github.com/rv8-io/musl-riscv-toolchain).

### Build Outputs

Binaries are placed in `bin/<arch>/<program>.<O3|O2|Os>[.stripped]`:
- Architectures: `riscv32`, `riscv64`, `i386`, `x86_64`, `arm`, `aarch64`
- Benchmarks: `aes`, `bigint`, `dhrystone`, `miniz`, `norx`, `primes`, `qsort`, `sha512`

## Running Benchmarks

```bash
npm start bench <benchmark> <target> <opt> <runs>
npm start gather   # Aggregate stats/*.json into data/benchmarks.dat
npm start print    # Display collected benchmark results
```

- `<benchmark>`: `all` or one of: `aes`, `bigint`, `dhrystone`, `miniz`, `norx`, `primes`, `qsort`, `sha512`
- `<target>`: `all` or targets like `rv-sim-riscv64`, `rv-jit-riscv32`, `qemu-riscv64`, `native-x86_64`, `size-riscv32`, etc.
- `<opt>`: `all`, `O3`, `O2`, or `Os`
- `<runs>`: Number of iterations (simulation targets capped at 5)

### Target Types

- `rv-sim-*`: rv8 instruction simulator
- `rv-jit-*`: rv8 JIT translator
- `rv-hist-*`: Generate instruction/register histograms
- `qemu-*`: QEMU user-mode emulation
- `native-*`: Native execution via `perf stat`
- `size-*`: Binary file size measurement

## Architecture

```
src/           # Benchmark source files (.c, .cc)
bin/           # Compiled binaries by architecture and opt level
stats/         # JSON results from benchmark runs
data/          # Aggregated benchmark data and markdown tables
  tables.txt   # Table format definitions
  template.md  # Report template with TABLE placeholders
plots/         # Gnuplot scripts for visualization
scripts/update-plots.sh  # Generate plots from collected data
rv8-bench.js   # Main Node.js benchmark runner
```

The runner (`rv8-bench.js`) executes binaries, captures timing/instruction counts, and stores results in `stats/`. The `gather` command aggregates results into TSV format for plotting.
