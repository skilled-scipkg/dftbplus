# dftbplus source map: Inputs and Modeling

Use this after the docs in `doc_map.md`.

## Query tokens
- `ParserVersion`
- `GenFormat`
- `xyzFormat`
- `VaspFormat`
- `Type2FileNames`
- `KPointsAndWeights`
- `ReadInitialCharges`
- `ReadChargesAsText`

## Fast source navigation
- `rg -n "readTGeometryHSD|readTGeometryGen|readTGeometryXyz|readTGeometryVasp|setupPeriodicGeometry" src/dftbp/type/typegeometryhsd.F90`
- `rg -n "parseHSD_file|createChildNode|getHSDPath" src/dftbp/io/hsdparser.F90`
- `rg -n "readBinaryAccessTypes|rotateH0|ss\\(|sp\\(|pp\\(" src/dftbp/dftbplus/input/fileaccess.F90 src/dftbp/dftb/sk.F90`

## Suggested source entry points
- `src/dftbp/io/hsdparser.F90` | inspect `parseHSD_file`, `createChildNode`, and `getHSDPath` for include semantics, duplicate-key handling, and parser error locations
- `src/dftbp/type/typegeometryhsd.F90` | inspect `readTGeometryHSD`, `readTGeometryGen`, `readTGeometryXyz`, `readTGeometryVasp`, `readTGeometryLammps`, and `setupPeriodicGeometry` for geometry-format behaviour and periodic-coordinate checks
- `src/dftbp/type/typegeometry.F90` | core geometry data structure used after parsing when atom ordering or lattice state looks inconsistent downstream
- `src/dftbp/dftbplus/input/fileaccess.F90` | inspect `readBinaryAccessTypes` when restart-style I/O needs explicit text/binary mode control
- `src/dftbp/dftbplus/input/geoopt.F90` | input-side geometry-optimisation handling when driver-side coordinate updates look wrong
- `src/dftbp/dftb/sk.F90` | inspect `rotateH0` and the shell-coupling routines `ss`, `sp`, `pp`, `pd`, `dd` when SK orientation or shell wiring is under suspicion
- `tools/dptools/src/dptools/geometry.py` | scripted geometry manipulation helpers mirrored by dptools CLI
