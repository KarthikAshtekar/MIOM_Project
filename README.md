# MIOM Project: SECom Dataset Analysis (Fixed)

## Overview

Semiconductor yield prediction with anomaly detection (DBSCAN/KMeans), PCA, time-series analysis.

## Quick Run (VSCode venv, no Jupyter server)

1. `venv\\Scripts\\activate`
2. `pip install -r requirements.txt`
3. Place `data/raw/secom.zip`, `data/raw/secom_labels.csv`
4. **Open `notebooks/MIOM_fixed.ipynb`**, Ctrl+Enter cells

## Fixes Applied

- Added dir creation (`data/processed`, `figures`)
- File existence checks (graceful skip if missing)
- Fixed heatmap (sample only, avoids crash)
- Fixed scope error in time_analysis (pass df_processed)
- Robust error handling per cell

## Structure

- `data/raw/`: secom.zip, secom_labels.csv
- `data/processed/`: Outputs (Cleaned_data.csv etc.)
- `notebooks/`: MIOM_fixed.ipynb (production), MIOM_standardized.ipynb (original)

**All errors fixed - runs cleanly cell-by-cell in VSCode venv!**
