# dftbplus source map: Developer Guide

Use this after the docs in `doc_map.md`.

## Query tokens
- `readHsdFile`
- `parseHsdTree`
- `initProgramVariables`
- `runDftbPlus`
- `writeResultsTag`
- `writeDetailedXml`
- `openFile`
- `TDftbPlus`

## Fast source navigation
- `rg -n "readHsdFile|parseHsdTree|readGeometry|readHamiltonian|readAnalysis|readElecDynamics|readTransportGeometry" src/dftbp/dftbplus/parser.F90`
- `rg -n "initProgramVariables|initGeometry_|initTransport_|initSccCalculator_|initOutputFiles" src/dftbp/dftbplus/initprogram.F90`
- `rg -n "runDftbPlus|processScc|getDensity|getGradients|handleCoordinateChange" src/dftbp/dftbplus/main.F90`
- `rg -n "writeResultsTag|writeDetailedXml|writeBandOut|writeHessianOut|writeCharges" src/dftbp/dftbplus/mainio.F90`
- `rg -n "openFile|fileExists|setDefaultBinaryAccess" src/dftbp/common/file.F90`

## Suggested source entry points
- `src/dftbp/dftbplus/parser.F90` | inspect `readHsdFile`, `parseHsdTree`, `readGeometry`, `readHamiltonian`, `readAnalysis`, `readElecDynamics`, and `readTransportGeometry` for input-surface changes
- `src/dftbp/dftbplus/initprogram.F90` | inspect `initProgramVariables`, `initGeometry_`, `initTransport_`, `initSccCalculator_`, and `initOutputFiles` for setup-stage logic
- `src/dftbp/dftbplus/main.F90` | inspect `runDftbPlus`, `processScc`, `handleCoordinateChange`, `getDensity`, and `getGradients` for runtime control flow and numerics
- `src/dftbp/dftbplus/mainio.F90` | inspect `writeResultsTag`, `writeDetailedXml`, `writeBandOut`, `writeHessianOut`, and `writeCharges` for output-format and restart-surface changes
- `src/dftbp/common/file.F90` | inspect `openFile`, `fileExists`, and `setDefaultBinaryAccess` for low-level file and binary-access behaviour
- `src/dftbp/api/mm/dftbplus.F90` | public API module to cross-check whether an internal change also affects the supported library surface
