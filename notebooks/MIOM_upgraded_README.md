# `MIOM_upgraded.ipynb` Project Flow

This guide explains the end-to-end flow of [`MIOM_upgraded.ipynb`](./MIOM_upgraded.ipynb), what it produces, and where each artifact now lives in the repository.

## What This Notebook Does

`MIOM_upgraded.ipynb` is the presentation-ready version of the MIOM project. It keeps the core SECOM anomaly-detection pipeline and extends it with:

- stronger narrative structure for management review
- additional SPC / Six Sigma style business interpretation
- richer visual outputs and dashboard-style figures
- cost-of-quality, capability, threshold, and executive-summary views

In practical terms, the notebook answers three questions:

1. Which process observations look abnormal?
2. How well do those abnormal observations line up with actual failures?
3. Which sensors, thresholds, and improvement actions matter most from an operations perspective?

## Expected Inputs

The notebook resolves the project root automatically and uses these folders:

- `data/raw/`
- `data/processed/`
- `data/secom_temp/`
- `outputs/figures/miom_upgraded/`

Important input files:

- `data/raw/secom.zip`
- `data/raw/secom_names.csv`
- `data/raw/secom_labels.csv`

It can also fall back to:

- `data/secom_temp/secom_labels.data`

## Flow By Section

### 1. Environment Setup

The setup cells:

- import `pandas`, `numpy`, `matplotlib`, `seaborn`, and `scikit-learn`
- discover `PROJECT_ROOT` dynamically
- initialize reusable paths for raw data, processed data, temp extracts, and generated figures
- create missing working folders automatically

### 2. Raw Data Extraction and Load

`load_and_extract_data()`:

- opens `data/raw/secom.zip`
- extracts the archive into `data/secom_temp/`
- loads `secom.data`
- assigns generic feature names such as `feature_0` to `feature_589`

This is the starting point for the full rerun.

### 3. Missing-Value Profiling

The notebook inspects:

- column-wise missingness
- row-wise missingness
- missing-value heatmaps and distributions

This drives the later preprocessing logic instead of treating nulls as an afterthought.

### 4. Preprocessing and Feature Engineering

The preprocessing block:

- removes features with excessive missingness
- imputes retained numeric features
- adds `_missing` indicator columns
- drops constant columns
- exports cleaned datasets to `data/processed/`

Main outputs from this stage:

- `data/processed/Cleaned_data.csv`
- `data/processed/Cleaned_data_Indicated.csv`

### 5. Scaling and PCA

`apply_scaling_pca()`:

- standardizes the retained features
- preserves missing indicators
- runs PCA
- creates a PCA-ready dataset and a standardized PCA dataset

Outputs:

- `data/processed/Final_Scaled_data_Indicated.csv`
- `data/processed/secom_pca_ready.csv`
- `data/processed/secom_pca_scaled.csv`

### 6. Clustering and Anomaly Detection

The notebook applies:

- `KMeans` for structure discovery
- `DBSCAN` for anomaly / noise-point detection

It then compares cluster and anomaly behavior visually in reduced PCA space.

### 7. Failure Alignment and Time Analysis

`time_analysis()` joins the anomaly results with the label file and evaluates:

- anomaly counts over time
- failure rate by hour / weekday / month
- anomaly-to-future-failure precision and recall
- the features with the strongest abnormal-vs-normal differences

This is where the unsupervised analysis starts turning into an operational signal.

### 8. Sensor-Level SPC and Six Sigma Analysis

The upgraded notebook then pivots from ML-style detection to quality-engineering interpretation:

- 3-sigma defect flagging in sensor space
- PCA-space defect flagging
- confusion-matrix style comparisons
- process capability summaries (`Cp`, `Cpk`)
- control-chart style visual analysis for selected sensors

### 9. Management / Executive Outputs

The later sections build stakeholder-facing visuals such as:

- COQ breakdown
- assignable vs common-cause variation
- Taguchi loss view
- DMAIC improvement story
- capability trend summaries
- quality-dimensions radar
- yield trends
- Pareto sensor ranking
- threshold comparison
- Hotelling `T²` style comparison
- executive dashboard

These are all saved under:

- `outputs/figures/miom_upgraded/`

## Generated Figure Folder

The upgraded notebook now saves all exported visuals into:

```text
outputs/figures/miom_upgraded/
```

Current figure set includes:

- `assignable_variation.png`
- `capability_over_time.png`
- `coq_breakdown.png`
- `dmaic_improve.png`
- `executive_dashboard.png`
- `hotelling_t2.png`
- `monthly_yield.png`
- `pareto_sensors.png`
- `quality_dimensions_radar.png`
- `spc_feature_431.png`
- `spc_feature_577.png`
- `taguchi_loss.png`
- `threshold_analysis.png`
- `variation_type_dist.png`
- `yield_over_time.png`

## Recommended Run Order

Run the notebook top-to-bottom on a clean kernel:

1. setup and path initialization
2. raw data extraction
3. missing-value analysis
4. preprocessing
5. scaling and PCA
6. clustering and anomaly detection
7. failure/time analysis
8. SPC / Six Sigma interpretation
9. executive visuals and dashboards

## Quick Start

```powershell
python -m venv venv
venv\Scripts\activate
pip install -r requirements.txt
jupyter notebook notebooks/MIOM_upgraded.ipynb
```

Before running, ensure:

- `data/raw/secom.zip` exists
- the notebook is run from top to bottom
- the selected interpreter is the project environment

## Repo Layout Relevant To This Notebook

```text
MIOM_Project/
|-- data/
|   |-- raw/
|   |-- processed/
|   `-- secom_temp/
|-- docs/
|   |-- notes/
|   |-- references/
|   `-- reports/
|-- notebooks/
|   |-- MIOM_standardized.ipynb
|   |-- MIOM_upgraded.ipynb
|   `-- MIOM_upgraded_README.md
`-- outputs/
    `-- figures/
        `-- miom_upgraded/
```

## Notes

- `MIOM_standardized.ipynb` remains the cleaner baseline workflow notebook.
- `MIOM_upgraded.ipynb` is the richer, presentation-oriented version.
- Generated CSV files stay in `data/processed/`.
- Generated PNG figures now stay out of `notebooks/`, which keeps the notebook folder cleaner for GitHub and future maintenance.
