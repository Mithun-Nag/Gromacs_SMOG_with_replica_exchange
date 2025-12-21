## Repository Overview — Analysis Workflow

### ▶️ How to run the analysis (IMPORTANT)

The analysis workflow is **sequential** and must be run **in order**.

1. **Enter** `Preanalysis_and_exploratory_data_analysis/`  
   Run the system-specific notebooks to inspect replica-exchange behavior and prepare structured outputs.

2. Return to `Analysis_Scripts/` and run the notebooks **in numerical order**:
   - `1_Combined_Analysis_SMOG.ipynb`
   - `2_RMSD_Rg_calculation.ipynb`
   - `3_PCA_Analysis_plots.ipynb`

Overall, this workflow analyzes:
- **where replicas (walkers) ended up in REMD**
- **global structural stability via RMSD and Rg**
- **dominant conformational modes via PCA**

---

### Preanalysis_and_exploratory_data_analysis/

This directory contains **initial post-processing and exploratory analysis notebooks** used to validate, organize, and prepare SMOG replica-exchange simulation data **prior to downstream RMSD, PCA, and free-energy analysis**.

These notebooks represent the **first analysis checkpoint** after simulations complete.

**Focus**
- replica exchange quality and temperature mixing  
- demultiplexing and temperature mapping  
- early assessment of conformational ensembles  
- generation of **structured, lightweight outputs** consumed by  
  **`1_Combined_Analysis_SMOG.ipynb`**

**System-specific notebooks**
- `SMOG_Replica_3PRG.ipynb`
- `SMOG_Replica_6ONJ.ipynb`
- `SMOG_Replica_6ONI.ipynb`

**Demultiplexing (DEMUX)**  
To reconstruct **continuous trajectories at fixed temperatures** from REMD simulations, the demultiplexing procedure is applied.

Tool used:  
https://github.com/srnas/demux/tree/master  

DEMUX generates:
- `*_trajout.xtc` — temperature-continuous trajectories  
- `replica_index.xvg` — replica index vs time  
- `replica_temp.xvg` — temperature vs time  

These files are used to verify replica mixing, assess temperature exchange efficiency, and prepare trajectories for downstream structural analysis.

Intermediate DEMUX files were removed from the repository after analysis to reduce storage footprint.

**Potential energy analysis (from `ener.edr`)**  
Potential energy traces extracted from GROMACS energy files are used to:
- assess equilibration behavior  
- identify temperature-dependent stability trends  
- verify smooth energy overlap between neighboring replicas  
- diagnose problematic replicas or insufficient exchange  

This provides a **thermodynamic sanity check** on the REMD setup before interpreting structural observables.

**Q-analysis (native contacts)**  
Native-contact analysis quantifies folding and unfolding behavior using the fraction of native contacts (Q).

Workflow adapted from:  
https://github.com/smog-server/SMOG2_tutorial  

Each notebook computes Q for every frame, constructs temperature-dependent \( P(Q) \) distributions, and prepares inputs for WHAM free-energy reconstruction.

**WHAM free-energy reconstruction**  
Free-energy profiles are reconstructed using:

\[
F(Q) = -kT \ln P(Q)
\]

This identifies folded, intermediate, and unfolded basins and their temperature-dependent stability across systems.

---

### 1_Combined_Analysis_SMOG.ipynb

Generates summary figures from the **preanalysis outputs**, including:
- replica-exchange diagnostics  
- specific heat profiles  
- potential energy profiles  

Also exports **consolidated `.csv` files** to simplify downstream plotting and figure generation.

---

### 2_RMSD_Rg_calculation.ipynb

Computes **RMSD** and **radius of gyration (Rg)** across all systems and temperatures.  
Processed results are exported as **`.csv` files** for consistent downstream visualization.

---

### 3_PCA_Analysis_plots.ipynb

Performs **Cα-only PCA** to characterize dominant collective motions and conformational landscapes.  
Outputs are written to **`Advanced_Analysis/`** and include PCA projections, eigenvectors, and representative extreme structures.

---

**Files removed after analysis**

To keep the repository lightweight and reviewer-friendly, the following large or intermediate files were removed:

