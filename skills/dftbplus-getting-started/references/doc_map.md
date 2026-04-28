# dftbplus documentation map: Getting Started

Curated for pass-2 enrichment from root docs, manual chapters, and small local
example inputs.

Total curated docs in this topic: 8

## Core quickstart docs
- `README.rst` | package overview, conda/source install choices, parameterisation pointers, and external tutorial/manual links
- `doc/dftb+/manual/introduction.tex` | supported calculation families and the high-level capability map
- `doc/dftb+/manual/hsd.tex` | HSD syntax essentials: assignments, methods, units, comments, and file inclusion
- `doc/dftb+/manual/dftbp.tex` | geometry formats, `Driver`, `Hamiltonian`, `Analysis`, and `ParserOptions`

## Small runnable example anchors
- `test/app/dftb+/input/caffeine_xyz/dftb_in.hsd` | minimal molecule-first input using `xyzFormat` and `Type2FileNames`
- `test/app/dftb+/input/unsorted_contcar/dftb_in.hsd` | periodic `VaspFormat` example with `SupercellFolding`
- `test/app/dftb+/non-scc/decapentaene/dftb_in.hsd` | self-contained non-SCC GEN-format example
- `test/app/dftb+/README.rst` | regression-framework note explaining why tests are examples, not scientific templates
