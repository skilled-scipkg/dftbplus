---
name: dftbplus-api-and-scripting
description: Use this skill for `libdftbplus`, Python bindings, C/Fortran API entry points, and dptools-based geometry/output scripting.
---

# dftbplus: API and Scripting

## High-Signal Playbook

### Route conditions
- Use `dftbplus-build-and-install` for compiling/installing the library or enabling the required build flags.
- Use `dftbplus-inputs-and-modeling` for HSD input structure; the API still consumes HSD and the manual recommends setting `ParserVersion`.
- Use `dftbplus-test` for full standalone workflows (`modes`, `phonons`, restarts, transport) rather than library embedding.
- Use `dftbplus-analysis-and-output` when the task is only to interpret output files rather than generate/transform them.

### Triage questions
- Is the user asking for Python, C, or Fortran API usage?
- Do they need to drive calculations programmatically, or only convert/post-process files?
- Was DFTB+ built with `WITH_API`, and for Python also `BUILD_SHARED_LIBS`, `ENABLE_DYNAMIC_LOADING`, and `WITH_PYTHON`?
- Is the task plain energy/force evaluation, external-potential coupling, or time propagation?
- Are they working inside the source tree or against an installed prefix?

### Canonical workflow
1. Build/install the library path you actually want to target; Python bindings require a dynamically loadable shared library.
2. Keep a normal `dftb_in.hsd` alongside the script and set `ParserVersion` there for forward compatibility.
3. In Python, construct `dftbplus.DftbPlus(...)`, call `set_geometry(...)`, then query energy, gradients, and charges.
4. For external QM/MM-style couplings, use `set_external_potential(...)` or callback-based `register_ext_pot_generator(...)`.
5. For time propagation and low-level control, step down to the Fortran/C API in `src/dftbp/api/mm`.
6. Use dptools CLI/scripts for geometry conversion (`xyz2gen`, `gen2xyz`, `repeatgen`) and for band/DOS post-processing (`dp_bands`, `dp_dos`).

### Minimal working example
```python
import numpy as np
import dftbplus

coords = np.array([
    [0.0, 0.0, 0.0],
    [2.5639291987021915, 2.5639291987021915, 2.5639291987021915],
])
latvecs = np.array([
    [5.1278583974043830, 5.1278583974043830, 0.0],
    [0.0, 5.1278583974043830, 5.1278583974043830],
    [5.1278583974043830, 0.0, 5.1278583974043830],
])

calc = dftbplus.DftbPlus(libpath="src/dftbp/libdftbplus", hsdpath="dftb_in.hsd")
calc.set_geometry(coords, latvecs=latvecs)
energy = calc.get_energy()
forces = calc.get_gradients()
charges = calc.get_gross_charges()
calc.close()
```

```bash
xyz2gen molecule.xyz -o molecule.gen
repeatgen -p molecule.gen 3 3 3 > supercell.gen
dp_bands band.out bands
dp_dos -w region1.out region1.dat
```

### Pitfalls and fixes
- `tools/CMakeLists.txt` only installs/tests dptools when `WITH_PYTHON` is enabled; `tools/pythonapi` is added only when `WITH_API` and `ENABLE_DYNAMIC_LOADING` are also on.
- The manual states that API-exchanged values are in atomic units; do not pass Angstrom/eV values unless the API routine explicitly consumes HSD-parsed input.
- The Python wrapper defaults `libpath` relative to the installed package layout; in-source-tree tests pass `src/dftbp/libdftbplus` explicitly.
- `dp_dos -w` is needed for PDOS-style occupation weighting; without it you just recover the total DOS.
- `dp_bands -A` auto-aligns to an approximate VBM/Fermi-like level, not necessarily the true Fermi energy from `detailed.out` or `results.tag`.
- `repeatgen -p` enforces odd supercell repetitions for phonon bandstructure preparation.

### Convergence and validation checks
- `ctest -R 'api_'` or `ctest -R 'pyapi_'` passes for the enabled API stack.
- A standalone `dftb+` run and the API-driven run agree on energy/forces for the same input and geometry.
- Installed prefixes contain `libdftbplus`, the exported CMake package, and Python scripts in the expected locations.
- dptools outputs (`.gen`, `.xyz`, band/DOS `.dat`) can be regenerated deterministically from the same input files.

## Primary documentation references
- `INSTALL.rst`
- `doc/dftb+/manual/api.tex`
- `doc/api/ford/dftbplus-api.md`
- `doc/dptools/api/index.rst`
- `doc/dptools/api/dptools.rst`

## Runnable example and validation anchors
- `test/src/dftbp/api/mm/testers/test_treeinit.f90`
- `test/src/dftbp/api/mm/testers/test_timeprop.f90`
- `test/src/dftbp/api/pyapi/testcases/geodisp/test_geodisp.py`
- `test/src/dftbp/api/pyapi/testcases/extpot/test_extpot.py`
- `test/src/dftbp/api/pyapi/testcases/qdepextpot/test_qdepextpot.py`

## Source entry points for unresolved issues
- `tools/pythonapi/src/dftbplus/dftbplus.py`
- `src/dftbp/api/mm/mmapi.F90`
- `src/dftbp/api/mm/capi.F90`
- `src/dftbp/api/mm/dftbplus.h`
- `tools/dptools/src/dptools/scripts/xyz2gen.py`
- `tools/dptools/src/dptools/scripts/gen2xyz.py`
- `tools/dptools/src/dptools/scripts/repeatgen.py`
- `tools/dptools/src/dptools/scripts/dp_bands.py`
- `tools/dptools/src/dptools/scripts/dp_dos.py`
- Prefer targeted source search, for example: `rg -n "DftbPlus|set_geometry|get_energy|initializeTimeProp|repeatgen|dp_dos" src tools test`.
