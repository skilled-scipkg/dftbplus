# dftbplus documentation map: Run Workflows and Validation

Curated for pass-2 enrichment from the main runtime manual chapters and the
shipped restart, `modes`, and `phonons` examples.

Total curated docs in this topic: 11

## Core workflow-reference docs
- `doc/dftb+/manual/dftbp.tex` | `Driver`, `VelocityVerlet`, `SecondDerivatives`, `Options`, and `ElectronDynamics`
- `doc/dftb+/manual/restart_files.tex` | charge, transport-contact, and time-propagation restart file formats
- `doc/dftb+/manual/modes.tex` | `modes_in.hsd`, Hessian ingestion, mode plotting, and mass handling
- `doc/dftb+/manual/phonons.tex` | `phonons_in.hsd`, Hessian reuse, transport analysis, and phonon-dispersion setup
- `doc/dftb+/manual/transport.tex` | open-boundary geometry rules, `Transport`, `GreensFunction`, and `TransportOnly`

## Regression and executable example anchors
- `test/app/dftb+/README.rst` | `autotest2` usage for `dftb+`
- `test/app/modes/README.rst` | `autotest2` usage for `modes`
- `test/app/dftb+/scc/GaAs_2_restart/readme.txt` | charge-restart band-structure pattern
- `test/app/dftb+/md/SiH-surface_restart/readme.txt` | two-stage MD restart pattern
- `test/app/modes/C24O6H8/modes_in.hsd` | minimal `modes` input consuming `hessian.out`
- `test/app/phonons/C-chain/phonons_in.hsd` | minimal `phonons` input consuming `hessian.out`
