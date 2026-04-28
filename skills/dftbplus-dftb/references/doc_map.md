# dftbplus documentation map: Advanced Methods and Features

Curated for pass-2 enrichment from the feature-overview chapter, advanced manual
appendices, and representative xTB/transport example inputs.

Total curated docs in this topic: 10

## Core feature-selection docs
- `doc/dftb+/manual/introduction.tex` | high-level capability map for SCC, MD, transport, excited states, GPU/MPI, and auxiliary tools
- `doc/dftb+/manual/transport.tex` | open-boundary electronic transport inputs and solver choices
- `doc/dftb+/manual/phonons.tex` | phonon transport and dispersion workflows
- `doc/dftb+/manual/modes.tex` | vibrational-analysis workflow and Hessian consumption
- `doc/dftb+/manual/restart_files.tex` | restart artifacts that appear in advanced runtime modes

## Parameter/reference appendices
- `doc/dftb+/manual/solvents.tex` | available solvent constants
- `doc/dftb+/manual/spin_constants.tex` | suggested atomic spin constants
- `doc/dftb+/manual/onsite_constants.tex` | suggested onsite-correction constants

## Representative advanced example inputs
- `test/app/dftb+/xtb/gfn1_h2/dftb_in.hsd` | minimal `Hamiltonian = xTB` example
- `test/app/dftb+/transport/graphene_x/dftb_in.hsd` | periodic open-boundary transport example with Poisson and `GreensFunction`
