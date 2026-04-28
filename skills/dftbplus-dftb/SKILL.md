---
name: dftbplus-dftb
description: Use this skill for advanced DFTB+ feature selection and method-level routing: xTB, spin/onsite corrections, solvents, transport-related physics hooks, time propagation, and other non-basic runtime capabilities.
---

# dftbplus: Advanced Methods and Features

## High-Signal Playbook

### Route conditions
- Use `dftbplus-getting-started` for the first successful standalone run.
- Use `dftbplus-inputs-and-modeling` for HSD syntax, geometry blocks, k-point setup, and charge-restart wiring.
- Use `dftbplus-test` when the question is about executing transport, `modes`, `phonons`, MD, or time-dependent workflows end to end.
- Use `dftbplus-api-and-scripting` when the advanced feature must be driven through `libdftbplus` rather than the standalone binary.

### Triage questions
- Are they choosing a Hamiltonian family (`DFTB` vs `xTB`) or enabling an advanced correction on top of DFTB?
- Is the target system cluster, periodic, or open-boundary transport?
- Which optional build flags are already enabled (`WITH_TBLITE`, `WITH_TRANSPORT`, `WITH_ARPACK`, `WITH_POISSON`, `WITH_PLUMED`)?
- Does the chosen Slater-Koster or xTB setup actually support the requested feature?
- Is the user after production physics or just a reduced regression/example case?

### Canonical workflow
1. Choose the feature family first: plain DFTB, xTB, transport, phonons, vibrational analysis, solvent/spin/onsite corrections, or electron dynamics.
2. Verify the build-time requirements in `config.cmake` and `CMakeLists.txt` before touching input syntax.
3. Start from the smallest shipped example for that feature family (`xtb/gfn1_h2`, `transport/graphene_x`, `phonons/C-chain`, `modes/C24O6H8`, or the time-propagation API test).
4. Keep defaults close to the documentation until the baseline case works.
5. Scale system size, k-point density, or advanced knobs only after the small case is reproducible.
6. Validate advanced behaviour against a simpler baseline whenever possible.

### Minimal working example
```hsd
Geometry = genFormat {
  2 C
  H
  1 1 0.0 0.0 -0.371762583041627
  2 1 0.0 0.0  0.371762583041627
}

Driver {}

Hamiltonian = xtb {
  Method = "GFN1-xTB"
}

Analysis { CalculateForces = Yes }
ParserOptions { ParserVersion = 9 }
```

```hsd
ElectronDynamics = {
  Steps = 40000
  TimeStep = 0.1
  FieldStrength [v/a] = 0.01
  Perturbation = Laser {
    PolarisationDirection = 0.5 0.5 0
    LaserEnergy [eV] = 2.55
  }
  EnvelopeShape = Sin2 {
    Time1 [fs] = 30.0
  }
}
```

```bash
cp test/app/dftb+/xtb/gfn1_h2/dftb_in.hsd ./dftb_in.hsd
dftb+ > xtb.out
```

### Pitfalls and fixes
- `Hamiltonian = xTB` is only meaningful in builds with `WITH_TBLITE`.
- `phonons` and transport features depend on `WITH_TRANSPORT`; `WITH_POISSON` follows automatically for transport builds unless explicitly overridden.
- Time-propagation restarts use `tddump.bin/dat`; keep `Restart`, `RestartFromAscii`, and `WriteAsciiRestart` consistent.
- The solvent, spin-constant, and onsite-constant appendices are reference tables, not automatic feature selectors; pair them with the correct Hamiltonian and parameter set.
- The transport manual explicitly says some geometry rules are not fully checked; invalid principal-layer definitions can still pass input parsing and fail later numerically.
- Do not promote regression inputs directly to production studies; several shipped tests intentionally use simplified or nonphysical choices.

### Convergence and validation checks
- The executable or library feature you need is actually present in the build.
- A reduced example reproduces the expected qualitative behaviour before you scale up.
- Advanced outputs (`tddump.*`, `shiftcont_*`, `transmission`, `vibrations.tag`) are produced when the associated feature is enabled.
- Compare against a simpler DFTB/SCC baseline or a smaller cell whenever the advanced feature changes physical conclusions.

## Primary documentation references
- `doc/dftb+/manual/introduction.tex`
- `doc/dftb+/manual/transport.tex`
- `doc/dftb+/manual/phonons.tex`
- `doc/dftb+/manual/modes.tex`
- `doc/dftb+/manual/solvents.tex`
- `doc/dftb+/manual/spin_constants.tex`
- `doc/dftb+/manual/onsite_constants.tex`
- `doc/dftb+/manual/restart_files.tex`

## Runnable example and validation anchors
- `test/app/dftb+/xtb/gfn1_h2/dftb_in.hsd`
- `test/app/dftb+/transport/graphene_x/dftb_in.hsd`
- `test/app/phonons/C-chain/phonons_in.hsd`
- `test/app/modes/C24O6H8/modes_in.hsd`
- `test/src/dftbp/api/mm/testers/test_timeprop.f90`

## Source entry points for unresolved issues
- `src/dftbp/dftb/scc.F90`
- `src/dftbp/dftb/thirdorder.F90`
- `src/dftbp/dftb/uniquehubbard.F90`
- `src/dftbp/dftb/spin.F90`
- `src/dftbp/dftb/spinorbit.F90`
- `src/dftbp/transport/negfint.F90`
- `src/dftbp/timedep/timeprop.F90`
- `src/dftbp/timedep/dynamicsrestart.F90`
- `src/dftbp/xtb/xtbspinw.F90`
- `app/phonons/phonons.F90`
- Prefer targeted source search, for example: `rg -n "xTB|Spin|Onsite|Transport|ElectronDynamics|Restart" src app`.
