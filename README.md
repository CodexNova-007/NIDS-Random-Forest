# Network Intrusion Detection with a Balanced Random Forest

This project trains a multiclass network intrusion detection model on flow-level network traffic. It uses the CICIDS2017-style CSV files in `MachineLearningCVE/`, combines the data into `merged_data.csv`, and trains a scikit-learn pipeline with preprocessing and a `BalancedRandomForestClassifier`.

## What the project does

The notebook:

1. Loads the merged flow dataset and inspects its shape, types, duplicates, and class distribution.
2. Removes constant and known duplicate features.
3. Splits the data into training and test sets with a fixed random seed (`42`).
4. Imputes missing rate features and applies `RobustScaler` to numeric features.
5. Trains a balanced random forest with 200 trees and per-tree class sampling capped at 500 rows per class.
6. Reports per-class precision, recall, F1 score, accuracy, macro F1, and a confusion matrix.
7. Saves the trained pipeline and sampling strategy when model export is enabled.

## Dataset

The source data is based on the CICIDS2017 intrusion-detection flow dataset. The local project currently contains eight daily CSV files under `MachineLearningCVE/` and a generated `merged_data.csv` with 2,830,743 rows and 79 columns.

The raw CSV files and merged dataset are intentionally excluded from Git because they are too large for a normal GitHub repository. Download or prepare the data locally, then place `merged_data.csv` in the repository root before running the notebook.

The target column is `Label`. The observed labels include benign traffic, DDoS, PortScan, DoS variants, brute-force attacks, Bot, Web Attack variants, Infiltration, and Heartbleed.

## Results recorded in the notebook

The Balanced Random Forest evaluation recorded:

- Accuracy: approximately `0.99`
- Macro F1: `0.7428`
- Weighted F1: approximately `0.99`

Accuracy is high partly because the dataset is strongly imbalanced. Macro F1 and the per-class report are more informative for rare attacks. In the recorded run, rare classes such as Infiltration and Web Attack variants had substantially lower precision/F1 than common classes, so this model should be treated as a research/educational baseline rather than a production security control.

## Setup

Python 3.10 or 3.11 is recommended.

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

## Run the project

1. Put `merged_data.csv` in the repository root.
2. Start Jupyter or open `Project.ipynb` in VS Code with the project virtual environment selected.
3. Run the notebook cells from top to bottom.
4. Review the classification report, macro F1, and confusion matrix.

The notebook can export the fitted pipeline to `models/rf_pipeline.pkl` and the sampling configuration to `models/sampling_strategy.pkl`. These generated files are ignored by Git; regenerate them locally when needed.

## Repository layout

```text
.
├── Project.ipynb                 # Analysis, training, and evaluation notebook
├── MachineLearningCVE/           # Local source CSV files; not committed
├── merged_data.csv               # Generated merged dataset; not committed
├── requirements.txt              # Python dependencies
├── .gitignore                    # Excludes datasets and generated artifacts
└── models/                       # Generated model files; not committed
```

## Limitations and next steps

- The train/test split is random rather than time-based or capture-session-based, so results may be optimistic when traffic from the same capture appears in both sets.
- Several attack classes have very few examples, making their metrics unstable.
- The notebook currently evaluates a baseline and does not provide live packet capture, streaming inference, alerting, or a deployment API.
- For a stronger evaluation, use a capture-aware split, report balanced accuracy and per-class support, and validate on a held-out day or an external dataset.

## Responsible use

Use this project only with network data you are authorized to analyze. It is intended for defensive security research, experimentation, and education.