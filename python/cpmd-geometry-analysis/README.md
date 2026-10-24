# CPMD geometry and analysis tools (Python)

Post-processing, plotting and geometry utilities for outputs of the
[CPMD](https://github.com/CPMD-code/CPMD) *ab initio* molecular dynamics code.
They were used for organic semiconductors (P3HT, P3MT) and for Sb₂S₃.

## Installation

```bash
pip install -r requirements.txt
```

## Main modules

| File | Content |
|------|---------|
| `src/plotCPMD.py` | Analysis classes: `ENERGIES` (NVE/NPT energies), `AEMD` (approach-to-equilibrium MD and temperature profiles for thermal conductivity), `DensityOfState` (eigenvalue extraction and Gaussian broadening), `StructuralProp`, `Exciton`, `FixGeo` |
| `src/functions.py` | Shared helpers: data import, plotting defaults, pressure and cell analysis, thermal conductivity, DOS broadening, transition-moment parsing, exciton lifetime, vector geometry |

## Scripts

| File | Purpose |
|------|---------|
| `src/AEMDdimerP3HT.py` | Plots AEMD results for a P3HT dimer |
| `src/calcTau.py` | Radiative exciton lifetime from the HOMO–LUMO gap and transition dipole |
| `src/calculTau.py` | Oscillator strengths of several transitions |
| `src/transMom2tst.py` | Parses transition moments and builds an absorption spectrum |
| `src/consInputv2.py` | Generates CPMD inputs from geometries taken along a trajectory |
| `src/extract_coord_and_velo.py` | Extracts coordinates and velocities into a geometry file |
| `src/fixGeo7.py` | Geometry manipulation of conjugated chains (translation, rotation, saturation) |
| `src/locTher.py` | Builds atom groups for local thermostats |
| `src/shrinkpositions.py` | Rescales atomic positions |
| `src/addToSysPath.py` | Sets up the import path before an interactive session |

`examples/` contains sample `GEOMETRY.xyz` files and an atom-index list.

## Known limitations

These scripts were written for interactive use (Spyder) during the thesis:

- some contain hard-coded Windows paths (`addToSysPath.py`, `shrinkpositions.py`);
- `plotCPMD.py` imports its helpers as `myPlots.functions`, and some scripts import the
  module as `plotCPMD2`, matching the original local folder layout.

Adjust the import path, or place `functions.py` in a `myPlots` package, before reuse.
