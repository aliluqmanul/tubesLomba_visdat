# Person 3 notebook guide

All implementation code is in [03_feature_engineering.ipynb](../03_feature_engineering.ipynb).
Project code uses `.ipynb` format. The notebook is self-contained and imports no local Python modules.
CSV, Parquet, JSON, Markdown, and PNG files are data and report artifacts.

## Running the notebook

Select a Python 3.12 kernel and choose **Restart Kernel & Run All**.
The settings cell provides:

| Setting | Default | Purpose |
|---|---:|---|
| INSTALL_DEPENDENCIES | False | Install libraries into the active kernel using system certificates |
| RUN_CHECKS | True | Run five feature and data-boundary checks |
| RUN_CV | False | Set True to rerun all experiments on the team's five folds |
| EXPORT_DATASETS | False | Set True to rebuild Parquet datasets |
| PROJECT_ROOT | None | Find the repository automatically; set a path if needed |

Notebook sections cover settings, data and folds, row-level features, aggregation,
feature checks, a preview, CV setup, execution or saved results, charts, prediction checks,
exports, team integration, and the full report.

## Data and features

Inputs: `ps-s6e11/data/train.csv`, `test.csv`, `sample_submission.csv`, and `folds.csv`.
The current data has 699,960 training rows, 299,984 test rows, and 24 original features.
The ID is `id`, the target is `is_fraud`, and the metric is ROC-AUC. Use the existing team folds.

| Group | Added features | Description |
|---|---:|---|
| monetary | 4 | Log transforms, amount/balance ratio, balance minus amount |
| activity | 6 | Transaction gap and frequency, amount/velocity interactions, retries/disputes, relative card age |
| temporal | 5 | Sine/cosine encodings of hour and day, hour-of-week index |
| risk | 5 | Risk-flag count and interactions, merchant risk/amount interactions |
| aggregate | 21 | Mean/std/min/max, difference, ratio, and z-score by merchant/channel |

There is no customer ID or full timestamp. Merchant/channel statistics do not represent
customer history. Amount multiplied by transaction count is not actual 24-hour spending.
Unseen categories and zero denominators become missing; unknown aggregate groups use
statistics from that training fold. ID, target, and fold are excluded from model inputs.

## Saved results and new training

The previous experiments selected **risk**, with OOF AUC **0.946503** versus baseline
**0.946318** (delta +0.000185; all five folds improved). The other groups did not improve
OOF AUC with the baseline parameters. These results come from six full experiments.

With `RUN_CV=False`, the notebook loads saved results that match the data and parameters.
It labels them as saved results. Editing feature code does not update saved scores; run CV
to evaluate code changes.

With `RUN_CV=True`, the default paths are:

- New checkpoints and predictions: `ps-s6e11/features/notebook_experiments/`.
- New reports: `reports/notebook/`.
- Previous results remain in `ps-s6e11/features/experiments/` and `reports/`.

Training can resume after an interruption. Resume checks hashes of the active functions,
data, parameters, and library versions. If these change, select new CV and report folders.
The notebook metadata preserves hashes of the source files used during conversion.

Each experiment uses the same LightGBM parameters as the baseline. A combined candidate
runs if more than one group improves OOF by over 0.00001. Feature selection and scoring use
the same folds, so the results are exploratory and need separate confirmation. Gain-based
feature importance does not, by itself, establish a CV improvement.

## Exporting datasets

Set `EXPORT_DATASETS=True` and run the notebook from the top:

- `ps-s6e11/features/train_featured.parquet` and `test_featured.parquet`: all row-level
  candidates (44 features), plus ID and the target in train only.
- `ps-s6e11/features/recommended/`: the best configuration when all features are row-level.
  The current export has 29 features: 24 original features and 5 risk features.
- Each folder includes a `manifest.json` with groups, row counts, and columns.

Fit aggregates separately inside each training fold. Do not create one globally aggregated
file for CV. The current recommended features are `fe_risk_flag_count`,
`fe_vpn_ip_mismatch`, `fe_foreign_ip_mismatch`, `fe_merchant_risk_times_amount`, and
`fe_new_merchant_times_amount`.

## Team handoff

For Person 1's notebook, copy the definitions from sections 3 and 4 (constants, helpers,
`add_row_features`, and `FraudFeatureEngineer`), along with the NumPy/Pandas imports and
the callback in section 12. Use the team's baseline parameters. The callback creates a new
transformer in each fold and fits it only on that fold's training rows.

In Person 1's notebook, call `run_cv('lgb_features', feature_fit_predict)` after the team's
pipeline and callback are defined. `run_cv` remains part of Person 1's pipeline.

Person 4 can use the recommended Parquet files with the team folds and encoding fit on each
training fold. Person 1 handles final selection and submission.
