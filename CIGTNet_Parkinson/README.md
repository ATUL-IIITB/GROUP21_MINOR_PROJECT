# Download Dataset : https://drive.google.com/drive/folders/12HIqDBTWdwwxM_MRognHcMHQi7UZqxZJ?usp=sharing

# Parkinson_Gait_CIGTNet

## A Clinically-Informed Multiscale Graph-Transformer Framework for Explainable Parkinsonian Gait Severity Assessment from Multisensor Time-Series Data

This repository contains the completed research workflow currently documented through **Notebook 11 (Baseline Models)** and its companion **Notebook 11A (Baseline Metric Visualizations)**. The work covers raw-data exploration, metadata mapping, linear/angular kinematic merging, missing-data analysis and handling, GSN/TBSN sequence normalization, scaling, fixed-length windowing, preprocessing validation, baseline model training, and baseline result visualization.

> **Current scope:** Notebooks 01–11 and 11A are included in this README. Notebooks 12–18 are the planned advanced-model and final-evaluation roadmap; they are not represented as completed in this project snapshot.

---

## 1. Project status at a glance

| Stage | Notebook(s) | Current recorded status |
|---|---|---|
| Dataset exploration and metadata mapping | 01–02 | Ready; raw-data integrity checks passed |
| Kinematic stream merging | 03 | 881 successful merges; 3 documented merge failures |
| Missing-data analysis and handling | 04–05 | Analysis completed; 881 trials processed by the handling run |
| Sequence normalization | 06–07 | GSN and TBSN runs completed to 751 frames; cohort-count discrepancy documented below |
| Scaling | 08 | Four report-aligned scaling branches validated for the current run |
| Windowing and preprocessing QA | 09–10 | 3,524 windows generated; recorded acceptance checks passed |
| Baseline models | 11 | 5 models × 4 branches = 20 fresh model/branch runs; final status READY |
| Baseline visualizations | 11A | Loss, accuracy, and confusion-matrix figures saved; notebook reports all visualization checks passed |

The statuses above are taken from the execution outputs embedded in the uploaded notebooks. They are not a substitute for rerunning the workflow on a new machine or verifying that every local artifact is present.

---

## 2. Dataset overview

The dataset exploration notebook discovered the following properties at runtime:

- **4,831 raw files** were discovered in the configured raw-data tree.
- **4,515 files** were inventoried under the `C3Dfiles` directory.
- **26 subjects** were observed, with both `on` and `off` medication states represented.
- **884 walking-trial keys** were initially identified for cross-modality pairing.
- The kinematic schema contained **135 linear features** and **45 angular features**, for **180 sensor features** in total.
- `frame` and `time` are retained as separate coordinates; the signal model input excludes both.
- The measured sampling frequency was approximately **142.86 Hz**.
- Across 884 measured trial sequences, the observed length ranged from **175 to 6,672 frames**, with mean **562.72** and median **474** frames.

The raw-data tree contains C3D files and derived kinematic/time-series exports. The model pipeline uses the linear and angular kinematic signals and associated clinical metadata; it should not be described as training directly on video.

### Clinical target

The baseline classification target is the condition-matched walking UPDRS-III score:

`label_updrs_iii_walking`

The baseline task uses three classes: **0, 1, and 2**.

---

## 3. Notebook sequence and responsibilities

Run the official notebooks in numerical order. Notebook 11A is an additional visualization companion to Notebook 11; it is not a replacement for Notebook 12.

