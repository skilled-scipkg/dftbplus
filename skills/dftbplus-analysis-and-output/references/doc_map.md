# dftbplus documentation map: Analysis and Output

Curated for pass-3 audit from the main output-reference sections, waveplot
manual, dptools docs, and shipped post-processing examples.

Total curated docs in this topic: 8

## Core output-reference docs
- `doc/dftb+/manual/dftbp.tex` | `WriteResultsTag`, `WriteDetailedXML`, `WriteEigenvectors`, `WriteBandOut`, and file-format sections for `results.tag`, `band.out`, `detailed.xml`, and eigenvector files
- `doc/dftb+/manual/waveplot.tex` | `waveplot_in.hsd`, required artifacts, basis definitions, grid controls, and cube-file generation
- `doc/dptools/api/index.rst` | dptools API landing page for CLI-backed post-processing
- `doc/dptools/api/dptools.rst` | modules and scripts behind `dp_bands` and `dp_dos`

## Example output-producing inputs
- `test/app/dftb+/xtb/gfn2_benzene_resltag/dftb_in.hsd` | writes `results.tag` and `band.out` from a small xTB run
- `test/app/dftb+/timedep/C4H6-Singlet_wfn/dftb_in.hsd` | writes `detailed.xml` and excited-state eigenvectors for a waveplot-style follow-up
- `test/app/dftb+/timedep/C4H6-Singlet_wfn/waveplot_in.hsd` | local waveplot configuration consuming `detailed.xml` and `excitedOrbs.bin`
- `test/tools/dptools/test_dp_dos.py` | validates `dp_dos` options against shipped fixtures
