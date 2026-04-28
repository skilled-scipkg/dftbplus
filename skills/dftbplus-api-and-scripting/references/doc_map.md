# dftbplus documentation map: API and Scripting

Curated for pass-2 enrichment from the API manual, install notes, dptools docs,
and shipped Fortran/Python API examples.

Total curated docs in this topic: 8

## Core API docs
- `INSTALL.rst` | API/Python build flags, install layout, CMake package export, and `pkg-config` use
- `doc/dftb+/manual/api.tex` | public-library build path, API stability guidance, and key entry-point files
- `doc/api/ford/dftbplus-api.md` | FORD-generated API doc configuration anchored to `src/dftbp/api/mm`

## dptools docs
- `doc/dptools/api/index.rst` | dptools API landing page
- `doc/dptools/api/dptools.rst` | Python modules backing geometry conversion and output post-processing

## Example bindings and scripted workflows
- `test/src/dftbp/api/pyapi/testcases/geodisp/test_geodisp.py` | minimal Python energy/force workflow with lattice vectors
- `test/src/dftbp/api/pyapi/testcases/extpot/test_extpot.py` | Python-driven external-potential injection
- `test/src/dftbp/api/pyapi/testcases/qdepextpot/test_qdepextpot.py` | callback-based population-dependent external potential