| # | Notebook | Main responsibility | Principal handoff / output |
|---:|---|---|---|
| 01 | `01_Dataset_Exploration.ipynb` | Runtime-driven discovery of raw files, subjects, medication states, modalities, feature schemas, sequence lengths, sampling properties, clinical metadata, and raw-data integrity. It does not impute, normalize, scale, window, or train models. | Dataset/file inventories, feature and clinical schemas, sequence summaries, provenance, dataset manifest, diagnostic figures, and artifact audit. |
| 02 | `02_File_Identification_and_Metadata.ipynb` | Deterministically maps linear and angular kinematic files to canonical trial keys, checks readability and pairing, and identifies duplicate/conflicting mappings. | `data/metadata/trial_metadata_index.csv`, `data/metadata/file_metadata_index.csv`, pairing validation and identification-problem reports. |
| 03 | `03_Linear_Angular_Data_Merging.ipynb` | Merges paired linear/angular streams while checking temporal alignment, preserving source signal values, and recording feature provenance. | Merged trial files, `merged_trial_manifest.csv`, `merged_feature_provenance.csv`, merge failure report, schema/alignment audits. |
| 04 | `04_Missing_Data_Analysis.ipynb` | Audits missing values, missing runs, affected trials/features, boundary gaps, and sampling characteristics. This notebook is analysis-only and does not repair data. | Missingness summaries by cell, feature, trial, subject, condition, modality, and burst; evidence-based missing-data policy and audit. |
| 05 | `05_Missing_Data_Handling.ipynb` | Applies the documented missing-data handling protocol to merged trials, records imputation events, and preserves raw and merged inputs. | Cleaned trial files under `data/cleaned/`, handled-data mirror, imputation audit, handling summary, and `cleaned_trial_manifest.csv`. |
| 06 | `06_Sequence_Normalization_GSN.ipynb` | Applies General Sequence Normalization (GSN / Method 1) to a target length of 751 frames. Short sequences are extended by repetition with remainder; long sequences are truncated; exact-length sequences are retained. | `data/normalized/gsn/`, matching validity masks, `gsn_manifest.csv`, GSN parameters, feature schema, validation report, diagnostic figures. |
| 07 | `07_Trend_Based_Sequence_Normalization_TBSN.ipynb` | Applies Trend-Based Sequence Normalization (TBSN / Method 2), deriving increasing/decreasing trends per sensor feature and extrapolating/cycling them to the target length. | `data/normalized/tbsn/`, masks, `tbsn_manifest.csv`, trend statistics, edge-case audit, parameters, validation report, diagnostic figures. |
| 08 | `08_Final_Normalization_and_Scaling.ipynb` | Creates and independently validates four report-aligned scaling branches, including scaler statistics, masks, provenance, and run manifests. It does not split data or train a model. | Run-scoped scaled feature exports, scaler artifacts, masks, audit tables, `run_manifest.json`, and `LATEST_SCALING_RUN.json`. |
| 09 | `09_Sequence_Windowing_and_Label_Preparation.ipynb` | Creates fixed-length windows, aligns clinical labels and medication metadata, builds split metadata, and writes window manifests and arrays. | `window_manifest.csv`, window summary/rejection/label audits, `data/final/` window arrays and masks, split metadata, and trial-split scaled feature files used by the baseline workflow. |
| 10 | `10_Data_Quality_and_Preprocessing_Validation.ipynb` | Independently audits window length/stride, one-window-per-trial behaviour, provenance, dimensions, feature ordering, labels, masks, split assignments, and reproducibility artifacts. | Part 4 leakage/feature-integrity/validation/acceptance tables and a recorded `READY FOR PART 5` handoff. |
| 11 | `11_Baseline_Models.ipynb` | Trains and evaluates five baseline architectures across four preprocessing branches using the `trial_split` partition. | Model checkpoints, epoch histories, held-out predictions, per-model metrics, and consolidated baseline result tables. |
| 11A | `11A_Baseline_Metric_Visualizations.ipynb` | Reads Notebook 11 artifacts and produces publication-style training/validation curves and held-out confusion matrices. It does not train models. | 26 PNG/PDF figures and four CSV exports under `figures/model/baseline_metric_visualizations/`. |

---

## 4. Data-processing methodology

### 4.1 File mapping and merging

Notebook 02 builds a canonical index of the linear and angular kinematic files and validates that the two modalities can be paired by walking-trial identity. Notebook 03 consumes this index rather than rediscovering file relationships.

The merged feature schema is:

