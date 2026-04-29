---
name: dftbplus-bomd
description: "Use this skill for DFTB+ Born-Oppenheimer molecular dynamics with `Driver = VelocityVerlet`: plain BOMD setup, thermostats and barostats, MD restarts and trajectory outputs, `Xlbomd` versus standard BOMD, and routing MaxwellLink-coupled BOMD questions."
---

# dftbplus: Born-Oppenheimer Molecular Dynamics

## High-Signal Playbook

### Route conditions
- Use `dftbplus-maxwelllink-socket` when the `VelocityVerlet` run includes `MaxwellLinkSocket`; that skill covers the socket protocol, `DipoleDerivative`, and the new experimental MaxwellLink BOMD path.
- Use `dftbplus-inputs-and-modeling` when the blocker is general HSD layout, geometry formats, or Hamiltonian wiring rather than MD behavior itself.
- Use `dftbplus-build-and-install` when the main problem is compile-time capability such as PLUMED, sockets, MPI, or optional libraries.
- Use `dftbplus-dftb` when the hard part is method selection or Hamiltonian limits rather than MD workflow.

### Triage questions
- Is this plain `VelocityVerlet` BOMD, conventional `Xlbomd`, or `XlbomdFast`?
- Are the initial velocities coming from `InitialTemperature`, explicit `Velocities`, or an MD restart?
- Does the run need a thermostat, a periodic barostat, PLUMED, or `WriteTrajectoryForces`?
- Is the system periodic, and if so are all atoms moving as required for barostatted runs?
- Is the user actually asking about socket-driven external fields in BOMD? If yes, switch to `dftbplus-maxwelllink-socket`.

### Canonical workflow
1. Start from a ground-state Hamiltonian and geometry that already produce stable forces for the intended system.
2. Add `Driver = VelocityVerlet` with explicit `Steps` and `TimeStep`, then choose either `Thermostat = None { InitialTemperature }`, explicit `Velocities`, or restart data.
3. Add optional MD controls only as needed: `MovedAtoms`, `KeepStationary`, `MDRestartFrequency`, `OutputPrefix`, `WriteTrajectoryForces`, `Barostat`, and `Plumed`.
4. If acceleration is needed, choose exactly one of `Xlbomd` or `XlbomdFast`; keep them separate from the plain BOMD assumptions and honor their force-evaluation and ensemble limits.
5. Validate from `md.out`, the XYZ trajectory, and restart artifacts before scaling system size or weakening convergence.
6. If the run couples BOMD to MaxwellLink, keep the MD setup here but move protocol and force/source details to `dftbplus-maxwelllink-socket`.

### Minimal working example
```hsd
Geometry = GenFormat {
  <<< "geom.gen"
}

Driver = VelocityVerlet {
  Steps = 500
  TimeStep [fs] = 0.1
  Thermostat = None {
    InitialTemperature [Kelvin] = 273.15
  }
  MDRestartFrequency = 10
}

Hamiltonian = DFTB {
  SCC = Yes
  SCCTolerance = 1.0E-6
  MaxSCCIterations = 1000
  MaxAngularMomentum = {
    H = "s"
  }
  Filling = Fermi {
    Temperature [Kelvin] = 1.0
  }
  SlaterKosterFiles = {
    H-H = "H-H.skf"
  }
}
```

### Pitfalls and fixes
- `VelocityVerlet` still needs a force-capable Hamiltonian; debug the force path before tuning MD controls.
- `MovedAtoms` defaults to `1:-1`, but a user-selected empty set aborts the run.
- `KeepStationary = Yes` is invalid for a one-atom system because translational motion cannot be removed.
- SCC MD restarts are safest when geometry, velocities, and `charges.bin/dat` all come from the same saved step.
- `WriteTrajectoryForces = Yes` adds `fx fy fz` columns to the XYZ trajectory; do not assume the output layout is unchanged.
- `Barostat` is only for periodic BOMD, and the parser rejects subset dynamics with a barostat.
- `Xlbomd` and `XlbomdFast` are mutually exclusive and currently stay in the NVE regime: no thermostats, no barostats, and additional limits on spin, filling, and force evaluation.
- `MaxwellLinkSocket` inside `VelocityVerlet` is experimental and not plain BOMD anymore; it is incompatible with `Xlbomd`, barostats, and internal external fields or atomic external potentials.

### Convergence and validation checks
- `md.out` and the geometry trajectory update at `MDRestartFrequency`, and SCC MD also writes charge restart data on that cadence.
- Plain NVE BOMD should show acceptable total-energy drift for the chosen time step; thermostatted or barostatted runs change the conserved quantity.
- Short reference runs with standard BOMD should bracket whether `Xlbomd` or `XlbomdFast` preserves acceptable forces and energy conservation.
- Restarting from saved geometry, velocities, and charges should continue the trajectory without an avoidable SCC transient.

## Primary documentation references
- `doc/dftb+/manual/dftbp.tex`
- `doc/dftb+/manual/restart_files.tex`

## Runnable example and validation anchors
- `test/app/dftb+/md/H3/dftb_in.hsd`
- `test/app/dftb+/md/H2O-extfield/dftb_in.hsd`
- `test/app/dftb+/md/ice_Ic/dftb_in.hsd`
- `test/app/dftb+/md/SiH-surface_restart/dftb_in1.hsd`
- `test/app/dftb+/md/ptcda-xlbomd/dftb_in.hsd`
- `test/app/dftb+/md/SiC64-xlbomdfast/dftb_in.hsd`

## Source entry points for unresolved issues
- `src/dftbp/dftbplus/parser.F90`
- `src/dftbp/dftbplus/initprogram.F90`
- `src/dftbp/dftbplus/main.F90`
- `src/dftbp/md/velocityverlet.F90`
- `src/dftbp/md/mdintegrator.F90`
- `src/dftbp/md/xlbomd.F90`
- Prefer targeted source search, for example: `rg -n "VelocityVerlet|MDRestartFrequency|WriteTrajectoryForces|Barostat|Xlbomd|XlbomdFast" src test/app/dftb+/md`.
