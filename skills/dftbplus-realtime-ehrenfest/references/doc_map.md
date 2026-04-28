# dftbplus documentation map: Real-Time Ehrenfest Dynamics

Curated for pass-2 enrichment from the main `ElectronDynamics` manual section,
the TD restart-file appendix, and representative shipped timeprop inputs.

Total curated docs in this topic: 7

## Core runtime-reference docs
- `doc/dftb+/manual/dftbp.tex` | `ElectronDynamics`, perturbation types, envelope shapes, TD outputs, and `IonDynamics` semantics
- `doc/dftb+/manual/restart_files.tex` | `tddump.bin` and `tddump.dat` contents for TD restarts

## Representative timeprop inputs
- `test/app/dftb+/timeprop/benzene_ions_pulse/dftb_in.hsd` | compact Ehrenfest MD example with `Laser`, `Sin2`, `IonDynamics`, and populations
- `test/app/dftb+/timeprop/benzene_ions_restart/dftb_in.hsd` | restart pattern with `IonDynamics = Yes` and `WriteRestart = No`
- `test/app/dftb+/timeprop/benzene_kick_restart/dftb_in.hsd` | minimal restartable `Kick` propagation example
- `test/app/dftb+/timeprop/GNR_periodic_ions/dftb_in.hsd` | periodic TD example that is relevant to the manual's MPI limitations note

## Programmatic validation anchor
- `test/src/dftbp/api/mm/testers/test_timeprop.f90` | `libdftbplus` example that mirrors the same propagation concepts from API entry points
