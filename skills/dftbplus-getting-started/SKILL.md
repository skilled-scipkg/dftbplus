---
name: dftbplus-getting-started
description: Use this skill for first-run DFTB+ questions: what to install, how to prepare a small runnable input, how to point to parameter files, and how to sanity-check the first result.
---

# dftbplus: Getting Started

## High-Signal Playbook

### Route conditions
- Use `dftbplus-build-and-install` if the user still needs to compile or package DFTB+.
- Use `dftbplus-inputs-and-modeling` once the question turns into HSD details, geometry formats, k-points, or restart files.
- Use `dftbplus-test` for restarts, MD, `modes`, `phonons`, transport, or time-dependent workflows.
- Use `dftbplus-api-and-scripting` for Python bindings, `libdftbplus`, or dptools-based post-processing.

### Triage questions
- Is DFTB+ already installed, and if so is it the standalone binary or an API-enabled build?
- Is the first target a molecule/cluster, a periodic crystal, or an open-boundary transport system?
- Which geometry format is most convenient: inline HSD, `GenFormat`, `xyzFormat`, or `VaspFormat`?
- Which Slater-Koster set is available, and where is it stored?
- Do they need a plain DFTB run, or an optional method such as xTB, transport, or time propagation?
- Are they asking for a physical starter input or just a regression/example input?

### Canonical workflow
1. Install via `conda` or build from source as documented in `README.rst`.
2. Obtain the needed parameterisation; `README.rst` points to `dftb.org`, while test data can be pulled with `utils/get_opt_externals`.
3. Choose the geometry format that matches existing data: `GenFormat`, `xyzFormat`, or `VaspFormat`.
4. Start with a static `Driver {}` run and only the minimum `Hamiltonian`, `Analysis`, and `ParserOptions` blocks.
5. Run `dftb+` in a clean working directory and inspect `dftb_pin.hsd`, `detailed.out`, and optionally `results.tag`.
6. Once the first calculation works, branch into geometry optimisation, band structure, MD, transport, or API use with the dedicated skills.

### Minimal working example
```hsd
Geometry = GenFormat {
  2  S
  Ga As
  1 1 0.000000 0.000000 0.000000
  2 2 1.356773 1.356773 1.356773
  0.000000 0.000000 0.000000
  2.713546 2.713546 0.000000
  0.000000 2.713546 2.713546
  2.713546 0.000000 2.713546
}

Driver {}

Hamiltonian = DFTB {
  SCC = Yes
  MaxAngularMomentum = { Ga = "d" As = "p" }
  SlaterKosterFiles = Type2FileNames {
    Prefix = "slakos/origin/hyb-0-2/"
    Separator = "-"
    Suffix = ".skf"
  }
  KPointsAndWeights = SupercellFolding {
    1 0 0
    0 1 0
    0 0 1
    0.0 0.0 0.0
  }
}

ParserOptions { ParserVersion = 5 }
```

```bash
export DFTBPLUS_PARAM_DIR=$PWD
dftb+ > output.out
```

### Pitfalls and fixes
- Test inputs are for regression coverage, not for physically meaningful production setups; `test/app/dftb+/README.rst` says not to use them as scientific templates.
- Missing parameter files are the most common first-run failure; `SlaterKosterFiles` also searches under `DFTBPLUS_PARAM_DIR`.
- Periodic geometries need `KPointsAndWeights`; cluster geometries do not.
- If you omit `ParserVersion`, DFTB+ assumes the current parser format; older inputs should state their target version explicitly.
- `GenFormat`, `xyzFormat`, and `VaspFormat` are all valid, but relative coordinates only make sense for periodic systems.
- Start with `Driver {}`; adding optimisation, MD, or transport before the base input is stable hides simpler mistakes.

### Convergence and validation checks
- `dftb_pin.hsd` is written and reflects the processed input you intended to run.
- `detailed.out` exists and the SCC cycle converges when `SCC = Yes`.
- Forces, charges, or `results.tag` only appear when the corresponding `Analysis` or `Options` flags were requested.
- The first successful small-system run should be reproduced before moving to bigger cells, denser k-meshes, or advanced features.

## Primary documentation references
- `README.rst`
- `doc/dftb+/manual/introduction.tex`
- `doc/dftb+/manual/hsd.tex`
- `doc/dftb+/manual/dftbp.tex`

## Runnable example and validation anchors
- `test/app/dftb+/input/caffeine_xyz/dftb_in.hsd`
- `test/app/dftb+/input/unsorted_contcar/dftb_in.hsd`
- `test/app/dftb+/non-scc/decapentaene/dftb_in.hsd`
- `test/app/dftb+/README.rst`

## Source entry points for unresolved issues
- `app/dftb+/dftbplus.F90`
- `app/dftb+/CMakeLists.txt`
- `src/dftbp/io/hsdparser.F90`
- `src/dftbp/type/typegeometryhsd.F90`
- `src/dftbp/io/taggedoutput.F90`
- `src/dftbp/dftbplus/input/fileaccess.F90`
- Prefer targeted source search, for example: `rg -n "GenFormat|xyzFormat|VaspFormat|ParserVersion|WriteResultsTag" app src tools`.
