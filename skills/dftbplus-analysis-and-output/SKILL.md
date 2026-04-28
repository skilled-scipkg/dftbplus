---
name: dftbplus-analysis-and-output
description: Use this skill for DFTB+ output interpretation and post-processing: `results.tag`, `band.out`, `detailed.xml`, `waveplot`, and quick CLI analysis of band/DOS artifacts before moving to reusable scripting.
---

# dftbplus: Analysis and Output

## High-Signal Playbook

### Route conditions
- Use `dftbplus-inputs-and-modeling` if the blocker is adding the right HSD keywords rather than interpreting the produced files.
- Use `dftbplus-test` for full workflow execution (`modes`, `phonons`, transport, TD restarts) instead of downstream output handling.
- Use `dftbplus-api-and-scripting` when the post-processing must become a reusable Python/C/Fortran script rather than a quick CLI or one-off inspection.
- Use `dftbplus-dftb` if the physics question is really about feature selection (`xTB`, spin, transport, electron dynamics) rather than the resulting artifacts.

### Triage questions
- Is the target file `detailed.out`, `results.tag`, `band.out`, `dos*.out`, `detailed.xml`, `eigenvec.bin`, `excitedOrbs.bin`, or a cube file from `waveplot`?
- Do they need interpretation only, a quick CLI transformation, or a rerun that enables more outputs?
- Was the originating calculation run with `WriteResultsTag`, `WriteDetailedXML`, `WriteEigenvectors`, or the relevant `Analysis` flags?
- Are they plotting ground-state orbitals or excited-state orbitals and densities?
- Do they need a visualisation artifact (`.cube`) or a tabulated artifact (`bands.dat`, `dos.dat`)?

### Canonical workflow
1. Check whether the source run already wrote the needed artifact; if not, rerun with only the minimum extra output flags.
2. For machine-readable scalar/tabular data, start from `results.tag`, `band.out`, or PDOS files before parsing `detailed.out`.
3. For quick band/DOS post-processing, use `dp_bands` and `dp_dos` on the written text outputs.
4. For orbital/density visualisation, rerun with `WriteDetailedXML = Yes` and `WriteEigenvectors = Yes`, then place a matching `waveplot_in.hsd` in the working directory and run `waveplot`.
5. Validate spin, k-point, level, and grid dimensions before interpreting the physics.

### Minimal working examples
```bash
cp test/app/dftb+/xtb/gfn2_benzene_resltag/dftb_in.hsd ./dftb_in.hsd
dftb+ > dftb.out
dp_bands band.out bands.dat
dp_dos band.out total_dos.dat
```

```bash
cp test/app/dftb+/timedep/C4H6-Singlet_wfn/dftb_in.hsd ./dftb_in.hsd
cp test/app/dftb+/timedep/C4H6-Singlet_wfn/butadiene.gen ./
dftb+ > td.out
cp test/app/dftb+/timedep/C4H6-Singlet_wfn/waveplot_in.hsd ./
waveplot > waveplot.out
```

### Pitfalls and fixes
- `waveplot` only reads `waveplot_in.hsd`; renaming the input file is not supported.
- `waveplot_in.hsd` must match the produced artifacts: ground-state runs usually need `eigenvec.bin`, while the shipped excited-state example uses `excitedOrbs.bin`.
- `WriteDetailedXML` and `WriteEigenvectors` are opt-in; missing `detailed.xml` or eigenvector files usually means the source run did not request them.
- `dp_dos -w` is required when the input file already contains projected or occupation-weighted information that should be preserved in the DOS.
- Large `NrOfPoints` together with `NrOfCachedGrids = -1` can turn waveplot into a memory problem before it becomes a physics problem.

### Convergence and validation checks
- `results.tag`, `band.out`, `detailed.xml`, `eigenvec.bin`/`excitedOrbs.bin`, or `.cube` outputs appear exactly where the selected tool expects them.
- `dp_bands`/`dp_dos` output sizes match the number of bands and grid points implied by the input file.
- `waveplot` level, k-point, and spin selections are consistent with the originating run.
- If an output file is absent, confirm the generating run requested it before debugging the consumer.

## Primary documentation references
- `doc/dftb+/manual/dftbp.tex`
- `doc/dftb+/manual/waveplot.tex`
- `doc/dptools/api/index.rst`
- `doc/dptools/api/dptools.rst`

## Runnable example and validation anchors
- `test/app/dftb+/xtb/gfn2_benzene_resltag/dftb_in.hsd`
- `test/app/dftb+/timedep/C4H6-Singlet_wfn/dftb_in.hsd`
- `test/app/dftb+/timedep/C4H6-Singlet_wfn/waveplot_in.hsd`
- `test/tools/dptools/dp_bands/band.out`
- `test/tools/dptools/dp_dos/TiO2_band.out`

## Source entry points for unresolved issues
- `src/dftbp/dftbplus/mainio.F90`
- `app/waveplot/waveplot.F90`
- `app/waveplot/initwaveplot.F90`
- `app/waveplot/molorb.F90`
- `app/waveplot/slater.F90`
- `app/waveplot/gridcache.F90`
- `tools/dptools/src/dptools/scripts/dp_bands.py`
- `tools/dptools/src/dptools/scripts/dp_dos.py`
- Prefer targeted source search, for example: `rg -n "writeResultsTag|writeDetailedXml|waveplot|dp_dos|dp_bands" src app tools`.
