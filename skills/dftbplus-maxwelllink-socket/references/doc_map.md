# dftbplus documentation map: MaxwellLink Socket Coupling

The current tree has no dedicated MaxwellLink user-manual section. Use the
items below for build prerequisites, generic TD context, and the closest local
socket examples before stepping into source.

Total curated docs in this topic: 6

## Build and TD-context docs
- `INSTALL.rst` | `-DWITH_SOCKETS` build flag required for socket-enabled binaries
- `doc/dftb+/manual/dftbp.tex` | generic `ElectronDynamics` keywords and the older `Driver = Socket` documentation that mirrors the same TCP or UNIX socket choices

## Closest shipped socket examples
- `test/app/dftb+/sockets/H2O/dftb_in.hsd` | UNIX-socket `Driver = Socket` pattern with an externally injected file path
- `test/app/dftb+/sockets/H2O_cluster/dftb_in.hsd` | another UNIX-socket regression case useful for build sanity checks
- `test/app/dftb+/sockets/diamond/dftb_in.hsd` | TCP `Host`-based socket input pattern
- `test/app/dftb+/sockets/diamond/prerun.py` | lightweight mock server showing the framing style used in the existing socket tests
