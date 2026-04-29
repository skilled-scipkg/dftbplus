# dftbplus documentation map: Born-Oppenheimer Molecular Dynamics

Use the manual first for standard `VelocityVerlet` behavior. Only leave this
doc path for source when the user is debugging implementation limits or the
experimental MaxwellLink BOMD extension.

Total curated docs in this topic: 8

## Manual sections
- `doc/dftb+/manual/dftbp.tex` | canonical `VelocityVerlet`, thermostat, barostat, `md.out`, and `Xlbomd` semantics
- `doc/dftb+/manual/restart_files.tex` | low-level restart-file details for `charges.bin/dat` used by SCC MD restarts

## Shipped MD examples
- `test/app/dftb+/md/H3/dftb_in.hsd` | smallest cluster BOMD example with seeded initial temperature
- `test/app/dftb+/md/H2O-extfield/dftb_in.hsd` | BOMD with an internal external field, useful to separate native field handling from MaxwellLink
- `test/app/dftb+/md/ice_Ic/dftb_in.hsd` | periodic BOMD with a barostat
- `test/app/dftb+/md/SiH-surface_restart/dftb_in1.hsd` | SCC MD restart pattern with saved trajectory data
- `test/app/dftb+/md/ptcda-xlbomd/dftb_in.hsd` | conventional `Xlbomd`
- `test/app/dftb+/md/SiC64-xlbomdfast/dftb_in.hsd` | fast `XlbomdFast`
