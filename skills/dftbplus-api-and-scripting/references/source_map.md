# dftbplus source map: API and Scripting

Use this after the docs in `doc_map.md`.

## Query tokens
- `DftbPlus`
- `set_geometry`
- `get_energy`
- `get_gradients`
- `register_ext_pot_generator`
- `initializeTimeProp`
- `repeatgen`
- `dp_dos`

## Fast source navigation
- `rg -n "class DftbPlus|set_geometry|get_energy|get_gradients|register_ext_pot_generator" tools/pythonapi/src/dftbplus/dftbplus.py`
- `rg -n "TDftbPlus_setupCalculator|TDftbPlus_setGeometry|TDftbPlus_getEnergy|TDftbPlus_initializeTimeProp|TDftbPlus_doOneTdStep" src/dftbp/api/mm/mmapi.F90`
- `rg -n "c_DftbPlus_getInputFromFile|c_DftbPlus_processInput|c_DftbPlus_setCoords|c_DftbPlus_getEnergy|c_DftbPlus_registerExtPotGenerator" src/dftbp/api/mm/capi.F90`
- `rg -n "xyz2gen|gen2xyz|repeatgen|dp_bands|dp_dos" tools/dptools/src/dptools/scripts`

## Suggested source entry points
- `tools/pythonapi/src/dftbplus/dftbplus.py` | inspect the `DftbPlus` class methods `set_geometry`, `get_energy`, `get_gradients`, `get_gross_charges`, `set_external_potential`, and `register_ext_pot_generator`
- `tools/pythonapi/src/dftbplus/__init__.py` | public Python package entry point
- `src/dftbp/api/mm/mmapi.F90` | inspect `TDftbPlus_getInputFromFile`, `TDftbPlus_setupCalculator`, `TDftbPlus_setGeometry`, `TDftbPlus_getEnergy`, `TDftbPlus_getGradients`, `TDftbPlus_initializeTimeProp`, and `TDftbPlus_doOneTdStep`
- `src/dftbp/api/mm/capi.F90` | inspect the `c_DftbPlus_*` bindings for C/ctypes call signatures and callback registration behaviour
- `src/dftbp/api/mm/dftbplus.h` | exported C header for symbol names and public signatures
- `tools/dptools/src/dptools/scripts/xyz2gen.py` | inspect `xyz2gen` for XYZ-to-GEN conversion behaviour
- `tools/dptools/src/dptools/scripts/gen2xyz.py` | inspect `gen2xyz` for GEN-to-XYZ conversion behaviour
- `tools/dptools/src/dptools/scripts/repeatgen.py` | inspect `repeatgen`, `_repeatgeo`, and `_repeatgeo2` for supercell expansion and odd-repeat logic
- `tools/dptools/src/dptools/scripts/dp_bands.py` | inspect `dp_bands` for band-alignment and output formatting
- `tools/dptools/src/dptools/scripts/dp_dos.py` | inspect `dp_dos` for broadening, weighting, and DOS-grid generation
