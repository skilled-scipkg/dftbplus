---
name: dftbplus-developer-guide
description: Use this skill for DFTB+ contribution and architecture questions: parser flow, runtime control, output writers, source layout, and how to patch or extend the codebase without starting from user-facing simulation skills.
---

# dftbplus: Developer Guide

## High-Signal Playbook

### Route conditions
- Use `dftbplus-build-and-install` for compilers, optional libraries, and packaging rather than source architecture.
- Use `dftbplus-test` when the question is how to reproduce runtime behaviour with shipped workflows.
- Use `dftbplus-api-and-scripting` for public API usage instead of internal implementation details.
- Use the narrower runtime skills first when the user mainly wants scientific setup help rather than code changes.

### Triage questions
- Are they changing input parsing, initialisation, the SCC/main loop, output writers, or a public API surface?
- Is the change user-visible in HSD/input behaviour, runtime numerics, or only in build/test plumbing?
- Which smallest shipped test reproduces the current behaviour before any edits?
- Does the change need a new output file, parser keyword, or transport/time-propagation hook?

### Canonical workflow
1. Start from the local contribution and buildsystem docs before opening code.
2. Map the requested change to the relevant layer: parser (`parser.F90`), initialisation (`initprogram.F90`), runtime (`main.F90`), output (`mainio.F90`), or API (`src/dftbp/api/mm`).
3. Reproduce the behaviour with the smallest shipped test or `testrun.sh` that covers it.
4. Inspect the reader/writer pair if the issue involves files, and inspect both parser and runtime if the issue involves a new input keyword.
5. Validate with the narrowest `ctest` or example workflow that exercises the touched layer.

### Pitfalls and fixes
- Many behaviours are split across parser, init, and main-loop stages; patching only one layer often produces partial fixes.
- Output-related changes usually need both a writer change in `mainio.F90` and a parser/control change in `parser.F90` or `initprogram.F90`.
- Transport and TD features add separate initialization and restart logic; follow those branches before assuming the main SCC path is authoritative.
- Shipped `autotest` inputs are excellent regression anchors, but many are intentionally nonphysical and should not be repurposed as scientific guidance.

### Validation checkpoints
- The smallest covering test/example reproduces the bug before the patch and exercises the changed layer after it.
- Parser changes still round-trip to the expected processed HSD/input tree.
- Output changes produce files that existing downstream consumers (`waveplot`, `dp_dos`, restart readers) still accept.

## Primary documentation references
- `CONTRIBUTING.rst`
- `doc/dftb+/code/source/buildsystem/introduction.rst`
- `doc/dftb+/ford/dftbplus-project-file.md`
- `test/app/dftb+/README.rst`

## Runnable example and validation anchors
- `test/app/dftb+/scc/GaAs_2_restart/testrun.sh`
- `test/app/dftb+/transport/C-chain_allSteps/testrun.sh`
- `test/src/dftbp/api/mm/testers/test_timeprop.f90`

## Source entry points for unresolved issues
- `src/dftbp/dftbplus/parser.F90`
- `src/dftbp/dftbplus/initprogram.F90`
- `src/dftbp/dftbplus/main.F90`
- `src/dftbp/dftbplus/mainio.F90`
- `src/dftbp/common/file.F90`
- `src/dftbp/api/mm/dftbplus.F90`
- Prefer targeted source search, for example: `rg -n "readHsdFile|initProgramVariables|runDftbPlus|writeResultsTag" src app`.
