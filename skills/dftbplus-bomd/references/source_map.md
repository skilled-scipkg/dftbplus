# dftbplus source map: Born-Oppenheimer Molecular Dynamics

Use this after the docs in `doc_map.md`.

## Query tokens
- `VelocityVerlet`
- `MDRestartFrequency`
- `WriteTrajectoryForces`
- `KeepStationary`
- `Xlbomd`
- `XlbomdFast`
- `mxlBomd`

## Fast source navigation
- `rg -n "MDRestartFrequency|KeepStationary|WriteTrajectoryForces|Barostat|Plumed|Xlbomd|XlbomdFast" src/dftbp/dftbplus/parser.F90`
- `rg -n "tBarostat|thermostatTypes|Xlbomd|MaxwellLinkSocket for BOMD|TMxlBomd_init" src/dftbp/dftbplus/initprogram.F90`
- `rg -n "getNextGeometry|updateDerivsByPlumed|isXlbomd|tBarostat|tMxlBomd" src/dftbp/dftbplus/main.F90`
- `rg -n "type\\(TVelocityVerlet\\)|BarostatStrength|subroutine init|subroutine state" src/dftbp/md/velocityverlet.F90 src/dftbp/md/mdintegrator.F90`
- `rg -n "TXLBOMDInp|Xlbomd_init|TransientSteps|Scale|MinSccIterations|IntegrationSteps|PreSteps" src/dftbp/md/xlbomd.F90`
- `rg -n "DipoleDerivative|BornUpdateEvery|PerturbSccTol|PerturbDegenTol|mxlBomd" src/dftbp/dftbplus/parser.F90 src/dftbp/dftbplus/main.F90 src/dftbp/md/mxlbomd.F90`

## Suggested source entry points
- `src/dftbp/dftbplus/parser.F90` | parse the `VelocityVerlet` block, thermostat and barostat options, `Xlbomd` selection, and the new BOMD `MaxwellLinkSocket` subblock
- `src/dftbp/dftbplus/initprogram.F90` | inspect MD capability checks, integrator construction, and restrictions on `Xlbomd`, barostats, PLUMED, and MaxwellLink BOMD
- `src/dftbp/dftbplus/main.F90` | inspect the MD geometry loop, restart writes, PLUMED coupling, barostat updates, and where MaxwellLink BOMD hooks into the main loop
- `src/dftbp/md/velocityverlet.F90` | inspect the actual velocity-Verlet and barostat coordinate and velocity updates
- `src/dftbp/md/mdintegrator.F90` | inspect the wrapper that advances the active MD integrator and exposes state
- `src/dftbp/md/xlbomd.F90` | inspect the extended-Lagrangian input data and runtime state
- `src/dftbp/md/mxlbomd.F90` | inspect only when the BOMD run is MaxwellLink-coupled; otherwise stay in the generic MD sources
