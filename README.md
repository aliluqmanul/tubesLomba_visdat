# tubesLomba_visdat

## Project workflow

All project development and analysis use **Jupyter notebooks (`.ipynb`)**.
The Person 3 notebook is self-contained: its implementation lives in notebook cells and does not import local `.py` modules.

## Person 3 - Feature Engineering

Open [03_feature_engineering.ipynb](03_feature_engineering.ipynb), select a Python 3.12 kernel,
and choose **Restart Kernel & Run All**.

- By default, the notebook loads saved CV results and checks the features and predictions.
- Set `RUN_CV=True` to rerun the full five-fold training.
- Set `EXPORT_DATASETS=True` to rebuild the Parquet datasets.
- Set `INSTALL_DEPENDENCIES=True` only if the kernel is missing a required library.

The dataset and team folds are in `ps-s6e11/data/`.
The recommended dataset is in `ps-s6e11/features/recommended/`.

- [Usage and integration guide](docs/FEATURE_ENGINEERING.md)
- [Previous experiment results](reports/FEATURE_FINDINGS.md)

The notebook covers row-level features, fold-level aggregation, feature checks, CV evaluation,
comparison charts, dataset export, and a callback for Person 1.


## Run preprocessing and feature engineering in order

1. Open `01_eda_preprocessing.ipynb` in VS Code or Jupyter and select your Python kernel. Set `INSTALL_DEPENDENCIES=True` only if dependencies are missing, run the installation cell, and restart the kernel if requested. Then set it back to `False`.
2. Leave `EXPORT_PREPROCESSED=True` and run all cells. Cleaned train/test files and their provenance manifest are written to `ps-s6e11/preprocessed/`; EDA reports are written to `reports/preprocessing/`.
3. Open `03_feature_engineering.ipynb` using the same Python environment. Keep `USE_PREPROCESSED_DATA=True`, `RUN_CV=False`, and `EXPORT_DATASETS=True`, then run all cells. Install its additional dependencies with its installation switch if needed.
4. Engineered Parquet files are written to `ps-s6e11/features/`. In this quick mode, any displayed CV scores come from compatible saved experiments, not a fresh training run.
5. To evaluate the features again, set `RUN_CV=True` in notebook 03 and rerun it. This trains at least 30 models (six experiments, five folds each). Results go to `ps-s6e11/features/notebook_experiments_preprocessed/` and `reports/notebook_preprocessed/`.

Run notebooks sequentially. They exchange files, so they do not need to share kernel variables. If raw data changes, rerun notebook 01 first. Learned encodings and aggregate statistics are fitted inside the training folds in notebook 03.
