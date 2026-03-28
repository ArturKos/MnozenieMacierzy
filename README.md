# MatrixMultiplication

High-performance **1000x1000 double-precision matrix multiplication** comparing sequential, OpenMP parallel, and Pluto polyhedral-optimized implementations with built-in benchmarking.

![C](https://img.shields.io/badge/C-99-A8B9CC?logo=c)
![OpenMP](https://img.shields.io/badge/OpenMP-Parallel-00599C)
![Pluto](https://img.shields.io/badge/Pluto-Polyhedral_Optimizer-8B0000)

## Features

- **Sequential baseline** with triple-nested loop matrix multiplication
- **OpenMP parallelization** with configurable thread count and static scheduling
- **Pluto polyhedral tiling** -- cache-friendly loop transformations with tile sizes of 93x95x97 for improved data locality
- **Benchmarking output** -- multiplication time and total execution time (including I/O) logged to separate files per thread count
- **Binary matrix I/O** -- matrices are read from and written to binary files for fast, lossless data transfer
- **Automated sweep script** (`run1000`) to benchmark across 1 to 1000 thread/tile configurations

## Variants

| File | Strategy | Description |
|------|----------|-------------|
| `mmul.c` | OpenMP parallel | Standard `i-j-k` loop parallelized with `#pragma omp parallel for` |
| `mmul.pluto.c` | Pluto tiled + OpenMP | Polyhedral-transformed loop nest with CLooG-generated tiling and OpenMP outer-loop parallelism |
| `mmul.pluto.orgin.c` | Pluto tiled (original) | Original Pluto compiler output with fixed tile file naming |

## Dependencies

| Component | Version | Purpose |
|-----------|---------|---------|
| GCC | any | C compiler with OpenMP support |
| OpenMP (libgomp) | any | Thread-level parallelism |
| Pluto | optional | Polyhedral source-to-source optimizer (only needed to regenerate tiled variants) |
| Math library | libc | `ceil()` / `floor()` for tile bound computation |

### Installing dependencies (Ubuntu / Debian)

```bash
sudo apt-get install gcc libgomp1
```

## Building

### OpenMP parallel version

```bash
gcc -fopenmp -O3 -lm mmul.c -o mmul
```

### Pluto tiled version

```bash
gcc -fopenmp -O3 -lm mmul.pluto.c -o mmul.p
```

### Pluto original version

```bash
gcc -fopenmp -O3 -lm mmul.pluto.orgin.c -o mmul.po
```

## Usage

```bash
./mmul <thread_count>
./mmul.p <thread_count>
```

| Argument | Description |
|----------|-------------|
| `thread_count` | Number of OpenMP threads (or tile configurations for Pluto variants) |

The program reads matrices `A_matrix` and `B_matrix` from binary files in the current directory, performs `C = A * B`, and writes the result to `C_outmatrix`.

### Benchmark sweep

```bash
chmod +x run1000
./run1000
```

This runs the Pluto-tiled variant with thread counts from 1 to 1000, logging timing results to separate files for each configuration.

## Output Files

| File | Content |
|------|---------|
| `C_outmatrix` | Result matrix C in binary format (1000x1000 doubles) |
| `czas_mnozenia_liczbawatkow-N` | Multiplication-only time for N threads |
| `czas_calkowity_liczbawatkow-N` | Total time (I/O + multiplication) for N threads |
| `czas_mnozenia_kafelki` | Multiplication time for Pluto tiled variant |
| `czas_calkowity_kafelki` | Total time for Pluto tiled variant |

## Project Structure

```
MatrixMultiplication/
├── mmul.c                  # OpenMP parallel matrix multiplication
├── mmul.pluto.c            # Pluto polyhedral tiled + OpenMP version
├── mmul.pluto.orgin.c      # Original Pluto compiler output
├── run1000                 # Bash script: benchmark sweep 1..1000 threads
├── A_matrix                # Input matrix A (binary, 1000x1000 doubles)
├── B_matrix                # Input matrix B (binary, 1000x1000 doubles)
├── C_outmatrix             # Output matrix C (binary, 1000x1000 doubles)
├── wyniki1000              # Benchmark results
└── README.md
```

## License

This project is provided as-is for educational purposes.