- `frame`
- `time`
- 135 linear kinematic features
- 45 angular kinematic features

This produces **182 columns** in the merged table. The feature provenance table records the source modality and original feature identity.

The recorded merge run attempted 884 paired trials. It successfully merged **881 trials** and documented **3 failures**. Downstream notebooks use the successful merged cohort; failed trials are recorded rather than silently treated as successful data.

### 4.2 Missing-data audit and repair

Notebook 04 reported:

- **881 merged trials** analyzed.
- **89,293,140** signal cells inspected.
- **670,881** missing cells, corresponding to approximately **0.7513%** of signal cells.
- **9,291** missing runs.

Notebook 05 reports processing 881 trials, imputing 670,881 cells, retaining 180 features, excluding zero trials in that execution, and leaving zero missing cells in its cleaned outputs. Its documented repair cases are boundary-adjacent copying, column-wise linear interpolation for internal gaps, isolated-neighbour averaging, and a conditional rule for fully missing signal columns. Each imputed observation is recorded in an audit.

### 4.3 Sequence normalization

Both normalization methods use **751 frames** as the configured target length (approximately 5.01 seconds at the observed sampling interval).

**GSN (Notebook 06)**

- If a sequence is longer than 751 frames, retain the first 751 frames.
- If it is shorter than 751 frames, repeat the original sequence and append the required remainder from its beginning.
- If it already has 751 frames, retain it unchanged.
- Frame numbering is sequential; timestamps for extended data follow the configured dominant interval pattern.
- A validity mask accompanies each sequence.

The executed GSN run reports the following operation distribution over 881 processed trials: 797 repetition-with-remainder, 83 truncation, and 1 unchanged.

**TBSN (Notebook 07)**

- Analyze each of the 180 sensor features independently.
- Identify contiguous increasing and decreasing trends and their average rates per frame.
- Start extrapolation from the last observed trend direction and alternate/cycle trend windows until 751 frames are reached.
- Handle constant, monotonic, and insufficient-valid-observation cases deterministically.
- Keep `frame` and `time` out of sensor trend extraction and maintain a matching validity mask.

The executed TBSN run reports 797 trend-based extrapolations, 83 truncations, and 1 unchanged sequence, with 27,357 edge-case events audited.

### 4.4 Scaling branches

The four report-aligned branch definitions are:

| Branch | Sequence normalization | Scaling | Scaling granularity |
|---|---|---|---|
| `gsn_minmax` | GSN | Min-Max | Sequence-wise (per trial, per feature) |
| `gsn_zscore` | GSN | Z-score | Sequence-wise (per trial, per feature) |
| `tbsn_minmax` | TBSN | Min-Max | Channel-wise |
| `tbsn_zscore` | TBSN | Z-score | Channel-wise |

Notebook 08 records a separate, run-scoped export for these four report-reproduction branches. It explicitly notes that its channel-wise TBSN statistics are pooled over all eligible trials at that stage and are **not train-only**. Do not use that full-cohort scaling output as evidence of leakage-free predictive evaluation.

Notebook 09 also builds the split-specific scaled feature files under `data/processed/scaled_features/` that Notebook 11 validates and consumes for its `trial_split` experiment. Keep these two scaling artifact workflows distinct when tracing provenance.

### 4.5 Windowing and labels

Notebook 09 uses:

- Window length `W = 751`.
- Stride `S = 751`.
- One complete, non-overlapping window per normalized trial and branch.
- 180 sensor features per frame; `frame` and `time` are not included in model input `X`.
- A matching validity mask and condition-matched clinical metadata.

The separate stride value is an implementation choice to create one complete window from each fixed-length sequence; the historical report specifies the 751-frame sequence length but does not specify a sliding-window stride.

The executed windowing run reports **3,524 windows** and **zero rejected windows** (881 trial sequences × 4 branches).

---

## 5. Baseline model experiments

### 5.1 Models

Notebook 11 constructs and evaluates:

