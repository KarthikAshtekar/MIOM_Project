# MIOM Project: SECOM Semiconductor Process Analysis

## Project Overview

This project analyzes the **SECOM semiconductor manufacturing dataset** to detect abnormal process behavior and relate those abnormalities to product failures.

The work is implemented primarily in `notebooks/MIOM_standardized.ipynb`, which contains the full end-to-end workflow:

1. load and prepare raw SECOM sensor data
2. inspect and handle missing values
3. create a cleaned feature set with missing-value indicators
4. standardize the features
5. reduce dimensionality using PCA
6. detect anomalies using KMeans and DBSCAN
7. compare anomalies against timestamped failure labels
8. perform SPC / Six Sigma style follow-up analysis on the processed outputs

In short, the notebook is trying to answer:

- which process records look abnormal
- whether those anomalies are predictive of failures
- which sensors appear most unstable or most strongly associated with defective output

## Repository Structure

```text
MIOM_Project/
|-- data/
|   |-- raw/
|   |   |-- secom_names.csv
|   |   `-- secom.zip                  # required for a clean rerun, not committed
|   |-- processed/                     # generated notebook outputs
|   |-- secom_temp/                    # extracted SECOM files used by the notebook
|-- notebooks/
|   `-- MIOM_standardized.ipynb
|-- Project_Pipeline_Detailed_Report.txt
|-- requirements.txt
|-- secom_labels.csv
`-- README.md
```

Other files in the repository are mainly course/project documents and reports.

## Actual Project Workflow

### Phase 1: Core Data Pipeline

This is the first half of the notebook and is the main analytical pipeline.

#### 1. Environment setup and path discovery

The notebook:

- imports `pandas`, `numpy`, `matplotlib`, `seaborn`, and `scikit-learn`
- dynamically finds the project root by searching upward for the `data/` folder
- works with three main directories: `data/raw`, `data/processed`, and `data/secom_temp`
- creates missing working directories automatically if needed

This makes the notebook portable enough to run from VS Code or Jupyter without hard-coded machine-specific paths.

#### 2. Raw data loading and extraction

The function `load_and_extract_data()`:

- expects `data/raw/secom.zip`
- extracts it into `data/secom_temp/`
- reads `secom.data`
- assigns generic feature names like `feature_0`, `feature_1`, ..., `feature_589`

Result:

- raw dataset size is about `1567 x 590`

#### 3. Missing-value analysis

The notebook then studies missingness before cleaning the data.

It creates:

- top columns by missing-value ratio
- row-level missingness histogram
- a sampled missingness heatmap

Why this matters:

- the SECOM dataset has a large number of missing sensor values
- the later preprocessing choices are based on that missingness pattern

#### 4. Preprocessing and feature engineering

The function `preprocess_data(df, missing_threshold=0.5)` performs the main cleaning logic:

- removes columns whose missing ratio is `>= 0.5`
- keeps the remaining sensor columns
- creates a binary `_missing` indicator column for each retained feature
- imputes missing numeric values using the median
- combines imputed features and missingness indicators
- drops constant columns that carry no information

Generated outputs:

- `data/processed/Cleaned_data.csv`
- `data/processed/Cleaned_data_Indicated.csv`

This is an important design choice in the project: missingness itself is treated as potentially useful information, not just noise to be removed.

#### 5. Scaling and PCA

The function `apply_scaling_pca(df, n_components=200)` does two things:

- standardizes the real sensor features using `StandardScaler`
- keeps missing-indicator columns unchanged

Then it applies PCA to reduce dimensionality and improve clustering stability.

Generated outputs:

- `data/processed/Final_Scaled_data_Indicated.csv`
- `data/processed/secom_pca_ready.csv`
- `data/processed/secom_pca_scaled.csv`

The notebook reports that the first `200` principal components retain roughly `97.9%` of the variance.

#### 6. Clustering and anomaly detection

The function `cluster_and_detect(df_pca)` runs two anomaly-oriented approaches:

- `KMeans`, using silhouette scores for `k = 2..9`, an elbow plot for model selection, and a final run with `k = 2`
- `DBSCAN`, using a nearest-neighbor distance plot for tuning and a final run with `eps = 16` and `min_samples = 10`

The notebook then:

- stores KMeans cluster labels
- stores DBSCAN labels
- marks DBSCAN noise points (`-1`) as anomalies
- plots anomaly separation in PCA space

This is the main anomaly-detection stage of the project.

#### 7. Time-based failure analysis

The function `time_analysis(df_results, df_processed)` links anomaly results to the label file.

It searches for labels in this order:

1. `data/raw/secom_labels.csv`
2. `data/secom_temp/secom_labels.data`
3. project-root `secom_labels.csv`

Then it:

- parses timestamps
- sorts observations over time
- derives `is_anomaly` from DBSCAN
- derives failure flags from the label file
- plots anomaly/failure behavior by date, hour, weekday, and month
- computes a simple next-record failure precision/recall view
- identifies the top features whose averages differ most between anomaly and normal groups

This is the step where the project connects unsupervised anomaly detection back to the business question: whether abnormal process states can serve as an early warning signal for failure.

### Phase 2: SPC / Six Sigma Follow-Up Analysis

The second half of the same notebook builds on the processed outputs from Phase 1.

It loads:

- `data/processed/Final_Scaled_data_Indicated.csv`
- `data/processed/secom_pca_scaled.csv`
- `data/secom_temp/secom_labels.data`

Then it performs:

#### 8. Sensor-level defect detection using the 3-sigma rule

- computes z-scores on real sensor features
- flags any row where at least one sensor crosses `+- 3 sigma`
- compares predicted anomalies against true labels using a confusion matrix

#### 9. PCA-level defect detection

- repeats a similar `+- 3 sigma` rule on the PCA-transformed feature set
- compares sensor-space detection vs PCA-space detection

This gives a tradeoff:

- sensor-level detection is more sensitive but creates more false alarms
- PCA-level detection is more conservative but misses more true defects

#### 10. Process capability analysis

The notebook selects important sensors using two criteria:

- sensors that most often violate the 3-sigma rule
- sensors whose means differ most between defect and normal groups

For the selected sensors, it computes:

- mean
- standard deviation
- upper and lower specification-style limits
- `Cp`
- `Cpk`

#### 11. Statistical Process Control (SPC)

For one selected sensor, the notebook creates:

- rolling mean chart
- rolling variability chart
- control-limit interpretation around `+- 3 sigma`

This converts the anomaly-detection work into a more operations-oriented process-monitoring view.

## Main Files Generated by the Notebook

When the notebook is run successfully, the important generated datasets are:

- `data/processed/Cleaned_data.csv`
- `data/processed/Cleaned_data_Indicated.csv`
- `data/processed/Final_Scaled_data_Indicated.csv`
- `data/processed/secom_pca_ready.csv`
- `data/processed/secom_pca_scaled.csv`

These are the canonical outputs. The project should treat `data/processed/` as the single source of truth for generated CSV artifacts.

## How An Outsider Should Run This Project After Forking

### 1. Fork the repository on GitHub

On GitHub:

- click `Fork`
- create your own copy under your account

### 2. Clone your fork locally

```powershell
git clone https://github.com/<your-username>/MIOM_Project.git
cd MIOM_Project
```

Optional but recommended:

```powershell
git remote add upstream https://github.com/<original-owner>/MIOM_Project.git
```

### 3. Create and activate a virtual environment

```powershell
python -m venv venv
venv\Scripts\activate
```

### 4. Install dependencies

```powershell
pip install --upgrade pip
pip install -r requirements.txt
```

### 5. Add the missing raw ZIP dataset

Important:

- `data/raw/secom.zip` is required by the notebook's first loading function
- that ZIP is not committed to the repo
- a fresh fork/clone will therefore not contain it automatically

Place the raw SECOM archive at:

```text
data/raw/secom.zip
```

The repo already includes label/name helper files, but for a clean full rerun the ZIP should still be added manually.

### 6. Open the notebook

Open this file in VS Code or Jupyter:

- `notebooks/MIOM_standardized.ipynb`

If using VS Code:

- install the Python and Jupyter extensions if prompted
- select the interpreter from `.\venv`
- run cells from top to bottom

If using classic Jupyter:

```powershell
jupyter notebook
```

Then open `notebooks/MIOM_standardized.ipynb` and run all cells in order.

### 7. Run the notebook from top to bottom

The notebook is stateful, so do not start from the middle on a clean machine.

Recommended execution order:

1. imports and path setup
2. data loading and extraction
3. missing-value analysis
4. preprocessing
5. scaling and PCA
6. clustering and anomaly detection
7. time-series and failure analysis
8. SPC / Six Sigma analysis

### 8. Check the generated outputs

After execution, confirm that these files exist:

- `data/processed/Cleaned_data.csv`
- `data/processed/Cleaned_data_Indicated.csv`
- `data/processed/Final_Scaled_data_Indicated.csv`
- `data/processed/secom_pca_ready.csv`
- `data/processed/secom_pca_scaled.csv`

## Troubleshooting

### Problem: `FileNotFoundError` for `data/raw/secom.zip`

Cause:

- the ZIP file is not committed to the repository

Fix:

- download/add the SECOM raw archive
- place it at `data/raw/secom.zip`

### Problem: notebook cells fail because variables are undefined

Cause:

- the notebook was started from the middle instead of from the top

Fix:

- restart the kernel
- run all cells in order from the beginning

### Problem: plots do not display properly

Fix:

- make sure the notebook kernel is using the project virtual environment
- rerun the import/setup cells first

## Cleanup Done

During repo cleanup, the following redundant root-level artifacts were removed:

- an exact duplicate of `Cleaned_data.csv`
- older root-level processed CSV exports that were superseded by `data/processed/`
- a duplicate metadata text file already present under `data/secom_temp/`

That cleanup makes the current workflow easier to understand and reduces confusion about which files are authoritative.

## Recommended Future Improvement

One small improvement still worth making later:

- update the notebook loader so it can fall back to `data/secom_temp/secom.data` when `data/raw/secom.zip` is not present

That would make the project easier for outsiders to run immediately after forking.
