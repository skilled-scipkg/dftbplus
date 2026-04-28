# dftbplus source map: Analysis and Output

Use this after the docs in `doc_map.md`.

## Query tokens
- `WriteResultsTag`
- `WriteDetailedXML`
- `WriteEigenvectors`
- `writeResultsTag`
- `writeDetailedXml`
- `writeBandOut`
- `waveplot`
- `TMolecularOrbital`
- `dp_bands`
- `dp_dos`

## Fast source navigation
- `rg -n "writeResultsTag|writeDetailedXml|writeBandOut|writeEigenvectors" src/dftbp/dftbplus/mainio.F90`
- `rg -n "TProgramVariables_init|readDetailed|readOptions|checkEigenvecs" app/waveplot/initwaveplot.F90`
- `rg -n "TMolecularOrbital_getValue|TSlaterOrbital_getValue|writeCubeFile|TGridCache_next" app/waveplot`
- `rg -n "dp_bands|dp_dos" tools/dptools/src/dptools/scripts`

## Suggested source entry points
- `src/dftbp/dftbplus/mainio.F90` | inspect `writeResultsTag`, `writeDetailedXml`, `writeBandOut`, `writeEigenvectors`, and `writeCharges` when an expected artifact is missing or malformed
- `app/waveplot/initwaveplot.F90` | inspect `TProgramVariables_init`, `readDetailed`, `readOptions`, and `checkEigenvecs` when `waveplot_in.hsd`, `detailed.xml`, or eigenvector files are rejected
- `app/waveplot/waveplot.F90` | inspect `writeCubeFile` for cube layout, grid ordering, and output naming
- `app/waveplot/molorb.F90` | inspect `TMolecularOrbital_init`, `TMolecularOrbital_getValue_real`, and `TMolecularOrbital_getValue_cmpl` for orbital sampling on the plotting grid
- `app/waveplot/slater.F90` | inspect `TSlaterOrbital_init` and `TSlaterOrbital_getValue` when basis definitions or cutoff behaviour are wrong
- `app/waveplot/gridcache.F90` | inspect `TGridCache_init`, `TGridCache_next_real`, and `TGridCache_next_cmpl` for memory/caching issues on dense grids
- `tools/dptools/src/dptools/scripts/dp_bands.py` | inspect `dp_bands` for alignment and band-column formatting
- `tools/dptools/src/dptools/scripts/dp_dos.py` | inspect `dp_dos` for broadening, weighting, and DOS-grid generation