1. `CNN1D`
2. `LSTM`
3. `BiLSTM`
4. `BiLSTM_CNN`
5. `Classical_LR` (Logistic Regression)

The same four preprocessing branches are evaluated for each model, producing **20 model/branch combinations**.

### 5.2 Training configuration recorded in Notebook 11

- Random seed: `42`.
- Input tensor: `751 × 180` per trial/window.
- Target: `label_updrs_iii_walking`, three classes (`0`, `1`, `2`).
- `CNN1D`, `LSTM`, and `BiLSTM`: Adam, learning rate `1e-3`, batch size `32`, maximum 100 epochs, early-stopping patience 10.
- `BiLSTM_CNN`: AdamW, learning rate `1e-4`, batch size `192`, maximum 400 epochs, early-stopping patience 10, ReduceLROnPlateau scheduler, gradient clipping at 1.0; checkpoint selection uses validation accuracy with validation loss as a tie-break.
- `Classical_LR`: Logistic Regression using LBFGS, with a maximum of 300 iterations.
- The notebook records best checkpoints, histories, test predictions, and classification metrics.

The executed notebook audit reports **20/20 combinations completed**, all freshly trained in that run, no reused checkpoints, and no missing combinations. Its final status is `READY`.

### 5.3 Recorded held-out test accuracy and macro F1

The following values are copied from the executed `baseline_final_test_metrics.csv` summary embedded in Notebook 11. Each cell is shown as **accuracy / macro F1**.

| Model | GSN + Min-Max | GSN + Z-score | TBSN + Min-Max | TBSN + Z-score |
|---|---:|---:|---:|---:|
| CNN1D | 0.9771 / 0.9763 | 0.9847 / 0.9815 | 0.9008 / 0.8929 | 0.9847 / 0.9815 |
| LSTM | 0.9542 / 0.9504 | 0.9466 / 0.9407 | 0.9160 / 0.9102 | 0.9160 / 0.9118 |
| BiLSTM | 0.9847 / 0.9815 | 0.9847 / 0.9815 | 0.9389 / 0.9347 | 0.9847 / 0.9830 |
| BiLSTM-CNN | 0.9313 / 0.9239 | 0.9771 / 0.9748 | 0.9771 / 0.9751 | 0.9847 / 0.9830 |
| Classical Logistic Regression | 0.9542 / 0.9548 | 0.9847 / 0.9815 | 0.8855 / 0.8820 | 0.9847 / 0.9815 |

Other recorded test metrics include balanced accuracy, weighted F1, Cohen's kappa, mean absolute error, and class-wise precision/recall/F1 in the classification reports and result tables.

> **Evaluation limitation — important:** Notebook 11 uses a **trial-wise 70/15/15 split** (`trial_split`): 619 training trials, 131 validation trials, and 131 test trials. Notebook 11 explicitly reports that all **26 subjects appear in more than one partition** (and all 26 appear in all three partitions). Therefore these results are **not subject-independent** and must not be presented as evidence of generalization to unseen subjects. The high scores should be interpreted in light of this split design. A subject-disjoint evaluation is required before making subject-independent performance claims.

---

## 6. Baseline metric visualizations (Notebook 11A)

Notebook 11A reads the saved baseline artifacts; it does not retrain any model. It generates the following figures:

- **Training/validation loss vs epochs:** one 2×2 branch-comparison figure for each of `CNN1D`, `LSTM`, `BiLSTM`, and `BiLSTM_CNN`.
- **Training/validation accuracy vs epochs:** one 2×2 branch-comparison figure for each of the same four deep models.
- **Held-out test confusion matrices:** one 2×2 branch-comparison figure for each of all five models, including `Classical_LR`.

Output directory:

`figures/model/baseline_metric_visualizations/`

Figure filename patterns:

- `{MODEL}_training_validation_loss.png` and `.pdf`
- `{MODEL}_training_validation_accuracy.png` and `.pdf`
- `{MODEL}_confusion_matrices.png` and `.pdf`

The notebook also saves four CSV files:

