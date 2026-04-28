---
name: dftbplus-build-and-install
description: Use this skill for DFTB+ source builds, optional feature toggles, testing, installation, and packaging questions before drilling into runtime inputs or workflows.
---

# dftbplus: Build and Install

## High-Signal Playbook

### Route conditions
- Use `dftbplus-getting-started` for first calculations, parameter-file selection, or a minimal `dftb+` run.
- Use `dftbplus-api-and-scripting` once the question becomes `libdftbplus`, `WITH_PYTHON`, ctypes bindings, or dptools packaging.
- Use `dftbplus-test` for runtime workflows such as restarts, `modes`, `phonons`, or transport execution.
- Use `dftbplus-inputs-and-modeling` when the blocker is HSD syntax or Hamiltonian/input content rather than compilation.

### Triage questions
- Do they want binaries (`conda`) or a source build?
- Is the target serial/OpenMP, MPI, or hybrid MPI+OpenMP?
- Which optional features are actually needed: `WITH_TRANSPORT`, `WITH_TBLITE`, `WITH_ARPACK`, `WITH_PLUMED`, `WITH_MBD`, `WITH_API`, `WITH_PYTHON`?
- Are BLAS/LAPACK and optional externals already installed and discoverable through `CMAKE_PREFIX_PATH`?
- Do they need just `dftb+`, or also `setupgeom`, `phonons`, `modes`, `libdftbplus`, and Python tooling?
- Is the goal a local dev build, CI validation, or an installable prefix?

### Canonical workflow
1. Prefer packaged binaries for quick usage; `README.rst` documents `conda` variants for serial, MPICH, and OpenMPI.
2. For source builds, inspect `config.cmake`; in this tree it is the authoritative place for default feature switches and test runner defaults.
3. Configure with explicit compilers and an out-of-source build directory.
4. Pull optional externals only for features/tests you will actually run; `./utils/get_opt_externals ALL` is the broadest path.
5. Build with `cmake --build`.
6. Validate with `ctest`, adjusting `TEST_MPI_PROCS` and `TEST_OMP_THREADS` when needed.
7. Install with `cmake --install`; for library use, confirm the exported CMake package and `pkg-config` files land under the install prefix.

### Minimal working example
```bash
FC=gfortran CC=gcc cmake -B _build -DCMAKE_INSTALL_PREFIX=$HOME/opt/dftb+ .
./utils/get_opt_externals ALL
cmake --build _build -- -j
pushd _build && ctest -j2 && popd
cmake --install _build
```

```bash
FC=mpifort CC=mpicc cmake -B _build \
  -DWITH_MPI=TRUE -DWITH_ELSI=TRUE \
  -DTEST_MPI_PROCS=2 -DTEST_OMP_THREADS=1 .
cmake --build _build -- -j
pushd _build && ctest && popd
```

### Pitfalls and fixes
- `INSTALL.rst` is the main narrative, but the actual defaults live in `config.cmake`; check both before assuming an option default.
- If CMake stops finding libraries after an option change, delete `_build/CMakeCache.txt` or reconfigure cleanly; `INSTALL.rst` calls this out explicitly.
- Missing `CMAKE_PREFIX_PATH` is the usual reason ELSI, ScaLAPACK, ARPACK, PLUMED, or custom BLAS/LAPACK are not found.
- `WITH_MPI=TRUE` requires MPI-capable Fortran and C compilers; top-level `CMakeLists.txt` hard-fails otherwise.
- `WITH_PYTHON` only installs/tests Python tooling when `tools/CMakeLists.txt` conditions are met; `pythonapi` additionally requires `WITH_API` and `ENABLE_DYNAMIC_LOADING`.
- `WITH_TRANSPORT` is what brings in `libNEGF`, `setupgeom`, and `phonons`; if those executables are missing, the build flags are wrong, not the runtime input.
- Hybrid MPI+OpenMP builds are supported, but `config.cmake` explicitly frames them as expert usage; reduce thread counts during `ctest`.

### Convergence and validation checks
- `_build/app/dftb+/dftb+` exists and `ctest` completes without unresolved external-data errors.
- Optional executables/libraries expected from the chosen flags are present after build/install.
- `_build/_install/lib/cmake/dftbplus/` or the chosen install prefix contains the exported package when `WITH_API` is enabled.
- Tests that need Slater-Koster or GBSA data pass only after `get_opt_externals`; failures before that are not meaningful.

## Primary documentation references
- `README.rst`
- `INSTALL.rst`
- `doc/dftb+/code/source/buildsystem/introduction.rst`
- `test/app/dftb+/README.rst`
- `test/app/modes/README.rst`

## Runnable example and validation anchors
- `test/app/dftb+/README.rst`
- `test/app/modes/README.rst`
- `test/src/dftbp/api/mm/CMakeLists.txt`
- `test/src/dftbp/api/pyapi/CMakeLists.txt`

## Source entry points for unresolved issues
- `config.cmake`
- `CMakeLists.txt`
- `cmake/DftbPlusUtils.cmake`
- `app/CMakeLists.txt`
- `tools/CMakeLists.txt`
- `tools/pythonapi/CMakeLists.txt`
- `tools/dptools/CMakeLists.txt`
- `test/CMakeLists.txt`
- `utils/get_opt_externals`
- Prefer targeted source search, for example: `rg -n "WITH_MPI|WITH_PYTHON|WITH_TRANSPORT|TEST_MPI_PROCS" config.cmake CMakeLists.txt cmake tools test`.
