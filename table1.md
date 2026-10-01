## Table 1. Stratified k-fold cross-validation results (mean ± std over folds)

| Algorithm                   |   Number of features |   Number of targets | k-fold validation   | Accuracy        | Precision       | Recall          | F1              | RMSE   | R2   |
|:----------------------------|---------------------:|--------------------:|:--------------------|:----------------|:----------------|:----------------|:----------------|:-------|:-----|
| Ridge (RidgeClassifier)     |                   29 |                   1 | Stratified 10-fold  | 0.8096 ± 0.0055 | 0.0449 ± 0.0022 | 0.8024 ± 0.0472 | 0.0850 ± 0.0043 | —      | —    |
| Lasso (LogReg, L1)          |                   29 |                   1 | Stratified 10-fold  | 0.8094 ± 0.0050 | 0.0448 ± 0.0022 | 0.8014 ± 0.0499 | 0.0848 ± 0.0043 | —      | —    |
| Elastic Net (LogReg, L1+L2) |                   29 |                   1 | Stratified 10-fold  | 0.8094 ± 0.0048 | 0.0449 ± 0.0022 | 0.8032 ± 0.0505 | 0.0850 ± 0.0041 | —      | —    |
| KNN (n = 30,000)*           |                   29 |                   1 | Stratified 10-fold  | 0.9890 ± 0.0001 | 0.0000 ± 0.0000 | 0.0000 ± 0.0000 | 0.0000 ± 0.0000 | —      | —    |
| Extra Trees                 |                   29 |                   1 | Stratified 10-fold  | 0.9710 ± 0.0016 | 0.1418 ± 0.0187 | 0.3218 ± 0.0418 | 0.1967 ± 0.0253 | —      | —    |
| AdaBoost                    |                   29 |                   1 | Stratified 10-fold  | 0.8371 ± 0.0072 | 0.0513 ± 0.0028 | 0.7869 ± 0.0503 | 0.0964 ± 0.0053 | —      | —    |
| Gradient Boosting           |                   29 |                   1 | Stratified 10-fold  | 0.8691 ± 0.0045 | 0.0588 ± 0.0044 | 0.7235 ± 0.0577 | 0.1087 ± 0.0082 | —      | —    |
| XGBoost                     |                   29 |                   1 | Stratified 10-fold  | 0.9545 ± 0.0019 | 0.1073 ± 0.0127 | 0.4288 ± 0.0632 | 0.1716 ± 0.0211 | —      | —    |
| LightGBM                    |                   29 |                   1 | Stratified 10-fold  | 0.9527 ± 0.0016 | 0.1125 ± 0.0098 | 0.4785 ± 0.0567 | 0.1821 ± 0.0165 | —      | —    |
| CatBoost                    |                   29 |                   1 | Stratified 10-fold  | 0.9261 ± 0.0031 | 0.0825 ± 0.0084 | 0.5638 ± 0.0711 | 0.1439 ± 0.0150 | —      | —    |
| HistGradientBoosting        |                   29 |                   1 | Stratified 10-fold  | 0.9366 ± 0.0021 | 0.0934 ± 0.0064 | 0.5457 ± 0.0450 | 0.1595 ± 0.0111 | —      | —    |

* KNN is evaluated on a stratified subsample of 30,000 rows; all other models on 100,000 rows (stratified subsample of Base.csv, fraud rate 1.10%). Precision/Recall/F1 for the fraud class at the default threshold. RMSE and R2 are not applicable to classification.

## Table 2. Extended results: ranking metrics and training time (sorted by PR-AUC)

|   Rank (PR-AUC) | Algorithm                   |   Samples | ROC-AUC         | PR-AUC          | F1              |   Fit time per fold, s |   Total CV fit time, s |
|----------------:|:----------------------------|----------:|:----------------|:----------------|:----------------|-----------------------:|-----------------------:|
|               1 | AdaBoost                    |    100000 | 0.8938 ± 0.0182 | 0.1877 ± 0.0312 | 0.0964 ± 0.0053 |                  46.37 |                  463.7 |
|               2 | Gradient Boosting           |    100000 | 0.8933 ± 0.0170 | 0.1748 ± 0.0317 | 0.1087 ± 0.0082 |                 102.12 |                 1021.2 |
|               3 | LightGBM                    |    100000 | 0.8829 ± 0.0163 | 0.1635 ± 0.0295 | 0.1821 ± 0.0165 |                   4.09 |                   40.9 |
|               4 | CatBoost                    |    100000 | 0.8712 ± 0.0237 | 0.1624 ± 0.0257 | 0.1439 ± 0.0150 |                  10.65 |                  106.5 |
|               5 | Ridge (RidgeClassifier)     |    100000 | 0.8863 ± 0.0185 | 0.1586 ± 0.0346 | 0.0850 ± 0.0043 |                   0.36 |                    3.6 |
|               6 | HistGradientBoosting        |    100000 | 0.8795 ± 0.0139 | 0.1562 ± 0.0198 | 0.1595 ± 0.0111 |                  11.33 |                  113.3 |
|               7 | Lasso (LogReg, L1)          |    100000 | 0.8849 ± 0.0188 | 0.1524 ± 0.0302 | 0.0848 ± 0.0043 |                 189.73 |                 1897.3 |
|               8 | Elastic Net (LogReg, L1+L2) |    100000 | 0.8848 ± 0.0189 | 0.1515 ± 0.0320 | 0.0850 ± 0.0041 |                 165.86 |                 1658.6 |
|               9 | XGBoost                     |    100000 | 0.8672 ± 0.0161 | 0.1441 ± 0.0258 | 0.1716 ± 0.0211 |                   5.42 |                   54.2 |
|              10 | Extra Trees                 |    100000 | 0.8666 ± 0.0149 | 0.1354 ± 0.0282 | 0.1967 ± 0.0253 |                  13.12 |                  131.2 |
|              11 | KNN (n = 30,000)*           |     30000 | 0.6931 ± 0.0400 | 0.0845 ± 0.0468 | 0.0000 ± 0.0000 |                   0.08 |                    0.8 |
