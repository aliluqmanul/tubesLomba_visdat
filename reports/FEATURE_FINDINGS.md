# Feature engineering results

Full data: 699,960 training rows; five folds from data/folds.csv; model seed 42.
All experiments use the same LightGBM parameters and early stopping.
The baseline was rerun in this environment instead of copying a score from an older notebook.

| Experiment | Features | OOF AUC | Delta vs. baseline | Improved folds |
|---|---:|---:|---:|---:|
| baseline | 24 | 0.946318 | +0.000000 | 0/5 |
| monetary | 28 | 0.946259 | -0.000059 | 3/5 |
| activity | 30 | 0.946059 | -0.000259 | 0/5 |
| temporal | 29 | 0.946129 | -0.000189 | 1/5 |
| risk | 29 | 0.946503 | +0.000185 | 5/5 |
| aggregate | 45 | 0.946270 | -0.000048 | 3/5 |

The highest OOF AUC was **risk** (0.946503), using the five risk features.

Feature selection and scoring used the same CV folds, so this gain is exploratory,
not an independent estimate after selection. Confirm it with a separate holdout or evaluation.
Check per-fold deltas in `feature_experiments.csv`; a small difference alone does not
establish a consistent improvement.

## Integration

Create a new `FraudFeatureEngineer` in each fold. Fit it only on that fold's training rows,
then transform validation and test. Mean/std/min/max aggregates describe merchant/channel
groups rather than customer history. OOF and test prediction files are saved per experiment
with the original IDs. No Kaggle submission is made.

Best prediction folder: `ps-s6e11/features/experiments/risk`.

## Dataset details

24 original features: 5 categorical, 6 boolean, and 13 numeric. No full timestamp or customer ID is available.
Fraud rate: 1.6700%.

## Target distribution by fold

- Fold 0: 139,992 rows, fraud 1.6694%.
- Fold 1: 139,992 rows, fraud 1.6701%.
- Fold 2: 139,992 rows, fraud 1.6701%.
- Fold 3: 139,992 rows, fraud 1.6701%.
- Fold 4: 139,992 rows, fraud 1.6701%.
