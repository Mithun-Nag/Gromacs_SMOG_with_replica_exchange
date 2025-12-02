# 🌟 SMOG2 Simulations of PPARg LBD

Structure-based (Gō-like) models provide a simplified yet powerful framework for studying biomolecular folding and conformational dynamics. Instead of assigning interactions strictly from chemical composition, these models stabilize a known experimental structure by defining native contacts as energetic minima, consistent with the principle of minimal frustration. This strategy enables efficient and interpretable simulations of protein folding, ligand binding, and large-scale rearrangements in proteins.

The **SMOG2** software package was deployed in MD engine **GROMACS** in this corrent workflow

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

# 📁 Directory Structure

```
Gromacs_SMOG_with_replica_exchange/
│
├── 3PRG/                      # SMOG REMD simulation directories where all output files will be written to also add your PDB files here 
├── 6ONI/
├── 6ONJ/
│
├── MDP/ 
│   └── example.mdp                     
│
├── Preparation_and_Run_Scripts/    # SMOG prep, tpr generation, slurm scripts
│   └── 1_SMOG_Simualtion_Prep_&_Run.ipynb
│
├── Analysis_Scripts/          # WHAM + Q-analysis notebooks & scripts
│   ├── SMOG_Replica_3PRG.ipynb
│   ├── SMOG_Replica_6ONJ.ipynb  
│   └── SMOG_Replica_6ONI.ipynb
|
├── contacts-python/           # Native-contact & Q-computation utilities
│
└── WHAM/                      # Free-energy reconstruction outputs
```

---

# 🚀 Workflow Overview

## **1️⃣ Preparation & Model Generation**
Begin by opening **Preparation_and_Run_Scripts/** and running the Jupyter notebook.  
ACCRE cannot use Docker directly, so the SMOG2 Docker image is converted to Apptainer:

```bash
apptainer pull smog2.sif docker://smogserver/smog2:latest
```

This `.sif` file contains the full SMOG2 + OpenSMOG environment.

🔗 Docker source: https://hub.docker.com/r/smogserver/smog2

Inside the notebook:

- SMOG-based models are generated  
- native-contact maps are built  
- all replica `.tpr` files are created  
- a temperature ladder (100–118 K) is selected based on preliminary exploration  
- a multidir REMD run is prepared  

⚠️ **Important:**  
This part must be adapted to *your* HPC:  
GROMACS build, Apptainer path, MPI module, and SLURM settings vary between clusters.

The GROMACS run writes large files to each replica directory. To conserve space in this repository, the following were removed after analysis:

- `md.log`  
- `ener.edr`  
- `traj_comp.xtc`

Everything required to reproduce the analysis remains.

---

## **2️⃣ Analysis: Q-Computation, Temperature Mapping, and WHAM**

Open **Analysis_Scripts/** and run the three Jupyter notebooks in order:

1. **SMOG_Replica_3PRG.ipynb**  
2. **SMOG_Replica_6ONJ.ipynb**  
3. **SMOG_Replica_6ONI.ipynb**

These notebooks implement:

### 🔄 **Demultiplexing (DEMUX)**  
To reconstruct continuous temperature trajectories from REMD, the demux tool is used:

🔗 Demux script: https://github.com/srnas/demux/tree/master  

This produces:

- `*_trajout.xtc`  
- `replica_index.xvg`  
- `replica_temp.xvg`  

These were later removed to save space.

### 📊 **Q-Analysis (Native Contacts)**  
Workflow adapted from:  
🔗 https://github.com/smog-server/SMOG2_tutorial  

Each notebook:

- computes native-contact Q for every frame  
- constructs probability distributions \(P(Q)\)  
- prepares WHAM inputs  

### 🧮 **WHAM Free-Energy Reconstruction**  
The WHAM folder contains outputs derived from:

\[
F(Q) = -kT \ln P(Q)
\]

Notebooks reconstruct folded → intermediate → unfolded basins across temperatures.

### 🧹 Additional Deleted Files  
Removed after analysis:

```
energy.dat
e-Q.dat
potential.xvg
timestep-*.dat
replica_index.xvg
replica_temp.xvg
*__trajout.xtc
traj_comp.xtc
ener.edr
md.log
```

---

# 🎨 Free-Energy Landscape (Enhanced ASCII Figure)

```
                PROTEIN FOLDING ROLLERCOASTER
        ==================================================
              Unfolded           Intermediate         Folded
                State               State             State
                 ____                 /\                *
     Q≈0        /    \               /  \             (Native)
   Energy -->  /      \____         /    \_____      
              /            \_______/            \______
        ==================================================
                    "Hold tight, it's a free-energy ride!"

```
This provides important data related to PPARg LBD folding landscape → intermediate → unfolded free-energy basins.

---

# Final Notes

This repository contains a complete and reproducible workflow for **SMOG2-based REMD simulations** in **GROMACS**, including preparation, execution, demultiplexing, Q-analysis, and WHAM free-energy reconstruction.

For questions or collaboration requests, contact:  
📧 **mithun121@gmail.com**

