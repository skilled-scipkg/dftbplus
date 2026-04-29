# dftbplus source map: MaxwellLink Socket Coupling

Use this after the docs in `doc_map.md`.

## Query tokens
- `MaxwellLinkSocket`
- `MxlSocketComm`
- `DipoleDerivative`
- `BornUpdateEvery`
- `PerturbSccTol`
- `PerturbDegenTol`
- `receiveField`
- `sendSource`
- `ResetDipole`
- `mxlBomd`

## Fast source navigation
- `rg -n "MaxwellLinkSocket|mxlHost|mxlPort|mxlVerbosity|mxlMoleculeId|mxlResetDipole|DipoleDerivative|BornUpdateEvery|PerturbSccTol|PerturbDegenTol" src/dftbp/dftbplus/parser.F90`
- `rg -n "MxlSocketComm|receiveField|sendSource|receiveInit|receiveFieldData|sendSourceReady" src/dftbp/io/mxlsocket.F90`
- `rg -n "tMxlSocket|MxlSocketComm_init|mxlHaveResult|mxlStop|buildMxlExtraJson|getMxlDipole" src/dftbp/timedep/timeprop.F90`
- `rg -n "tMxlBomd|getMxlBomdEndpointDipole|updateMxlBomdBornCharges|DipoleDerivative|mxlSource" src/dftbp/dftbplus/main.F90 src/dftbp/md/mxlbomd.F90`
- `rg -n "MaxwellLinkSocket for BOMD|BornChargesOnTheFly|TMxlBomd_init|tMxlBornResponse" src/dftbp/dftbplus/initprogram.F90`
- `rg -n "IpiSocketComm|STATUS|GETFORCE|FORCEREADY" src/dftbp/io/ipisocket.F90`
- `rg -n "WITH_SOCKETS|fsockets" CMakeLists.txt config.cmake src/dftbp/extlibs/CMakeLists.txt src/dftbp/extlibs/fsockets.F90`

## Suggested source entry points
- `src/dftbp/dftbplus/parser.F90` | inspect `readElecDynamics`, the `VelocityVerlet` parser branch, and `parseMaxwellLinkSocketAddress` for shared address parsing and BOMD-specific keywords
- `src/dftbp/dftbplus/initprogram.F90` | inspect the runtime checks that gate MaxwellLink BOMD, especially `Xlbomd`, barostat, thermostat, and perturbation restrictions
- `src/dftbp/dftbplus/main.F90` | inspect the BOMD geometry-loop hook, endpoint dipole helper, and on-the-fly Born-charge update path
- `src/dftbp/md/mxlbomd.F90` | inspect the BOMD-specific coupling model, `DipoleDerivative` choices, finite-difference source handling, and JSON metadata builder
- `src/dftbp/io/mxlsocket.F90` | inspect the dedicated MaxwellLink communicator, `INIT` parsing, field-packet consumption, and source-current response format
- `src/dftbp/timedep/timeprop.F90` | inspect the TD main loop that receives fields, checks `dt_au` and molecule ids, applies the field, and sends midpoint source data back
- `src/dftbp/io/ipisocket.F90` | inspect the older i-PI socket implementation when you need a framing comparison or to understand inherited socket helper usage
- `src/dftbp/extlibs/fsockets.F90` | inspect the wrapper around the external `fsockets` library when connection setup itself is failing
