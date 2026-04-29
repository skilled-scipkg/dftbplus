---
name: dftbplus-maxwelllink-socket
description: "Use this skill for DFTB+ `MaxwellLinkSocket` coupling in either `ElectronDynamics` or `Driver = VelocityVerlet`: socket-capable builds, TCP or UNIX socket setup, MaxwellLink handshake semantics, BOMD `DipoleDerivative` options, molecule-id and timestep checks, and source-level debugging of both MaxwellLink paths."
---

# dftbplus: MaxwellLink Socket Coupling

## High-Signal Playbook

### Route conditions
- Use `dftbplus-realtime-ehrenfest` for general `ElectronDynamics`, `IonDynamics`, and restart behavior when the run is not socket-driven.
- Use `dftbplus-bomd` for plain `Driver = VelocityVerlet` BOMD, thermostats, barostats, and restart behavior when the run is not socket-driven.
- Use `dftbplus-build-and-install` when the blocker is enabling `-DWITH_SOCKETS=TRUE` or validating the required socket-capable build.
- Use `dftbplus-api-and-scripting` if the user wants direct `libdftbplus` control rather than MaxwellLink-mediated field exchange.

### Triage questions
- Is the socket-coupled run using `ElectronDynamics` or `Driver = VelocityVerlet`?
- Was DFTB+ compiled with `WITH_SOCKETS`, and is the runtime actually using that build?
- Is MaxwellLink connecting over TCP (`Host` and `Port`) or a UNIX socket (`File`, optionally `Prefix`)?
- Should DFTB+ enforce the `INIT` payload's `dt_au` and `molecule id`, or accept any incoming values?
- For BOMD, should the field force come from `MullikenCharges`, `FixedCharges`, or `BornChargesOnTheFly`?
- Does the client want reported dipoles relative to the initial state (`ResetDipole = Yes`)?
- Is MPI enabled, and do they understand only the global lead rank owns the socket while data are broadcast to the other ranks?

### Canonical workflow
1. Build DFTB+ with `-DWITH_SOCKETS=TRUE`; otherwise the parser rejects `MaxwellLinkSocket`.
2. Choose the driver path explicitly:
   `ElectronDynamics`: keep `Perturbation` absent or `None` if MaxwellLink is the sole field source.
   `Driver = VelocityVerlet`: treat MaxwellLink as an experimental BOMD extension, not as plain MD.
3. Add `MaxwellLinkSocket` with either `Host` and `Port`, or `File` and optional `Prefix`, plus any `Verbosity`, `MoleculeId`, and `ResetDipole` controls.
4. For BOMD, choose `DipoleDerivative` deliberately: default `MullikenCharges`, `FixedCharges` plus `Charges`, or `BornChargesOnTheFly` plus `BornUpdateEvery`, `MaxPerturbIter`, `PerturbSccTol`, and `PerturbDegenTol`. `BornChargesFile` has been removed.
5. At startup, DFTB+ opens the socket, consumes MaxwellLink `INIT` metadata, and in MPI broadcasts the field, stop flag, `dt_au`, and molecule id from the lead rank.
6. Validate the handshake and per-step semantics early: `TimeStep` must match the incoming `dt_au` when provided, `MoleculeId` mismatches abort when checking is enabled, and TD versus BOMD return different source-current constructions.

### Minimal working examples
`ElectronDynamics`:
```hsd
ElectronDynamics = {
  Steps = 5000
  TimeStep [au] = 0.2
  IonDynamics = Yes
  InitialTemperature [K] = 0.0
  Forces = Yes
  VerboseDynamics = Yes
  MaxwellLinkSocket = {
    Host = "localhost"
    Port = 31415
    Verbosity = 1
    MoleculeId = 7
    ResetDipole = Yes
  }
}
```

`Driver = VelocityVerlet`:
```hsd
Driver = VelocityVerlet {
  Steps = 500
  TimeStep [fs] = 0.1
  Thermostat = None {
    InitialTemperature [Kelvin] = 0.0
  }
  MaxwellLinkSocket = {
    Host = "localhost"
    Port = 31415
    Verbosity = 1
    DipoleDerivative = MullikenCharges
    MoleculeId = 7
    ResetDipole = Yes
  }
}
```

