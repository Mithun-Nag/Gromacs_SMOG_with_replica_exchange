# 🌟 SMOG2 Simulations of PPARg LBD

Structure-based (Gō-like) models provide a simplified yet powerful framework for studying biomolecular folding and conformational dynamics. Instead of assigning interactions strictly from chemical composition, these models stabilize a known experimental structure by defining native contacts as energetic minima, consistent with the principle of minimal frustration. This strategy enables efficient and interpretable simulations of protein folding, ligand binding, and large-scale rearrangements in proteins.

The **SMOG2** software package was deployed in MD engine **GROMACS** in this current workflow

📖 **Comprehensive reference** → https://pmc.ncbi.nlm.nih.gov/articles/PMC8740843/

---

## 🔬 System Preparation in This Workflow

In this work, we used the **SMOG2 Docker environment** (run via *Apptainer* on ACCRE) together with **gromacs-2023.5-plumed-mpi** to simulate three conformational states of the PPARγ ligand-binding domain:

- **3PRG** → Apo  
- **6ONJ** → Agonist-bound (Rosiglitazone)  
- **6ONI** → Repressive conformation (T0070907-bound)

All protein models were generated using **SWISS-MODEL**, with custom templates based on these PDB structures. Each model corresponds to the full-length human PPARγ isoform **P37231-1**  
🔗 https://www.uniprot.org/uniprotkb/P37231/entry  
and is trimmed to begin at **residue 235 (Glu)**, the crystallographically resolved start of the LBD.

---

Gromacs_SMOG_with_replica_exchange/
│
├── 3PRG/                          # SMOG REMD simulation directories (system/temperature level)
├── 6ONI/
├── 6ONJ/
│
├── MDP/                           # GROMACS parameter files
│   └── example.mdp
│
├── Preparation_and_Run_Scripts/   # SMOG preparation, topology/tpr generation, SLURM run scripts
│   └── 1_SMOG_Simulation_Prep.ipynb
│
├── Analysis_Scripts/              # Analysis workflow (run sequentially after simulations)
│   │
│   ├── Preanalysis_and_exploratory_data_analysis/
│   │   ├── SMOG_Replica_3PRG.ipynb
│   │   ├── SMOG_Replica_6ONJ.ipynb
│   │   └── SMOG_Replica_6ONI.ipynb
│   │
│   ├── 1_Combined_Analysis_SMOG.ipynb     # Demultiplexing, temperature mapping, data collation
│   ├── 2_RMSD_Rg_calculation.ipynb        # RMSD and radius of gyration calculations
│   ├── 3_PCA_Analysis_plots.ipynb         # PCA construction and visualization
│   └── README.md                          # Detailed analysis documentation
│
├── Advanced_Analysis/             # Lightweight analysis outputs (PCA, RMSD, Rg; no raw trajectories)
│
├── contacts-python/               # Native-contact & Q-computation utilities
│
└── WHAM/                          # Free-energy reconstruction outputs


---

# 🚀 Workflow Overview

## **1️⃣ Preparation & Model Generation**
Begin by opening **Preparation_and_Run_Scripts/** and running the Jupyter notebook - 1_SMOG_Simulation_Prep.ipynb.  
ACCRE cannot use Docker directly, so the SMOG2 Docker image is converted to Apptainer:

```bash
apptainer pull smog2.sif docker://smogserver/smog2:latest
```

This `.sif` file contains the full SMOG2 + OpenSMOG environment.

🔗 Docker source: https://hub.docker.com/r/smogserver/smog2



⚠️ **Important:**  
This part must be adapted to *your* HPC:  
GROMACS build, Apptainer path, MPI module, and SLURM settings vary between clusters.

The GROMACS run writes large files to each replica directory. To conserve space in this repository, the following were removed after analysis:

- `md.log`  
- `ener.edr`  
- `traj_comp.xtc`

### What this notebook does

This notebook is responsible **only for setup and execution**, not analysis and the analysis scripts are included in Analysis_Scripts folder, please
refer to the README in the folder for more details.

It performs the following:

- Uses **input PDB files placed inside the system folders**: `3PRG/`, `6ONJ/`, `6ONI/`
- Generates SMOG-based models for each system
- Builds native-contact maps
- Creates all replica **`.tpr`** files
- Defines the **temperature ladder (100–118 K)** as specified in the notebook
- Automatically creates **per-temperature subdirectories** inside each system folder
- Configures GROMACS **multidir REMD**
- Submits simulations via a **SLURM job script**
- Writes **all trajectories and outputs directly into the corresponding system/temperature directories**

         INPUT PDBs
    ┌───────────────────┐
    │   3PRG / 6ONJ /   │
    │        6ONI       │
    └─────────┬─────────┘
              │
              ▼
    ┌───────────────────┐
    │   SMOG2 MODELING  │
    │  + CONTACT MAPS  │
    └─────────┬─────────┘
              │
              ▼
    ┌───────────────────┐
    │  REPLICA SETUP    │
    │  (TPRs, TEMPS)   │
    └─────────┬─────────┘
              │
              ▼
    ┌───────────────────┐
    │  SLURM SUBMISSION │
    │  MULTIDIR REMD   │
    └─────────┬─────────┘
              │
              ▼
    ┌───────────────────┐
    │ OUTPUTS WRITTEN TO│
    │ system/temp dirs │
    └───────────────────┘


---

# Final Notes

This repository contains a complete and reproducible workflow for **SMOG2-based REMD simulations** in **GROMACS**, including preparation, execution, and analysis.


For questions or collaboration requests, contact:  
📧 **mithun121@gmail.com**


                HAPPY SIMULATING LAUNCHPAD
    ==================================================
       Setup            Run (REMD)            Science
    (SMOG2+TPRs)     (SLURM + multidir)     (Plots/Paper)
       _____             _________            ________
      /     \           /         \          /        \
     /  PDB  \____     /  replica  \____    /  figure  \
    /__________   \___/  exchange   \   \__/___________/
               \      \_____________/      /
                \_________________________/
    ==================================================
         "May your jobs finish, and your replicas mix!"

