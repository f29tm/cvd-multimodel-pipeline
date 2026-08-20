# Cardiovascular Disease Detection using a Multi-model Pipeline

Code and results accompanying the paper of the same name.

**Authors:** Fatimetou Agha, Mohammad Abu Zahra, Wardah Jamil, Mousa Herzallah

**Institution:** Abu Dhabi University

## Contents

| File | Description |
|---|---|
| `CVD_Prediction.ipynb` | Complete pipeline: loading, cleaning, feature engineering, training, evaluation, and every figure in the paper |
| `requirements.txt` | Package versions |

Running the notebook top to bottom reproduces every number and figure reported in the paper.

## Dataset

Kaggle Cardiovascular Disease dataset (Sulianova, 2019) — 70,000 records, semicolon-separated `cardio_train.csv`.

https://www.kaggle.com/datasets/sulianova/cardiovascular-disease-dataset

Download `cardio_train.csv` from the link above and place it in the same directory as the
notebook before running it. The file is not redistributed here.

## Pipeline

The order of these steps matters. Outlier bounds and scaling statistics are fitted on the training partition only, after the split.

1. Drop `id`, remove 24 duplicate rows (70,000 → 69,976)
2. Encode: `age_years = age / 365.25`, `height` to metres, `gender` to 0/1, drop `age`
3. Stratified 80/20 split (55,980 train / 13,996 test), `random_state=42`
4. IQR clipping on `height`, `weight`, `ap_hi`, `ap_lo`, `age_years` — bounds fitted on training data only, applied to both partitions
5. Derive `bmi` and `pulse_pressure` from the clipped values
6. `StandardScaler` fitted on training data only
7. Train and evaluate

No records are deleted during cleaning; out-of-range values are clipped instead.

### Final feature set

`gender`, `height`, `weight`, `ap_hi`, `ap_lo`, `cholesterol`, `gluc`, `smoke`, `alco`, `active`, `age_years`, `bmi`, `pulse_pressure`

## Results

Held-out test set, n = 13,996.

| Model | Accuracy | Precision | Recall | F1 | ROC-AUC |
|---|---|---|---|---|---|
| Gradient Boosting | **0.7370** | 0.7575 | 0.6969 | **0.7259** | 0.8046 |
| Random Forest | 0.7369 | **0.7606** | 0.6912 | 0.7242 | 0.8041 |
| XGBoost | 0.7358 | 0.7576 | 0.6931 | 0.7239 | **0.8057** |
| LightGBM | 0.7357 | 0.7548 | **0.6979** | 0.7252 | 0.8046 |

Differences between models are smaller than the fold-to-fold variation observed under 5-fold stratified cross-validation, so no model is statistically superior to the others.

## Running

```bash
pip install -r requirements.txt
# place cardio_train.csv in this directory first — see Dataset above
jupyter notebook CVD_Prediction.ipynb
```

All randomness is seeded with `random_state=42`.

## Licence

Code in this repository is released under the MIT Licence — see `LICENSE`. The licence covers
the code only; the Cardiovascular Disease dataset is distributed by Kaggle under its own terms.
Please cite the paper if you build on this work.
