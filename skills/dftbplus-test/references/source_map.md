# dftbplus source map: Run Workflows and Validation

Use this after the docs in `doc_map.md`.

## Query tokens
- `ReadInitialCharges`
- `VelocityVerlet`
- `SecondDerivatives`
- `ElectronDynamics`
- `GreensFunction`
- `TransportOnly`
- `TunnelingAndDOS`

## Fast source navigation
- `rg -n "VelocityVerlet_init|VelocityVerlet_next|VelocityVerlet_state" src/dftbp/md/velocityverlet.F90`
- `rg -n "ComputeModes|PhononDispersion|transportPeriodicSetup|check_pls|calc_current" app/phonons/phonons.F90 src/dftbp/transport/negfint.F90`
- `rg -n "TElecDynamics_init|runDynamics|doTdStep|writeRestartFile|readRestartFile" src/dftbp/timedep/timeprop.F90 src/dftbp/timedep/dynamicsrestart.F90`

## Suggested source entry points
- `app/dftb+/dftbplus.F90` | main standalone runtime entry point when a workflow fails before any specialised driver code is reached
- `src/dftbp/md/velocityverlet.F90` | inspect `VelocityVerlet_init`, `VelocityVerlet_next`, `VelocityVerlet_state`, and `VelocityVerlet_reset` for MD restart and velocity handoff issues
- `app/modes/modes.F90` | `modes` executable entry point consuming `hessian.out`
- `app/phonons/phonons.F90` | inspect `ComputeModes`, `PhononDispersion`, and `writeTaggedOut` for phonon transport output behaviour
- `app/transporttools/setupgeom.F90` | transport-geometry preprocessing entry point
- `src/dftbp/transport/negfint.F90` | inspect `transportPeriodicSetup`, `TNegfInt_init`, `check_pls`, `calc_current`, and `local_currents` for NEGF setup and current/LDOS behaviour
- `src/dftbp/timedep/timeprop.F90` | inspect `TElecDynamics_init`, `runDynamics`, `doTdStep`, `setPresentField`, and `writeTDOutputs` for time-propagation execution
- `src/dftbp/timedep/dynamicsrestart.F90` | inspect `writeRestartFile`, `readRestartFile`, `writeRestartFileBlacs`, and `readRestartFileBlacs` for TD restart-file handling
