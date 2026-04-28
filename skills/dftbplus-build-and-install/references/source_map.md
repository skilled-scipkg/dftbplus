# dftbplus source map: Build and Install

Use this after the docs in `doc_map.md`.

## Query tokens
- `WITH_MPI`
- `WITH_API`
- `WITH_PYTHON`
- `WITH_TRANSPORT`
- `WITH_TBLITE`
- `CMAKE_PREFIX_PATH`
- `TEST_MPI_PROCS`
- `TEST_OMP_THREADS`

## Fast source navigation
- `rg -n "WITH_MPI|WITH_API|WITH_PYTHON|WITH_TRANSPORT|WITH_TBLITE" config.cmake CMakeLists.txt cmake tools`
- `rg -n "TEST_MPI_PROCS|TEST_OMP_THREADS|autotest2|DFTBPLUS_PARAM_DIR" test tools`
- `rg -n "install\\(|add_subdirectory\\(" CMakeLists.txt app tools src/dftbp`

## Suggested source entry points
- `config.cmake` | inspect the default cache values for `WITH_MPI`, `WITH_API`, `WITH_PYTHON`, `WITH_TRANSPORT`, `TEST_MPI_PROCS`, and `TEST_OMP_THREADS`
- `CMakeLists.txt` | inspect the top-level `project(...)`, dependency discovery, `WITH_MPI`/`WITH_API` guards, and install/test enablement blocks
- `cmake/DftbPlusUtils.cmake` | inspect helper macros that translate feature options into preprocessor and target settings
- `app/CMakeLists.txt` | inspect which executables are compiled only when `WITH_TRANSPORT` or related feature flags are enabled
- `app/dftb+/CMakeLists.txt` | inspect the standalone `dftb+` target definition and installation path
- `tools/CMakeLists.txt` | inspect the `WITH_PYTHON`, `WITH_API`, and `ENABLE_DYNAMIC_LOADING` gates for dptools and the Python wrapper
- `tools/pythonapi/CMakeLists.txt` | inspect the Python package build/install path and shared-library expectations
- `tools/dptools/CMakeLists.txt` | inspect how dptools scripts are installed and tested
- `test/src/dftbp/api/mm/CMakeLists.txt` | inspect API regression-test wiring and `DFTBPLUS_PARAM_DIR` setup
- `test/src/dftbp/api/pyapi/CMakeLists.txt` | inspect Python API regression-test wiring and wrapper invocation
