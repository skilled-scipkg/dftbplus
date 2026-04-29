# dftbplus documentation map: MaxwellLink Socket Coupling

The current tree has no dedicated MaxwellLink user-manual section or MaxwellLink
regression case. Use the items below for build prerequisites, generic TD and
MD context, and the closest local socket examples before stepping into source.

Total curated docs in this topic: 8

## Build, TD, and MD-context docs
- `INSTALL.rst` | `-DWITH_SOCKETS` build flag required for socket-enabled binaries
- `doc/dftb+/manual/dftbp.tex` | generic `ElectronDynamics`, `VelocityVerlet`, and the older `Driver = Socket` documentation that mirrors the same TCP or UNIX socket choices

## Closest shipped socket examples
- `test/app/dftb+/sockets/H2O/dftb_in.hsd` | UNIX-socket `Driver = Socket` pattern with an externally injected file path
- `test/app/dftb+/sockets/H2O_cluster/dftb_in.hsd` | another UNIX-socket regression case useful for build sanity checks
- `test/app/dftb+/sockets/diamond/dftb_in.hsd` | TCP `Host`-based socket input pattern
- `test/app/dftb+/sockets/diamond/prerun.py` | lightweight mock server showing the framing style used in the existing socket tests
- `test/app/dftb+/md/H3/dftb_in.hsd` | plain `VelocityVerlet` input anchor for the BOMD-side MaxwellLink block
- `test/app/dftb+/md/H2O-extfield/dftb_in.hsd` | internal external-field MD example useful for separating native field input from MaxwellLink coupling
