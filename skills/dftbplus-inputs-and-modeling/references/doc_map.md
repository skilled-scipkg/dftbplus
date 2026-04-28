# dftbplus documentation map: Inputs and Modeling

Curated for pass-2 enrichment from the HSD manual, the main DFTB+ input
reference, restart-file notes, and shipped input examples.

Total curated docs in this topic: 8

## Core input-reference docs
- `doc/dftb+/manual/hsd.tex` | HSD syntax, unit modifiers, file inclusion, and duplicate-key behaviour
- `doc/dftb+/manual/dftbp.tex` | geometry formats, `Driver`, `Hamiltonian`, `SlaterKosterFiles`, `KPointsAndWeights`, `Options`, and `ParserOptions`
- `doc/dftb+/manual/restart_files.tex` | `charges.bin/dat`, transport contact files, and time-propagation restart files
- `doc/dftb+/manual/setupgeom.tex` | transport-geometry preparation rules when a plain geometry must be partitioned into device/contact regions

## Example inputs to reuse carefully
- `test/app/dftb+/input/caffeine_xyz/dftb_in.hsd` | `xyzFormat` plus `Type2FileNames`
- `test/app/dftb+/input/unsorted_contcar/dftb_in.hsd` | `VaspFormat` plus periodic k-point setup
- `test/app/dftb+/scc/GaAs_2_restart/dftb_in2.hsd` | charge-restart band-path input using `ReadInitialCharges` and `KLines`
- `test/app/dftb+/transport/local-curr/dftb_in.hsd` | transport input showing restart, Poisson, and `ReadChargesAsText` wiring
