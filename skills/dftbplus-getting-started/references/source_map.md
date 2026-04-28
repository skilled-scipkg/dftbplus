# dftbplus source map: Getting Started

Use this after the docs in `doc_map.md`.

## Query tokens
- `GenFormat`
- `xyzFormat`
- `VaspFormat`
- `ParserVersion`
- `WriteResultsTag`
- `dftb+`

## Fast source navigation
- `rg -n "parseHSD_file|parseHSD_opened|dumpHSD_file|getHSDPath" src/dftbp/io/hsdparser.F90`
- `rg -n "readTGeometryHSD|readTGeometryGen|readTGeometryXyz|readTGeometryVasp|setupPeriodicGeometry" src/dftbp/type/typegeometryhsd.F90`
- `rg -n "TTaggedWriter_init|writeTaggedHeader|readBinaryAccessTypes" src/dftbp/io/taggedoutput.F90 src/dftbp/dftbplus/input/fileaccess.F90`

## Suggested source entry points
- `src/dftbp/io/hsdparser.F90` | inspect `parseHSD_file`, `parseHSD_opened`, `dumpHSD_file`, and `getHSDPath` when the processed `dftb_pin.hsd` does not match the intended first input
- `src/dftbp/type/typegeometryhsd.F90` | inspect `readTGeometryHSD`, `readTGeometryGen`, `readTGeometryXyz`, `readTGeometryVasp`, and `setupPeriodicGeometry` for format-specific geometry parsing and periodicity rules
- `src/dftbp/io/taggedoutput.F90` | inspect `TTaggedWriter_init` and `writeTaggedHeader` when `results.tag`-style output is missing or malformed
- `src/dftbp/dftbplus/input/fileaccess.F90` | inspect `readBinaryAccessTypes` when restart/output files switch between text and binary access modes
- `app/dftb+/dftbplus.F90` | standalone executable entry point for first runs when runtime behaviour differs from the parsed input
- `app/dftb+/CMakeLists.txt` | confirms the installed executable name and target wiring for `dftb+`