For a UNIX socket, replace `Host` and `Port` with `File = "job.sock"`. A non-absolute
file name is prefixed with `/tmp/socketmxl_` unless `Prefix` is set explicitly.

### Pitfalls and fixes
- `Host` and `File` are mutually exclusive. `File` must be non-empty, and TCP `Port` must be greater than zero.
- The parser maps `File = "job.sock"` to `/tmp/socketmxl_job.sock` by default; use an absolute path or set `Prefix` if that is not what the driver expects.
- MaxwellLink uses an i-PI-like packet layout here, but the payload is not atomic positions or forces: DFTB+ expects exactly one electric-field 3-vector and returns exactly one source-current 3-vector.
- `ElectronDynamics` and BOMD do not return the same source: TD uses a dipole finite difference across one electronic step, while BOMD uses half-step velocities for `FixedCharges` and endpoint dipole differences for the other derivative modes.
- BOMD `FixedCharges` requires a non-empty `Charges` list with one value per atom.
- BOMD `DipoleDerivative = BornChargesFile` has been removed; use `BornChargesOnTheFly` instead.
- `BornChargesOnTheFly` inherits coordinate-derivative perturbation limits: it rejects MPI-enabled DFTB+ builds and several advanced Hamiltonian variants such as hybrid, spin-polarized, DFTB+U, onsite-corrected, and multipolar models.
- `Driver = VelocityVerlet` MaxwellLink is incompatible with `Xlbomd`, barostats, and internal external fields or atomic external potentials. Thermostats are accepted but warned because they break conservative Maxwell-molecule energy exchange.
- `ResetDipole = Yes` subtracts the initial dipole before DFTB+ forms the reported dipoles. In BOMD this affects the reported diagnostic dipoles, while the force model still follows the selected derivative mode.
- Periodic runs emit a warning because the MaxwellLink field is uniform and therefore inherits the same periodic-direction caveats as internal laser fields.
- There is no dedicated MaxwellLink regression example in this tree; the older `test/app/dftb+/sockets/*` cases are only build and framing references for generic socket support.

### Convergence and validation checks
- The run reaches `mxlSocketCreate: ...Done` and, with verbosity enabled, shows a clean `STATUS`/`INIT`/field/message sequence rather than unexpected headers.
- A zero-field MaxwellLink driver reproduces the same free propagation as `ElectronDynamics` with no perturbation.
- A zero-field MaxwellLink BOMD driver reproduces the same `VelocityVerlet` trajectory as the equivalent non-socket BOMD setup.
- `INIT dt_au` and the configured `TimeStep` agree within the source tolerance, and any enforced `MoleculeId` matches the incoming value.
- The returned metadata stream contains the expected `time_au`, dipole, midpoint dipole, total energy, kinetic energy, and potential energy fields.

## Primary documentation references
- `INSTALL.rst`
- `doc/dftb+/manual/dftbp.tex`

## Runnable example and validation anchors
- `test/app/dftb+/sockets/H2O/dftb_in.hsd`
- `test/app/dftb+/sockets/H2O_cluster/dftb_in.hsd`
- `test/app/dftb+/sockets/diamond/dftb_in.hsd`
- `test/app/dftb+/sockets/diamond/prerun.py`

## Source entry points for unresolved issues
- `src/dftbp/dftbplus/parser.F90`
- `src/dftbp/dftbplus/main.F90`
- `src/dftbp/timedep/timeprop.F90`
- `src/dftbp/md/mxlbomd.F90`
- `src/dftbp/io/mxlsocket.F90`
- `src/dftbp/io/ipisocket.F90`
- `src/dftbp/extlibs/fsockets.F90`
- Prefer targeted source search, for example: `rg -n "MaxwellLinkSocket|DipoleDerivative|BornUpdateEvery|MxlSocketComm|mxlBomd|receiveField|sendSource|getInitDt|ResetDipole" src test/app/dftb+/md test/app/dftb+/sockets`.
