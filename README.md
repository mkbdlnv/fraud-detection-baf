# Fraud Detection in Financial Transactions — Assignment 1

Comparison of 11 machine-learning classifiers for detecting fraudulent bank-account applications on the
**Bank Account Fraud (BAF) Dataset Suite (NeurIPS 2022)**, evaluated with **Stratified 10-fold cross-validation**.

| | |
|---|---|
| **Dataset** | Bank Account Fraud Dataset Suite, NeurIPS 2022 — `Base.csv` (1,000,000 applications × 32 columns) |
| **Source** | Kaggle: <https://www.kaggle.com/datasets/sgpjesus/bank-account-fraud-dataset-neurips-2022> |
| **Target** | `fraud_bool` (binary, 1 = fraud, ≈1.1% of rows) |
| **Validation** | `StratifiedKFold(n_splits=10, shuffle=True, random_state=42)` |
| **Models** | Ridge (`RidgeClassifier`), Lasso (L1 logistic regression), Elastic Net (L1+L2 logistic regression), KNN, Extra Trees, AdaBoost, Gradient Boosting, XGBoost, LightGBM, CatBoost, HistGradientBoosting |
| **Metrics** | Accuracy, Precision, Recall, F1 (fraud class), ROC-AUC, PR-AUC, training time |

## Run in Google Colab

1. Open `fraud_detection_assignment1.ipynb` in Colab (*File → Upload notebook*, or *File → Open notebook → GitHub* and paste the repository URL).
2. Give the notebook access to Kaggle. Keys are **never** written in the code. Use one of these options:
   - **Colab Secrets (recommended):** click the key icon in the left sidebar and add
     - `KAGGLE_API_TOKEN` = your API token (Kaggle → *Settings* → *API* → *Generate New Token*), **or**
     - `KAGGLE_USERNAME` + `KAGGLE_KEY` (legacy key from `kaggle.json`),

     then switch on *Notebook access* for the secret(s).
   - **Upload `kaggle.json`:** Kaggle → *Settings* → *API* → *Create Legacy API Key* downloads `kaggle.json`. If no secret is found, the notebook asks you to upload this file.
   - If Kaggle asks for it, open the dataset page once and accept its terms.
3. **Runtime → Run all.** The full run (`SAMPLE_SIZE = 100,000`, 10 folds, 11 models) takes about **1.5 hours** on the free Colab CPU runtime (2 cores). Most of that time goes to Lasso and Elastic Net (`saga` solver, about 3 min per fold) and Gradient Boosting (about 1.5 min per fold). Keep the browser tab open. If the session disconnects, run all again: finished models are loaded from the `results/` cache.
4. The last cell packs all outputs into `results.zip` and downloads it.

Settings at the top of the notebook:

| Variable | Default | Meaning |
|---|---|---|
| `QUICK_TEST` | `False` | `True` runs a ~1-minute smoke test (5,000 rows, 3 folds) |
| `SAMPLE_SIZE` | `100_000` | size of the stratified subsample of `Base.csv` (fraud rate is preserved) |
| `KNN_SAMPLE_SIZE` | `30_000` | KNN uses a smaller stratified subsample because its prediction cost grows with training-set size |
| `N_SPLITS` | `10` | number of CV folds |
| `RANDOM_STATE` | `42` | seed for all random operations |
| `FORCE_RETRAIN` | `False` | `False` reuses cached per-model CV results in `results/` after a disconnect |

### Running locally

```bash
pip install -r requirements.txt
# Kaggle credentials: export KAGGLE_API_TOKEN=... or put kaggle.json in ~/.kaggle/
jupyter notebook fraud_detection_assignment1.ipynb
```
You can also put `Base.csv` next to the notebook; then nothing is downloaded.

## Method in brief

