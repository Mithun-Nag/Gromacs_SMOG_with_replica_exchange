## Repository Overview

### Preanalysis_and_exploratory_data_analysis/
Scripts for demultiplexing SMOG replica-exchange simulations and performing initial exploratory analysis of exchange behavior and conformational ensembles.

Includes system-specific directories:
- SMOG_Replica_3PRG
- SMOG_Replica_6ONJ
- SMOG_Replica_6ONI

---

### Combined_Analysis_SMOG/
Python/Jupyter notebooks used to generate all figures and analyses from the raw simulation data.

Selected outputs were exported as `.csv` files or copied as processed `.dat` files for downstream generation of publication-quality vector figures (not included here).

### 2_RMSD_Rg_calculation
This script calculates all RMSD and Rg for the entire system and then copies over the Rg and RMSD for downstream generation of publication-quality vector figures (not included here).