---
name: dftbplus-test
description: Use this skill for runtime workflows in DFTB+: static runs, charge and MD restarts, `modes`, `phonons`, transport, time-dependent execution, and regression-style validation paths.
---

# dftbplus: Run Workflows and Validation

## High-Signal Playbook

### Route conditions
- Use `dftbplus-inputs-and-modeling` for HSD syntax, geometry blocks, k-point rules, and charge-file format details.
- Use `dftbplus-build-and-install` if the missing piece is `WITH_TRANSPORT`, MPI testing, or the absence of the needed executable.
- Use `dftbplus-analysis-and-output` for interpreting `detailed.out`, `results.tag`, `vibrations.tag`, or waveplot-style output content.
- Use `dftbplus-api-and-scripting` when the workflow becomes a library-driven one rather than a standalone executable run.

### Triage questions
- Is this a plain `dftb+` run, a restart, or an auxiliary executable such as `modes`, `phonons`, or `setupgeom`?
- Is the goal static SCC, geometry optimisation, MD, transport, vibrational analysis, or time-dependent propagation?
- Are optional build flags already enabled for the desired workflow (`WITH_TRANSPORT`, `WITH_POISSON`, `WITH_ARPACK`, `WITH_TBLITE`)?
- Are restart artifacts already present (`charges.bin/dat`, `geo_end.gen`, `velocities.dat`, `hessian.out`, `tddump.bin/dat`)?
- Does the user want a scientific workflow or a regression/CI validation path based on `ctest`/`autotest2`?

### Canonical workflow
1. Stabilise the base `dftb+` input first with `Driver {}` or a simple optimisation.
2. For band structures, converge charges on a proper mesh first, then restart with `ReadInitialCharges = Yes` and `KLines`.
3. For MD restarts, keep the geometry/velocity handoff explicit; the shipped `SiH-surface_restart` test is the clearest local pattern.
4. For vibrational analysis, generate `hessian.out` with `Driver = SecondDerivatives {}` and then run `modes` with `modes_in.hsd`.
5. For phonon transport, prepare the transport geometry, reuse `hessian.out`, and run `phonons` with a `phonons_in.hsd` that defines `Transport`, `Masses`, `Hessian`, and `Analysis`.
6. For electronic transport, enforce the transport geometry rules first; use `setupgeom` if the contact partitioning is not already clean.
7. For time-dependent propagation, define `ElectronDynamics`, and if restarting, keep the `tddump.bin/dat` format consistent with the chosen restart flags.

### Minimal working example
```hsd
# SCF on a proper k-mesh
Hamiltonian = DFTB {
  SCC = Yes
  SCCTolerance = 1.0E-8
  KPointsAndWeights = SupercellFolding {
    6 0 0
    0 6 0
    0 0 6
    0.5 0.5 0.5
  }
}

# Follow-up band path using saved charges
Hamiltonian = DFTB {
  SCC = Yes
  ReadInitialCharges = Yes
  SCCTolerance = 10
  KPointsAndWeights [relative] = KLines {
    1  0.0 0.0 0.0
    10 0.0 0.0 0.5
  }
}
```

```hsd
# Step 1 in dftb+: create hessian.out
Driver = SecondDerivatives {}

# Step 2 in modes: read the Hessian
Geometry = GenFormat { <<< "geo.gen" }
SlaterKosterFiles = Type2FileNames {
  Prefix = "slakos/origin/mio-1-1/"
  Separator = "-"
  Suffix = ".skf"
}
Hessian = { <<< "hessian.out" }
InputVersion = 3
```

```bash
cp test/app/dftb+/scc/GaAs_2_restart/GaAs.gen ./
cp test/app/dftb+/scc/GaAs_2_restart/dftb_in1.hsd ./dftb_in.hsd
dftb+ > scf.out
cp test/app/dftb+/scc/GaAs_2_restart/dftb_in2.hsd ./dftb_in.hsd
dftb+ > bands.out
```

```bash
cp test/app/modes/C24O6H8/geo.gen ./
cp test/app/modes/C24O6H8/hessian.out ./
cp test/app/modes/C24O6H8/modes_in.hsd ./
modes > modes.out
```

### Pitfalls and fixes
- `test/app/dftb+/README.rst` and `test/app/modes/README.rst` both warn that autotest inputs are for regression, not for physically meaningful production templates.
- `modes` requires masses from Slater-Koster files unless `Masses` is set explicitly.
- `phonons` and `setupgeom` only exist in builds with `WITH_TRANSPORT`.
- The transport manual states that several contact-geometry rules are only partially checked by the code; validate contact ordering and principal layers yourself.
- `modes` `Atoms` must match the `MovedAtoms` subset that produced the Hessian.
- `phonons.tex` recommends the `deltaOmega` broadening model over `Mingo` for robustness.
- `WriteHS`/`WriteRealHS` are not normal runs; the manual says DFTB+ writes matrices and stops immediately.
- Time-dependent restarts must use the matching `RestartFromAscii`/`WriteAsciiRestart` choice for `tddump.dat` versus `tddump.bin`.

### Convergence and validation checks
- Restart files are produced and then consumed by a second run without species-ordering changes.
- `geo_end.gen`, `charges.bin/dat`, `hessian.out`, `vibrations.tag`, or transport outputs appear exactly where the workflow expects them.
- `ctest`, `ctest -R modes`, or `autotest2` can reproduce the local shipped workflow on small examples before scaling up.
- For transport and phonons, validate the geometry partitioning and cutoff assumptions before trusting numerical trends.

## Primary documentation references
- `doc/dftb+/manual/dftbp.tex`
- `doc/dftb+/manual/restart_files.tex`
- `doc/dftb+/manual/modes.tex`
- `doc/dftb+/manual/phonons.tex`
- `doc/dftb+/manual/transport.tex`
- `test/app/dftb+/README.rst`
- `test/app/modes/README.rst`

## Runnable example and validation anchors
- `test/app/dftb+/scc/GaAs_2_restart/`
- `test/app/dftb+/md/SiH-surface_restart/`
- `test/app/modes/C24O6H8/modes_in.hsd`
- `test/app/phonons/C-chain/phonons_in.hsd`
- `test/app/phonons/C-chain-proj/phonons_in.hsd`

## Source entry points for unresolved issues
- `app/dftb+/dftbplus.F90`
- `src/dftbp/md/velocityverlet.F90`
- `app/modes/modes.F90`
- `app/phonons/phonons.F90`
- `app/transporttools/setupgeom.F90`
- `src/dftbp/transport/negfint.F90`
- `src/dftbp/timedep/timeprop.F90`
- `src/dftbp/timedep/dynamicsrestart.F90`
- Prefer targeted source search, for example: `rg -n "ReadInitialCharges|VelocityVerlet|SecondDerivatives|ElectronDynamics|GreensFunction" app src`.