- `training_validation_loss_curves.csv`
- `training_validation_accuracy_curves.csv`
- `confusion_matrix_counts.csv`
- `baseline_test_evaluation_summary.csv`

The embedded execution output reports 4 loss figures, 4 accuracy figures, and 5 confusion-matrix figures, each saved as PNG and PDF: **13 PNG + 13 PDF = 26 image files**, plus **4 CSV files**. The recorded visualization audit reports `ALL VALIDATION CHECKS PASSED`.

---

## 7. Important methodological notes and items to reconcile

These points are retained explicitly because the uploaded notebooks contain conflicting statements or distinct split/artifact workflows. They should be resolved before final thesis claims are made.

1. **880 vs 881 eligible trials:** The introductory methodology text in Notebook 06 says 880 trials are eligible and one trial (`SUB16_off_walk_4`) is excluded. However, the executed Notebook 06 output reports 881 eligible trials and zero exclusions; Notebook 07 and Notebook 08 also report 881 trials per source/branch. Notebook 05 reports that 881 trials were processed. Reconcile the actual `cleaned_trial_manifest.csv` and downstream manifests before fixing the authoritative cohort count in the final report.
2. **Missing-data zero-fill policy:** Notebook 05 describes a conditional all-NaN-column repair case, while its runtime-loaded policy output states `Zero-filling allowed: False`. Confirm the exact behaviour from the current policy/configuration and imputation audit before describing zero-fill as an executed operation.
3. **Two distinct split descriptions:** Notebook 09 records a subject-level summary of 18/4/4 subjects (with 589/134/158 trials), but Notebook 11's actual `trial_split` experiment uses 619/131/131 trials and reports all 26 subjects in every partition. These are not interchangeable. The baseline results in this snapshot are trial-wise, not subject-disjoint.
4. **Scaling artifact provenance:** Notebook 08's channel-wise TBSN scaling description says its statistics pool all eligible trials and are not train-only. Notebook 09 separately creates split-specific scaled files that Notebook 11 reads. Keep the exact artifact/run manifests attached to every reported result and do not label full-cohort scaling as leakage-free.
5. **Notebook 10 vs Notebook 11 split audit:** Notebook 10's recorded acceptance table includes a passing subject-leakage check, while Notebook 11 later documents subject overlap in the baseline `trial_split`. The Notebook 11 partition report must be considered when describing baseline evaluation validity.

These notes do not invalidate the fact that the uploaded notebooks executed and generated their recorded artifacts. They identify limits on how those outputs should be interpreted and reported.

---

## 8. Reproducibility and data-integrity principles

The current notebook workflow implements or documents the following safeguards:

- Raw input data under `data/raw/` is treated as read-only and integrity snapshots are recorded.
- File/trial matching is deterministic and uses canonical trial identifiers.
- Merge failures and processing anomalies are recorded in manifests/audit tables rather than silently discarded.
- Feature provenance is maintained across linear/angular merging.
- GSN/TBSN outputs include manifests and validity masks.
- Notebook 08 validates the current run's outputs, scalers, numerical properties, masks, and branch definitions.
- Notebook 09 writes trial/window provenance and label alignment information.
- Notebook 10 runs preprocessing acceptance and reproducibility checks.
- Notebook 11 uses a fixed seed, checks expected input shapes/classes/partitions, writes model checkpoints and result artifacts, and audits all 20 model/branch combinations.
- Notebook 11A checks input provenance and test prediction counts before plotting.

Do not manually edit generated manifests, prediction files, masks, or metric tables. If an upstream configuration or source artifact changes, rerun the dependent notebooks in order and retain the corresponding run identifiers and manifests.

---

## 9. Key project directories and artifacts

The notebooks use the following principal directories (some outputs are run-scoped):

