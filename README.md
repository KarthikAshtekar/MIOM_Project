# MIOM Project: SECOM Process Analysis

This repository contains the MIOM semiconductor-manufacturing project built around the SECOM dataset. The work combines anomaly detection, PCA, time-based failure analysis, and SPC / Six Sigma interpretation.

## Notebook Variants

- [`notebooks/MIOM_standardized.ipynb`](./notebooks/MIOM_standardized.ipynb)
  Clean, baseline end-to-end workflow.
- [`notebooks/MIOM_upgraded.ipynb`](./notebooks/MIOM_upgraded.ipynb)
  Expanded, presentation-oriented version with executive visuals and richer quality-engineering interpretation.
- [`notebooks/MIOM_upgraded_README.md`](./notebooks/MIOM_upgraded_README.md)
  Separate flow guide for the upgraded notebook.

## Repository Layout

```text
MIOM_Project/
|-- data/
|   |-- raw/                # raw dataset assets and labels
|   |-- processed/          # generated CSV outputs
|   `-- secom_temp/         # extracted SECOM files used during runs
|-- docs/
|   |-- notes/              # project notes / pipeline writeups
|   |-- references/         # reference material
|   `-- reports/            # proposal, report, and submission documents
|-- notebooks/
|   |-- MIOM_standardized.ipynb
|   |-- MIOM_upgraded.ipynb
|   `-- MIOM_upgraded_README.md
|-- outputs/
|   `-- figures/
|       `-- miom_upgraded/  # exported PNG figures from the upgraded notebook
|-- requirements.txt
`-- TODO.md
```

## Core Workflow

Across both notebooks, the project flow is:

1. load and extract the raw SECOM process data
2. profile and handle missing values
3. engineer missing-value indicator features
4. scale the cleaned features
5. reduce dimensionality with PCA
6. detect unusual observations with clustering / anomaly methods
7. align anomalies with failure labels over time
8. interpret the results with SPC / Six Sigma style analysis

The upgraded notebook then adds management-facing visuals such as capability, COQ, Pareto, threshold, Hotelling `T²`, and executive-dashboard views.

## Inputs And Outputs

Main expected inputs:

- `data/raw/secom.zip`
- `data/raw/secom_names.csv`
- `data/raw/secom_labels.csv`

Main generated CSV outputs:

- `data/processed/Cleaned_data.csv`
- `data/processed/Cleaned_data_Indicated.csv`
- `data/processed/Final_Scaled_data_Indicated.csv`
- `data/processed/secom_pca_ready.csv`
- `data/processed/secom_pca_scaled.csv`

Main generated figure outputs from the upgraded notebook:

- `outputs/figures/miom_upgraded/`

## Quick Start

```powershell
python -m venv venv
venv\Scripts\activate
pip install -r requirements.txt
jupyter notebook notebooks/MIOM_upgraded.ipynb
```

If you want the cleaner baseline workflow instead, open:

```text
notebooks/MIOM_standardized.ipynb
```

## Notes

- The repository now keeps exported PNG figures out of `notebooks/` so the notebook folder stays cleaner.
- Proposal/report documents have been grouped under `docs/` to reduce root-level clutter.
- The upgraded notebook guide lives in [`notebooks/MIOM_upgraded_README.md`](./notebooks/MIOM_upgraded_README.md).
