---
name: dftbplus-realtime-ehrenfest
description: Use this skill for DFTB+ real-time Ehrenfest dynamics with `ElectronDynamics`: laser or kick perturbations, free propagation, `IonDynamics`, time-propagation restarts, TD outputs, and validation of real-time runs.
---

# dftbplus: Real-Time Ehrenfest Dynamics

## High-Signal Playbook

### Route conditions
- Use `dftbplus-maxwelllink-socket` when the run is specifically driven by `MaxwellLinkSocket`.
- Use `dftbplus-api-and-scripting` for `libdftbplus` or Python/C/Fortran control of time propagation instead of a standalone `dftb+` run.
- Use `dftbplus-inputs-and-modeling` for general HSD layout, geometry formats, and Hamiltonian setup before narrowing to time propagation.
- Use `dftbplus-test` when the request is broader workflow orchestration rather than Ehrenfest-specific behavior.

### Triage questions
- Is this pure electronic propagation, or full Ehrenfest MD with `IonDynamics = Yes`?
- Is the perturbation a `Kick`, `Laser`, `KickAndLaser`, or the default `None` free evolution?
- Does the run need populations, forces, restart files, or pump-probe snapshots?
- Is the Hamiltonian DFTB or xTB, and does it support the requested force path?
- Is MPI involved in a case the manual marks as unsupported for electron dynamics: periodic systems, hybrid functionals, DFTB+U, onsite corrections, or ion dynamics?

### Canonical workflow
1. Start from a stable SCC ground state with the same Hamiltonian, geometry, and k-point setup you will propagate.
2. Define `ElectronDynamics` with explicit `Steps` and `TimeStep`; choose `Perturbation` and `FieldStrength` only when the field is internal to DFTB+.
3. For real Ehrenfest MD, set `IonDynamics = Yes` and provide either `InitialTemperature` or explicit `Velocities`, unless you are restarting from `tddump.bin/dat`.
4. Keep restart mode consistent: `RestartFromAscii` must match the format previously written by `WriteAsciiRestart`.
5. Validate the run from its TD artifacts: `tdcoords.xyz`, `forcesvst.dat`, `molpopul*.dat`, `tddump.bin/dat`, and any bond-energy or atom-energy outputs you enabled.

### Minimal working example
```hsd
Hamiltonian = DFTB {
  SCC = Yes
  SCCTolerance = 1.0E-10
  Differentiation = FiniteDiff {}
}

ElectronDynamics = {
  Steps = 2000
  TimeStep [au] = 0.2
  Perturbation = Laser {
    PolarisationDirection = 0.0 1.0 1.0
    LaserEnergy [eV] = 6.795
  }
  EnvelopeShape = Sin2 {
    Time1 [fs] = 6.0
  }
  FieldStrength [V/AA] = 0.01
  IonDynamics = Yes
  InitialTemperature [K] = 0.0
  Populations = Yes
  Forces = Yes
  WriteRestart = Yes
  RestartFrequency = 200
}
```

### Pitfalls and fixes
- The manual states that periodic systems, hybrid functionals, DFTB+U, onsite corrections, and `IonDynamics` are not implemented with MPI time propagation; keep those runs OpenMP-only.
- `IonDynamics = Yes` requires either `InitialTemperature` or `Velocities`, unless the run is a TD restart.
- `Restart = Yes` expects `tddump.bin` by default, or `tddump.dat` only when `RestartFromAscii = Yes`.
- `Forces = Yes` writes Ehrenfest forces to `forcesvst.dat`; force availability still depends on the Hamiltonian path, and the tree already contains a tblite-side guard for unsupported cases.
- `Perturbation = None` is valid and suppresses the need for `FieldStrength`; use that default for free propagation or when an external driver supplies the field.

### Convergence and validation checks
- A standalone ground-state run and the propagated run agree on the initial energy, geometry, and occupations before the first TD step.
- `tdcoords.xyz` appears when `IonDynamics = Yes`, `forcesvst.dat` appears when `Forces = Yes`, and `molpopul*.dat` appears when `Populations = Yes`.
- A restart from `tddump.bin/dat` reproduces the continuing trajectory without format mismatches or missing densities.
- The shipped timeprop examples or API tester can reproduce small benchmark trajectories before you scale up system size or field strength.

## Primary documentation references
- `doc/dftb+/manual/dftbp.tex`
- `doc/dftb+/manual/restart_files.tex`

## Runnable example and validation anchors
- `test/app/dftb+/timeprop/benzene_ions_pulse/dftb_in.hsd`
- `test/app/dftb+/timeprop/benzene_ions_restart/dftb_in.hsd`
- `test/app/dftb+/timeprop/benzene_kick_restart/dftb_in.hsd`
- `test/app/dftb+/timeprop/GNR_periodic_ions/dftb_in.hsd`
- `test/src/dftbp/api/mm/testers/test_timeprop.f90`

## Source entry points for unresolved issues
- `src/dftbp/dftbplus/parser.F90`
- `src/dftbp/timedep/timeprop.F90`
- `src/dftbp/timedep/dynamicsrestart.F90`
- `src/dftbp/io/taggedoutput.F90`
- `src/dftbp/api/mm/mmapi.F90`
- Prefer targeted source search, for example: `rg -n "ElectronDynamics|IonDynamics|RestartFromAscii|doTdStep|getTDEnergy" src test/app/dftb+/timeprop`.
