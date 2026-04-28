# dftbplus documentation map: Build and Install

Curated for pass-2 enrichment from root docs, manual/API notes, and executable
test guidance.

Total curated docs in this topic: 7

## Core build and install docs
- `README.rst` | binary install options, source-build loop, optional externals download, `ctest`, and `cmake --install`
- `INSTALL.rst` | requirements, optional libraries, CMake flags, MPI/OpenMP guidance, testing, install, and library linking
- `doc/dftb+/code/source/buildsystem.rst` | top-level pointer to the build-system docs
- `doc/dftb+/code/source/buildsystem/introduction.rst` | custom `cmake/` layout, fypp preprocessing, and submodule integration notes
- `doc/dftb+/manual/api.tex` | build/install path for `libdftbplus` and API-focused validation

## Validation and test-operation docs
- `test/app/dftb+/README.rst` | `autotest2` usage and the warning that regression inputs are not scientific templates
- `test/app/modes/README.rst` | `modes` regression workflow and SK-data expectations
