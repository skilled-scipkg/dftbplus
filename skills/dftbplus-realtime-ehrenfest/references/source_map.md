# dftbplus source map: Real-Time Ehrenfest Dynamics

Use this after the docs in `doc_map.md`.

## Query tokens
- `ElectronDynamics`
- `IonDynamics`
- `RestartFromAscii`
- `doTdStep`
- `getTDEnergy`
- `tdcoords`
- `forcesvst`

## Fast source navigation
- `rg -n "readElecDynamics|Perturbation|FieldStrength|WriteRestart|IonDynamics" src/dftbp/dftbplus/parser.F90`
- `rg -n "TElecDynamics_init|runDynamics|doTdStep|getTDEnergy|VerboseDynamics" src/dftbp/timedep/timeprop.F90`
- `rg -n "writeRestartFile|readRestartFile|tddump" src/dftbp/timedep/dynamicsrestart.F90`
- `rg -n "forcesvst|tdcoords|final forces|final velocities" src/dftbp/io/taggedoutput.F90`
- `rg -n "initializeTimeProp|doOneTdStep|finalizeTimeProp" src/dftbp/api/mm/mmapi.F90 test/src/dftbp/api/mm/testers/test_timeprop.f90`

## Suggested source entry points
- `src/dftbp/dftbplus/parser.F90` | inspect `readElecDynamics` for all HSD keys, defaults, unit conversions, and perturbation parsing
- `src/dftbp/timedep/timeprop.F90` | inspect `TElecDynamics_init`, `runDynamics`, `doTdStep`, and `getTDEnergy` for the real-time loop and force/energy updates
- `src/dftbp/timedep/dynamicsrestart.F90` | inspect the TD restart serialization format and restart readback path
- `src/dftbp/io/taggedoutput.F90` | inspect TD-specific tagged outputs written at the end of propagation
- `src/dftbp/api/mm/mmapi.F90` | inspect the library entry points used by API-driven time propagation
- `test/src/dftbp/api/mm/testers/test_timeprop.f90` | inspect a small end-to-end API example that sets up and steps the TD engine
