# PhD Data Analysis — exciton dynamics in organic semiconductors

[![build](https://github.com/cheick-oumar-diarra/phd-data-analysis/actions/workflows/build.yml/badge.svg)](https://github.com/cheick-oumar-diarra/phd-data-analysis/actions/workflows/build.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

Analysis and geometry tools I wrote during my PhD in computational physics
(Université de Strasbourg, ICube laboratory, 2024). The thesis used *ab initio*
molecular dynamics (Car–Parrinello, CPMD) to model **exciton diffusion** and
**thermal transport** in conjugated polymers such as P3HT and P3MT.

The repository contains two independent parts:

| Part | Language | Purpose |
|------|----------|---------|
| [`fortran/`](fortran/) | Fortran 2008 | `AnalyzerExcitonTransfer`: a keyword-driven program that builds polymer geometries and analyses exciton and molecular trajectories |
| [`python/cpmd-geometry-analysis/`](python/cpmd-geometry-analysis/) | Python 3 | Post-processing and plotting of CPMD outputs (energies, thermal transport, density of states, transition moments) and geometry manipulation |

## What the Fortran program does

`AnalyzerExcitonTransfer` reads an input deck from standard input. Each block
triggers one task:

| Block | Task |
|-------|------|
| `&MOLECULE` | Describes the monomer (atom counts per species, atoms per ring, number of stacks) |
| `&LENGTHENING` | Extends a polymer chain by adding rings, rebuilding C–H bonds |
| `&STACKING` | Builds π-stacked chains from a single chain, with optional alternation and periodicity |
| `&EXCITON` | Follows the electron and hole (Wannier centres) along an AIMD trajectory, unwraps periodic boundary conditions, and computes the mean squared displacement (MSD) and step lengths |
| `&TRAJECTORY` | Geometric analysis of the stacks along the trajectory: inter-atomic distances, π–π and inter-stack distances, ring shifts and tilt angles |
| `&END` | Stops reading |

Key numerical steps of the exciton analysis:

- **Trajectory unwrapping** across periodic boundaries, with two algorithms (`UNFOLDALGO`).
- **MSD** of the exciton (average of electron and hole MSDs), in 3D and per axis, with a choice
  between a single-origin estimator and a multiple-time-origin estimator (`MSDALGO`).

The program writes a log to `OUTPUT` and results to plain-text files
(`msd_eh_3d.res`, `pasElecHol.out`, `distPi.out`, `TiltAngle.out`, …).
It expects CPMD-style files in the working directory (`TRAJEC.xyz`, `TRAJ_mol.xyz`, `GEOMETRY.xyz`).

## Build

Requirements: `gfortran` and GNU `make`.

```bash

cd fortran
make            # builds fortran/AnalyzerExcitonTransfer
make clean
```

## Run

```bash
cd my_simulation/                       # directory containing the CPMD trajectory
/path/to/fortran/AnalyzerExcitonTransfer < input.in
```

Main keywords of the `&EXCITON` block (each keyword on its own line, its value on the next;
a block ends with `}`):

| Keyword | Value |
|---------|-------|
| `TSTEP` | CPMD time step (atomic units) |
| `STEPTRAJ` | Number of MD steps between two saved frames |
| `INITSTEP` | First frame to analyse |
| `TOTSTEP` | Number of frames to analyse |
| `BOXDIM` | Simulation box dimensions (x, y, z) |
| `NUMBERBWC` | Number of Wannier centres used to locate the electron and the hole |
| `UNFOLDALGO` | Unwrapping algorithm: `1` or `2` |
| `MSDALGO` | `1`: single time origin; `2`: multiple time origins, followed by the number of steps and the interval between origins |

## Python tools

See the [Python README](python/cpmd-geometry-analysis/README.md).

```bash
pip install -r python/cpmd-geometry-analysis/requirements.txt
```

## Status and known limitations

This is research code, kept as a record of the thesis work. Known limitations:

- Input and output file names are fixed in the code.
- Several Python scripts were written as interactive sessions (Spyder) and contain
  hard-coded local paths; they need light adaptation before reuse.
- There are no unit tests yet; continuous integration currently checks that everything builds.

A modern, tested and parallel re-implementation of the trajectory-unwrapping and
MSD kernels is planned as a separate project.

## License

[MIT](LICENSE) © Cheick Oumar Diarra
