# Battery Fire Simulation

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.20392473.svg)](https://doi.org/10.5281/zenodo.20392473)

OpenFOAM case files for the battery fire simulation during thermal runaway reported in:

**A Coupled Thermal Runaway and Fire Dynamics Model for a 100 Ah Cylindrical Lithium-ion Battery Module**  
Chen Huang, Henrik Wretman, Petra Andersson  
*Fire Technology* — accepted for publication

This is the final validated case (case 32) corresponding to the published results.

---

## Requirements

- OpenFOAM v2406 (ESI edition)
- Solver: `reactingFoam`
- Parallel run recommended: the provided `sbatch_OF_dardel_test` script targets the Dardel HPC cluster (PDC, KTH) with 128 cores

## Repository structure

```
.
├── 0.org/                        # Initial conditions (template)
├── constant/
│   ├── triSurface/               # Battery module STL geometry
│   ├── chemistryProperties       # Combustion chemistry settings
│   ├── combustionProperties
│   ├── reactions                 # Reaction mechanism
│   ├── thermo.compressibleGas    # Thermodynamic data
│   ├── thermophysicalProperties
│   └── turbulenceProperties
├── system/                       # Solver and meshing dictionaries
│   ├── blockMeshDict             # Background mesh
│   ├── snappyHexMeshDict         # Geometry-fitted mesh refinement
│   ├── controlDict               # Run control (endTime = 1200 s)
│   ├── fvSchemes / fvSolution    # Discretisation and linear solvers
│   └── ...
├── Allrun                        # Full mesh + initialisation script
├── Allclean                      # Cleanup script
└── sbatch_OF_dardel_test         # HPC job script (Dardel / SLURM)
```

## Running the case

### 1. Mesh generation and initialisation (serial)

```bash
./Allrun
```

This runs: `surfaceFeatureExtract` → `blockMesh` → `snappyHexMesh` → `topoSet` → `createPatch` → `checkMesh` → `renumberMesh` → `potentialFoam`

### 2. Parallel CFD solve

Decompose and launch with `reactingFoam` in parallel (example for 128 cores):

```bash
decomposePar
mpirun -np 128 reactingFoam -parallel > log.reactingFoam 2>&1
reconstructPar
```

On a SLURM cluster, adapt and submit `sbatch_OF_dardel_test`.

### 3. Simulation parameters

| Parameter | Value |
|-----------|-------|
| End time | 1200 s |
| Write interval | 30 s |
| Turbulence model | k-ε (RAS) |
| Combustion | Eddy Dissipation Concept (EDC) |

## License

The case files are released under the [Creative Commons Attribution 4.0 International (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/) licence. Please cite the paper above if you use this case.

## Contact

Chen Huang — RISE Research Institutes of Sweden
chen.huang@ri.se
