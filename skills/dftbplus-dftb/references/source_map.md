# dftbplus source map: Advanced Methods and Features

Use this after the docs in `doc_map.md`.

## Query tokens
- `xTB`
- `Spin`
- `Onsite`
- `Transport`
- `ElectronDynamics`
- `Restart`
- `ThirdOrder`
- `Solvent`

## Fast source navigation
- `rg -n "TScc_init|updateCharges|getInternalElStatPotential|finishSccLoop" src/dftbp/dftb/scc.F90`
- `rg -n "ThirdOrder_init|getShifts|addGradientDc|gamma3|hh\\(" src/dftbp/dftb/thirdorder.F90`
- `rg -n "getSpinShift|getOnsiteSpinOrbitEnergy|getDualSpinOrbitShift|wvalues" src/dftbp/dftb/spin.F90 src/dftbp/dftb/spinorbit.F90 src/dftbp/xtb/xtbspinw.F90`
- `rg -n "TNegfInt_init|check_pls|TElecDynamics_init|doTdStep|writeRestartFile" src/dftbp/transport/negfint.F90 src/dftbp/timedep/timeprop.F90 src/dftbp/timedep/dynamicsrestart.F90`

## Suggested source entry points
- `src/dftbp/dftb/scc.F90` | inspect `TScc_init`, `updateCharges`, `updateShifts`, `getInternalElStatPotential`, and `finishSccLoop` for advanced SCC-driven feature behaviour
- `src/dftbp/dftb/thirdorder.F90` | inspect `ThirdOrder_init`, `getShifts`, `addGradientDc`, and the `gamma3`/`hh` family when third-order onsite terms change energies or forces
- `src/dftbp/dftb/uniquehubbard.F90` | inspect `TUniqueHubbard_init`, `sumOverUniqueU`, and `getOrbitalEquiv` for shell-resolved Hubbard/U equivalencing
- `src/dftbp/dftb/spin.F90` | inspect `getSpinShift`, `getEnergySpin_total`, and `Spin_getOrbitalEquiv` for spin-polarised Hamiltonian behaviour
- `src/dftbp/dftb/spinorbit.F90` | inspect `getOnsiteSpinOrbitEnergy`, `addOnsiteSpinOrbitHam`, and `getDualSpinOrbitShift` for spin-orbit pathways
- `src/dftbp/transport/negfint.F90` | inspect `TNegfInt_init`, `check_pls`, `negf_current`, and `local_currents` for transport-side advanced physics
- `src/dftbp/timedep/timeprop.F90` | inspect `TElecDynamics_init`, `runDynamics`, `doTdStep`, and `setPresentField` for real-time electronic dynamics
- `src/dftbp/timedep/dynamicsrestart.F90` | inspect `writeRestartFile` and `readRestartFile` for TD restart-file support
- `src/dftbp/xtb/xtbspinw.F90` | inspect `wvalues` for xTB spin constants in this tree
- `app/phonons/phonons.F90` | inspect `ComputeModes` and `PhononDispersion` when advanced phonon transport outputs diverge from expectations
