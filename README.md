# gbmMINER (Original Notebook Repository)

## Execution Guide & Required Input Files

This repository contains the original Jupyter notebooks used for the
gbmMINER analysis described in the associated manuscript - https://www.medrxiv.org/content/10.1101/2024.04.05.24305380v1.full.pdf. Large datasets
are **not included** in this repository and must be supplied by the
user.

This document explains:

1.  How to set up the environment
2.  How to execute the notebooks (recommended order)
3.  Exactly which input files are required
4.  Common failure points and troubleshooting

------------------------------------------------------------------------

# 1. Environment Setup

## Option A: Conda (Recommended)

``` bash
conda create -n gbmminer python=3.10 -y
conda activate gbmminer
pip install numpy pandas scipy scikit-learn matplotlib seaborn lifelines statsmodels jupyter nbformat
```

## Option B: venv + pip

``` bash
python -m venv .venv
source .venv/bin/activate   # mac/linux
# .\.venv\Scripts\activate   # windows
pip install numpy pandas scipy scikit-learn matplotlib seaborn lifelines statsmodels jupyter nbformat
```

------------------------------------------------------------------------

# 2. IMPORTANT: Hardcoded Paths

Many notebooks contain lines like:

``` python
os.chdir("E:\\ISB work stuff\\Post-Doc_BaligaLab_GBMSYGNALProject")
```

You MUST either: - Replace this path with your local path - Or comment
it out and run the notebook from the repo root

If you do not fix this, file loading will fail.

------------------------------------------------------------------------

# 3. Core Input Files Required

The notebooks depend on the following categories of input data:

## A. MINER Output Folder (Required)

Generated from running MINER externally.

Minimum required files:

-   `regulons.json`
-   `eigengenes.csv` (required for RMST notebook)
-   `overExpressedMembers.csv`
-   `underExpressedMembers.csv`

These are typically found in:

    GBM/results/MINER/MINER_MicroLowessRNATMM.08.24.2020/

If you do not have these files, you must generate them using the MINER
workflow outside this repository.

------------------------------------------------------------------------

## B. Expression Matrix (Required)

Example filename used in notebooks:

-   `GbmMicroRNAMergedmRNAMicroarraymiRNA.csv`
-   OR `GbmMicroRNAMergedWithIDsZScored.csv`

Format: - Rows: genes (and optionally miRNAs) - Columns: patient/sample
IDs - Values: normalized expression (often Z-scored)

------------------------------------------------------------------------

## C. Survival Metadata (Required for Cox/RMST)

Example:

-   `TCGA_Survival_Gbm.csv`

Must contain: - Sample ID column matching expression matrix - Survival
time column - Event indicator column (0/1)

------------------------------------------------------------------------

# 4. Recommended Notebook Execution Order

## 1️⃣ MINER_networkMapping.ipynb

Purpose: - Map expression to regulon coherent membership

Requires: - Expression matrix - `regulons.json`

Output: - `coherentMembers.csv`

------------------------------------------------------------------------

## 2️⃣ RegulonActivityCalculation.ipynb

Purpose: - Compute regulon activity matrix

Requires: - `regulons.json` - Expression matrix or abundance matrix

Output: - Regulon activity tables

------------------------------------------------------------------------

## 3️⃣ CoxHRCalculationMINER.ipynb

Purpose: - Compute Cox proportional hazards for regulons

Requires: - Survival metadata - Expression/activity matrix -
`regulons.json`

Output: - `CoxProportionalHazardsRegulons.csv`

------------------------------------------------------------------------

## 4️⃣ RegulonRMSTCalculation.ipynb

Purpose: - RMST-based survival comparison

Requires: - `eigengenes.csv` - `overExpressedMembers.csv` -
`underExpressedMembers.csv` - Survival metadata

Output: - RMST summary CSV

------------------------------------------------------------------------

# 5. Validation Notebooks (Require Multiple Cohorts)

If validating across additional datasets (Rembrandt, GSE7696, French,
Ivy Atlas):

You must supply:

-   Cox results per cohort
-   Variance test outputs per cohort
-   Regulon definitions
-   Expression matrices for each cohort

These are referenced as inputs in:

-   `GbmMINERRegulonValidation.ipynb`
-   `MinerRegulatorToRegulonStatisticalTest.ipynb`
-   `MINERDiseaseRelevantC-MFiltering.ipynb`

------------------------------------------------------------------------

# 6. Drug Mapping (Optional Translational Analysis)

Notebook: - `DrugToCMFlowMapping.ipynb`

Requires: - OpenTargets drug-gene mapping tables - ClinicalTrials.gov
drug tables - Significant regulon results

------------------------------------------------------------------------

# 7. Achilles RNAi Notebook (Optional)

Notebook: - `AchillesRNAiDataCompilation.ipynb`

Requires: - `D2_combined_gene_dep_scoresRNAiSymbol.csv` -
`sample_infoAchillesGbm.csv`

------------------------------------------------------------------------

# 8. File Placement Recommendation

Simplest approach: Place all CSV files in the same folder as the
notebooks (`src/`).

Cleaner approach: Create an `Inputs/` folder and update file paths in
notebooks accordingly.

------------------------------------------------------------------------

# 9. Common Failure Points

-   Sample IDs do not match between survival and expression files
-   Hardcoded Windows paths not updated
-   Missing `regulons.json`
-   Missing eigengenes/membership files for RMST

------------------------------------------------------------------------

# 10. Minimal Setup to Run Core Pipeline

To reproduce core survival analysis on TCGA-like data, you only need:

1.  MINER output folder (with `regulons.json`)
2.  Expression matrix
3.  `TCGA_Survival_Gbm.csv`

Then run:

1.  `MINER_networkMapping.ipynb`
2.  `CoxHRCalculationMINER.ipynb`
3.  (Optional) `RegulonRMSTCalculation.ipynb`

------------------------------------------------------------------------

# Final Notes

This repository reproduces portions of analyses described in the manuscript, but
does not contain raw datasets. Users are responsible for supplying
expression matrices, survival data, and MINER outputs.

If errors occur, first verify: - File paths - Column names - Sample ID
consistency