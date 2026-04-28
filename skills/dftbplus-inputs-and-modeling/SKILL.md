---
name: dftbplus-inputs-and-modeling
description: Use this skill for DFTB+ HSD syntax, geometry/species setup, Slater-Koster parameter wiring, parser conventions, k-point setup, and restart-related input choices.
---

# dftbplus: Inputs and Modeling

## High-Signal Playbook

### Route conditions
- Use `dftbplus-getting-started` for the first runnable example and initial install/parameter questions.
- Use `dftbplus-test` for workflow transitions such as charge restarts, MD, `modes`, `phonons`, or transport execution.
- Use `dftbplus-dftb` when the question is really about advanced method choice (`xTB`, spin, transport, time propagation, solvent, onsite corrections).
- Use `dftbplus-api-and-scripting` for geometry conversion automation or programmatic input construction.

### Triage questions
- Is the system a cluster, a periodic solid, or a transport geometry with contacts?
- Does the geometry already exist as GEN, XYZ, or POSCAR/CONTCAR?
- Is the run non-SCC, SCC, or a restart that reuses charges from an earlier calculation?
- Which Slater-Koster set is intended, and does it support the chemical species and Hamiltonian options?
- Is a simple `Type2FileNames` pattern enough, or does the basis choice require explicit file names?
- Are the k-points for SCF integration, band-structure lines, or a supercell folding grid?

### Canonical workflow
1. Write the geometry in the most natural format: plain `Geometry`, `GenFormat`, `xyzFormat`, or `VaspFormat`.
2. Add `Driver {}` first, then a minimal `Hamiltonian = DFTB { ... }` block.
3. Set `MaxAngularMomentum` and wire `SlaterKosterFiles`; prefer `Type2FileNames` unless the basis setup forces explicit pairwise file names.
4. Add `KPointsAndWeights` only for periodic or helical cases, choosing `SupercellFolding` for SCF meshes and `KLines` only after an SCF charge density already exists.
5. Add `Options`/`Analysis` for outputs and `ParserOptions { ParserVersion = ... }` for compatibility.
6. For restart-driven band structures or post-SCF runs, reuse charges deliberately with `ReadInitialCharges` and matching charge-file format flags.

### Minimal working example
```hsd
Geometry = xyzFormat {
  <<< "molecule.xyz"
}

Driver {}

Hamiltonian = DFTB {
  SCC = Yes
  SccTolerance = 1e-10
  MaxAngularMomentum {
    H = "s"
    C = "p"
    N = "p"
    O = "p"
  }
  SlaterKosterFiles = Type2FileNames {
    Prefix = "slakos/origin/mio-1-1/"
    Separator = "-"
    Suffix = ".skf"
  }
}

Analysis { CalculateForces = Yes }
ParserOptions { ParserVersion = 8 }
```

```hsd
Hamiltonian = DFTB {
  SCC = Yes
  ReadInitialCharges = Yes
  KPointsAndWeights [relative] = KLines {
    1  0.0 0.0 0.0
    10 0.0 0.0 0.5
  }
}
```

```bash
cp test/app/dftb+/input/caffeine_xyz/dftb_in.hsd ./dftb_in.hsd
cp test/app/dftb+/input/caffeine_xyz/caffeine.xyz ./
dftb+ > first-model.out
```

### Pitfalls and fixes
- HSD does not use "last assignment wins"; `hsd.tex` states the first definition is kept and later duplicates are ignored.
- `Type2FileNames` cannot be used with extended bases built through `SelectedShells`; fall back to explicit pairwise file names there.
- Relative coordinates are only valid for periodic systems; `typegeometryhsd.F90` enforces this.
- `KLines` is for band-path evaluation, not charge-density integration; the manual explicitly expects charges from an earlier proper k-mesh run.
- `ReadInitialCharges = Yes` must match the on-disk format: `charges.bin` by default, `charges.dat` only with `ReadChargesAsText = Yes`.
- `DFTBPLUS_PARAM_DIR` is searched in addition to explicit paths, which is helpful for shared SK data but easy to forget when debugging file lookup.

### Convergence and validation checks
- `dftb_pin.hsd` shows the geometry and units exactly as DFTB+ interpreted them.
- Periodic inputs have a deliberate `KPointsAndWeights` block; cluster inputs do not carry accidental k-point baggage.
- A restart input reuses charges only when the geometry/species ordering and total charge/magnetisation are still consistent.
- SK file names resolve to the intended set, and the chosen set matches the species, spin, and method assumptions of the calculation.

## Primary documentation references
- `doc/dftb+/manual/hsd.tex`
- `doc/dftb+/manual/dftbp.tex`
- `doc/dftb+/manual/setupgeom.tex`
- `doc/dftb+/manual/restart_files.tex`

## Runnable example and validation anchors
- `test/app/dftb+/input/caffeine_xyz/dftb_in.hsd`
- `test/app/dftb+/input/unsorted_contcar/dftb_in.hsd`
- `test/app/dftb+/scc/GaAs_2_restart/dftb_in2.hsd`

## Source entry points for unresolved issues
- `src/dftbp/io/hsdparser.F90`
- `src/dftbp/type/typegeometryhsd.F90`
- `src/dftbp/type/typegeometry.F90`
- `src/dftbp/dftbplus/input/fileaccess.F90`
- `src/dftbp/dftbplus/input/geoopt.F90`
- `src/dftbp/dftb/sk.F90`
- `tools/dptools/src/dptools/geometry.py`
- Prefer targeted source search, for example: `rg -n "ParserVersion|Type2FileNames|KPointsAndWeights|ReadInitialCharges|GenFormat" src tools`.
