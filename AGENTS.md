# Agent Guidelines for AOCL Data Analytics

AOCL-DA is AMD's optimized data analytics and machine learning library. It exposes a C-compatible API, builds with CMake, and depends on AOCL-BLAS, AOCL-LAPACK, AOCL-Sparse, AOCL-DLP, AOCL-Utils, and Boost.Sort.

## Project Layout

```
source/            # C++ library source and public C API headers
tests/             # unit tests
tools/             # helper tools
python_interface/  # Python bindings
examples/          # C++ example programs
external/          # bundled Lbfgsb and RALFit
cmake/             # CMake helpers and preset includes
doc/               # Sphinx documentation source
```

## Critical Rules

1. **C API is the public contract.** Internal C++ changes are fine, but changes to `include/` or `source/` public headers must preserve ABI compatibility or bump the version.
2. **Examples and docs are first-class.** New algorithms need an example in `examples/` and a doc page under `doc/Algorithms/`.
3. **Tests live next to the library.** Add unit tests under `tests/` and register them in CMake.
4. **Licensing headers must be preserved.** Files carry the AMD BSD-style header; do not strip it.

## Build Commands

Prerequisites: CMake 3.26+, AOCL libraries, Boost 1.86.0+, C/C++ compiler (GCC, AOCC, or MSVC).

```bash
# Configure with AOCL dependencies
mkdir build && cd build
cmake -DCMAKE_BUILD_TYPE=Release       -DAOCL_ROOT=$AOCL_ROOT       -DBUILD_EXAMPLES=ON       -DBUILD_GTEST=ON       ..

# Build
make -j$(nproc)
```

## Test Commands

```bash
cd build

# Run all tests
ctest --output-on-failure

# Generate coverage report (configured with -DCOVERAGE=On)
make coverage
```

## Lint / Format

```bash
# C/C++ formatting
clang-format -i source/**/*.cpp source/**/*.h

# CMake presets are checked via branch-name-check.yml; validate JSON with
python3 -m json.tool CMakePresets.json
```

## Common Operations

```bash
# Run an example
./build/examples/basic_statistics

# Build Python interface
cd build
cmake -DBUILD_PYTHON_INTERFACE=ON ..
make
```

## Gotchas

- `AOCL_ROOT` must point to the directory containing the AOCL libraries (e.g., `/opt/aocl/5.0`).
- `ARCH=dynamic` enables dynamic dispatch; omitting it builds for the host CPU (`native`).
- Boost.Sort is required; point CMake to it with `-DBoost_ROOT=...` if it is not on the system path.
- The project captures git commit/tag into the binary for version reporting; dirty worktrees show the commit hash.