```text
project-root/
├── configs/
│   ├── config.yaml
│   ├── data_config.yaml
│   ├── experiment_config.yaml
│   └── training_config.yaml          # written/updated by Notebook 09
├── data/
│   ├── raw/                          # original data; read-only
│   ├── metadata/                     # subject, trial, feature, label, split manifests
│   ├── merged/                       # successful linear/angular merges
│   ├── cleaned/                      # handled/imputed merged trials
│   ├── normalized/
│   │   ├── gsn/
│   │   └── tbsn/
│   ├── processed/                    # scaled features and run manifests
│   └── final/                        # window arrays and masks
├── src/                              # reusable preprocessing, model and evaluation code
├── results/
│   ├── tables/                       # inventories, manifests, histories, metrics, audits
│   ├── predictions/                  # held-out test predictions
│   └── checkpoints/                  # baseline model checkpoints
├── figures/
│   ├── preprocessing/                # data quality and normalization diagnostics
│   └── model/
│       └── baseline_metric_visualizations/
├── notebooks/
│   ├── 01_Dataset_Exploration.ipynb
│   ├── ...
│   ├── 11_Baseline_Models.ipynb
│   └── 11A_Baseline_Metric_Visualizations.ipynb
└── research/
    └── PROJECT_STATE.md              # project handoff/status, when present
```

The uploaded notebooks use configuration files and the project source modules under `src/`. Place the original dataset in the configured raw-data location and verify the paths in `configs/` before execution.

---

## 10. Execution order

1. Run `01_Dataset_Exploration.ipynb`.
2. Run `02_File_Identification_and_Metadata.ipynb`.
3. Run `03_Linear_Angular_Data_Merging.ipynb`.
4. Run `04_Missing_Data_Analysis.ipynb`.
5. Run `05_Missing_Data_Handling.ipynb`.
6. Run `06_Sequence_Normalization_GSN.ipynb`.
7. Run `07_Trend_Based_Sequence_Normalization_TBSN.ipynb`.
8. Run `08_Final_Normalization_and_Scaling.ipynb`.
9. Run `09_Sequence_Windowing_and_Label_Preparation.ipynb`.
10. Run `10_Data_Quality_and_Preprocessing_Validation.ipynb`.
11. Run `11_Baseline_Models.ipynb`.
12. Run `11A_Baseline_Metric_Visualizations.ipynb` after Notebook 11 has generated its histories, metrics, and predictions.

For each notebook, use a fresh kernel and execute all cells from top to bottom. Check the notebook's final readiness output and generated artifacts before proceeding. The visualization notebook is a read-only consumer of Notebook 11 outputs; it should not train models.

---

## 11. Planned work after the current snapshot

The project scaffold defines the following next stages. They are **planned**, not completed in the uploaded notebook set covered by this README:

| # | Planned notebook | Planned responsibility |
|---:|---|---|
| 12 | `12_Multiscale_Temporal_Encoder.ipynb` | Short-, medium-, and long-term temporal feature encoding |
| 13 | `13_Biomechanical_Graph_Construction.ipynb` | Skeletal anatomy, bilateral symmetry, and adaptive graph matrices |
| 14 | `14_Graph_Transformer.ipynb` | Spatial graph transformer with anatomical structural biases |
| 15 | `15_Temporal_Graph_Cross_Attention.ipynb` | Bidirectional fusion between temporal and graph streams |
| 16 | `16_Medication_Aware_Learning.ipynb` | Medication conditioning and ON/OFF representation learning |
| 17 | `17_CIGTNet_Ordinal_Multitask_Model.ipynb` | Full CIGT-Net with ordinal and continuous severity estimation |
| 18 | `18_Explainability_and_Final_Evaluation.ipynb` | Temporal, channel, body-region, and graph-edge explanations plus final evaluation |

Before relying on the final performance claims, prioritize resolving the cohort-count and split-design discrepancies above, then perform a strictly subject-disjoint model evaluation.

---

## 12. Research-use statement

This project is intended for research-oriented analysis of Parkinsonian gait severity using multisensor kinematic time-series data. The notebooks implement data processing and machine-learning experiments; the outputs are not a clinical diagnosis or a substitute for assessment by a qualified clinician.