- **Preprocessing.** Negative sentinel values (the dataset's encoding of missing values) are converted to NaN in `prev_address_months_count`, `current_address_months_count`, `intended_balcon_amount`, `bank_months_count`, `session_length_in_minutes` and `device_distinct_emails_8w`. `device_fraud_count` (constant) and `month` (time index) are dropped, which leaves 29 input features. Median imputation with missing-value indicators, standardisation (linear models and KNN only) and one-hot encoding run **inside a scikit-learn `Pipeline`**, so they are fitted on the training folds only and nothing leaks between folds.
- **Class imbalance.** Each model weights the classes itself: `class_weight="balanced"`, balanced `sample_weight` (AdaBoost and Gradient Boosting), `scale_pos_weight` (XGBoost) or `auto_class_weights="Balanced"` (CatBoost). No resampling is used.
- **Ridge, Lasso and Elastic Net as classifiers.** The task is binary classification, so these use their classification counterparts with the same regularisation: `RidgeClassifier` (L2), and `LogisticRegression` with an L1 or elastic-net penalty (`solver="saga"`).
- **Evaluation.** Metrics are computed on every fold and reported as mean ± std. Out-of-fold predictions feed the PR/ROC curves and confusion matrices. PR-AUC is the main ranking metric because Accuracy is misleading at a 1.1% fraud rate.

## Key results (Stratified 10-fold CV, 100,000 applications, fraud rate 1.10%)

| Rank | Model | PR-AUC | ROC-AUC | F1 (threshold 0.5) | Fit time / fold |
|---|---|---|---|---|---|
| 1 | AdaBoost | 0.188 ± 0.031 | 0.894 ± 0.018 | 0.096 ± 0.005 | 46.4 s |
| 2 | Gradient Boosting | 0.175 ± 0.032 | 0.893 ± 0.017 | 0.109 ± 0.008 | 102.1 s |
| 3 | LightGBM | 0.164 ± 0.030 | 0.883 ± 0.016 | 0.182 ± 0.017 | 4.1 s |

Table 1 for all 11 models is in [`table1.md`](table1.md). The random-classifier PR-AUC baseline is 0.011. The highest F1 at the default threshold belongs to Extra Trees (0.197 ± 0.025).

## Repository structure

```
├── fraud_detection_assignment1.ipynb   # main notebook (Colab-ready)
├── article_template.md                 # article skeleton: structure, word targets, guiding questions, facts
├── README.md
├── requirements.txt
├── table1.csv / table1.xlsx / table1.md   # Table 1 (+ extended table) - generated
├── table1_extended.csv                    # ROC-AUC, PR-AUC, training time - generated
├── figures/                               # all figures, 300 dpi PNG - generated
└── results/                               # per-fold metrics, feature importance, summary.json - generated
```

## Output files

| File | Content |
|---|---|
| `table1.csv`, `table1.xlsx` (sheet *Table 1*), `table1.md` | Table 1 in the assignment format: Algorithm, number of features, number of targets, k-fold, Accuracy / Precision / Recall / F1 (mean ± std), RMSE / R² (n/a) |
| `table1_extended.csv`, `table1.xlsx` (sheet *Extended*) | ROC-AUC, PR-AUC, F1, fit time per fold, total CV time, rank by PR-AUC |
| `figures/fig01_class_distribution.png` | class balance |
| `figures/fig02_missing_values.png` | share of missing values per feature |
| `figures/fig03_feature_distributions.png` | distributions of key features by class |
| `figures/fig04_categorical_fraud_rate.png` | fraud rate per category |
| `figures/fig05_correlation_matrix.png` | Spearman correlation matrix |
| `figures/fig06_target_correlation.png` | correlation of features with the target |
| `figures/fig07_model_comparison_f1_prauc.png` | F1 and PR-AUC per model (mean ± std) |
| `figures/fig08_fold_boxplots.png` | F1 and PR-AUC per fold (boxplots) |
| `figures/fig09_pr_curves_top3.png`, `fig10_roc_curves_top3.png` | PR and ROC curves of the top-3 models (out-of-fold) |
| `figures/fig11_confusion_matrices_top3.png` | confusion matrices of the top-3 models |
| `figures/fig12_feature_importance_best.png` | permutation feature importance of the best model |
| `figures/fig13_training_time.png` | training time per fold |
| `results/cv_fold_metrics.csv` | every metric for every fold and model |
| `results/feature_importance_<model>.csv` | permutation importance values |
| `results/eda_missing_values.csv` | missing-value statistics |
| `results/summary.json` | key numbers: dataset, sample, best model, top-3, library versions |
| `results.zip` | all of the above in one archive |

## Citation of the dataset

Jesus, S., Pombal, J., Alves, D., Cruz, A., Saleiro, P., Ribeiro, R. P., Gama, J., & Bizarro, P. (2022). *Turning the Tables: Biased, Imbalanced, Dynamic Tabular Datasets for ML Evaluation.* Advances in Neural Information Processing Systems 35 (NeurIPS 2022), Datasets and Benchmarks Track.
