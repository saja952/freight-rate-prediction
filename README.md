# Freight Rate Prediction

Predicts the posted rate of freight loads from their lane, distance, equipment, weight, date and market signals.

The labelled data covers **Jan–Oct 2025**, and the 12,000 loads to predict are in **Nov–Dec 2025**, so the task is a two-month-ahead forecast. The model is validated the same way: each fold trains on the past and tests on the following two months.

The final model combines two parts:
- a **gradient-boosted model** that prices each load from distance, weight, equipment and pickup/delivery coordinates
- a **linear model** for market and calendar effects (market index, trend, quarter-end ramp, day of week), which can extrapolate into future months where a tree model cannot

On the time-based validation folds it scores **1.60% MAPE**, against 4.69% for a lane-median baseline.

## How to run

The whole solution is in `freight-rate-prediction.ipynb
`.

### On Kaggle
1. Upload the files in `data/` as a Kaggle dataset and attach it to the notebook.
2. If the dataset path differs, update `DATA` in section 0 of the notebook.
3. Click **Run All** (about 15 minutes on CPU; no GPU needed).

Outputs are written to `/kaggle/working`.

### Locally
Requires Python 3.10 or newer.

```bash
python -m pip install -r requirements.txt jupyter
jupyter notebook freight-rate-prediction.ipynb

```

Then **Run All**. The notebook reads the input files from `data/` and writes its outputs to the repository root.

### Outputs
- `validation_predictions.csv`: `load_id,predicted_rate` for all 12,000 validation loads
- `december_chart_inputs.csv`: the fixed December rows with `predicted_rate` filled in

### Check the outputs with the provided scorer
```bash
python data/score.py --predictions validation_predictions.csv --december-predictions december_chart_inputs.csv
```

Results were produced with the library versions pinned in `requirements.txt` (scikit-learn 1.6.1). Other versions can shift the scores very slightly.
