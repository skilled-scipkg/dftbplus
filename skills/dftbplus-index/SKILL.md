---
name: dftbplus-index
description: This skill should be used when users ask how to use dftbplus and the correct generated documentation skill must be selected before going deeper into source code.
---

# dftbplus Skills Index

## Route the request
- Pick the narrowest skill that can answer the question from docs first.
- Prefer the enriched core skills for first-contact support; use leaf skills only when the question is clearly about their narrow scope.
- Use the selected topic skill's doc map before source, and only open that same skill's source map when the docs leave behaviour ambiguous.

## Generated topic skills
- `dftbplus-getting-started`: first install/use decisions, first runnable input, parameter-file orientation, and first-run sanity checks.
- `dftbplus-build-and-install`: source builds, optional dependencies, MPI/OpenMP variants, `ctest`, install prefixes, and packaging.
- `dftbplus-inputs-and-modeling`: HSD rules, geometry formats, species/k-points, Slater-Koster wiring, and charge-restart input choices.
- `dftbplus-test`: runtime workflows, restarts, `modes`, `phonons`, transport execution, time-dependent runs, and regression-style validation.
- `dftbplus-bomd`: `Driver = VelocityVerlet`, standard BOMD setup, thermostats and barostats, MD restarts, `Xlbomd` distinctions, and routing to MaxwellLink-coupled BOMD.
- `dftbplus-realtime-ehrenfest`: `ElectronDynamics`, `IonDynamics`, real-time perturbations, TD restarts, Ehrenfest outputs, and timeprop validation.
- `dftbplus-maxwelllink-socket`: `MaxwellLinkSocket`, MaxwellLink TCP or UNIX socket setup, handshake semantics, BOMD `DipoleDerivative` choices, and source-level debugging of the TD and BOMD socket paths.
- `dftbplus-api-and-scripting`: `libdftbplus`, Python/C/Fortran API entry points, and dptools conversions/post-processing scripts.
- `dftbplus-dftb`: advanced feature selection and method-level routing for xTB, spin, onsite/solvent tables, transport-related physics, and time propagation.
- `dftbplus-analysis-and-output`: `results.tag`, `band.out`, `detailed.xml`, `waveplot`, and quick CLI post-processing; for reusable scripts or library-driven post-processing, start in `dftbplus-api-and-scripting`.
- `dftbplus-developer-guide`: narrow developer/contribution questions that are not about user-facing runtime behaviour.

## Documentation-first inputs
- `README.rst`
- `INSTALL.rst`
- `doc/dftb+/manual`
- `test/app`
- `test/src/dftbp/api`

## Example roots to prefer
- `test/app/dftb+/`
- `test/app/modes/`
- `test/app/phonons/`
- `test/src/dftbp/api/`

## Test roots for behavior checks
- `test`
- `utils/test`

## Escalate only when needed
- Start from the selected topic skill `SKILL.md`.
- If that is insufficient, open the selected skill's doc map under its references folder.
- If documentation still leaves ambiguity, inspect the selected skill's source map under that same references folder.
- Use targeted search while in source, e.g. `rg -n "<symbol_or_keyword>" app src tools`.

## Source directories for deeper inspection
- `app`
- `src`
- `tools`

## Pass-2 audit note
- No `dftbplus-advanced-topics` merge was applied: only `dftbplus-analysis-and-output` and `dftbplus-developer-guide` are single-doc leaf topics, so collapsing them would reduce routing clarity more than it would reduce noise.
