# PCOS Prediction Using Classification Algorithms

Machine learning models for the **early detection of Polycystic Ovary Syndrome (PCOS)** from routine clinical, hormonal, and lifestyle data — comparing four classifiers to find the most reliable predictor for at-risk screening.

![Python](https://img.shields.io/badge/Python-3670A0?style=flat&logo=python&logoColor=white)
![scikit-learn](https://img.shields.io/badge/scikit--learn-F7931E?style=flat&logo=scikit-learn&logoColor=white)
![Pandas](https://img.shields.io/badge/pandas-150458?style=flat&logo=pandas&logoColor=white)
![Jupyter](https://img.shields.io/badge/Jupyter-F37626?style=flat&logo=jupyter&logoColor=white)
![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)

---

## Overview

PCOS is one of the most common endocrine disorders in women of reproductive age, affecting an estimated **8–13%** of women — yet up to **70% of cases go undiagnosed**. Left unaddressed, it raises the risk of insulin resistance, obesity, infertility, and cardiovascular complications.

This project builds and compares four supervised classification models that predict a PCOS diagnosis from a patient's demographic, clinical, hormonal, and lifestyle attributes, with the goal of supporting **earlier, data-driven screening**.

**Headline result:** a **Random Forest classifier achieved 91% accuracy and a 0.86 F1-score** on the PCOS-positive class — the strongest of the four models evaluated.

---

## Dataset

- **Source:** [PCOS Dataset — Kaggle](https://www.kaggle.com/datasets/shreyasvedpathak/pcos-dataset)
- **File:** `PCOS_data_without_infertility.xlsx` (sheet 2)
- **Size:** 541 patients × 45 columns (reduced to 42 after cleaning)
- **Target:** `PCOS (Y/N)` — binary (0 = No, 1 = Yes)
- **Class balance:** 364 negative / 177 positive (**32.7% positive**)

**Feature groups (41 predictors):**

| Category | Example Features |
|---|---|
| Demographics | Age, Weight, Height, BMI, Blood Group |
| Vitals | Pulse rate, Respiration rate, BP (Systolic/Diastolic) |
| Blood work | Hb, FSH, LH, FSH/LH, TSH, AMH, PRL, Vitamin D3, PRG, RBS, β-HCG |
| Clinical indicators | Follicle count (L/R) & size, Endometrium thickness, Cycle length/regularity |
| Lifestyle | Fast-food consumption, Regular exercise |
| Symptoms | Weight gain, Hair growth, Skin darkening, Hair loss, Pimples |

---

## Methodology

1. **Data cleaning** — trimmed column-name whitespace; dropped non-informative columns (`Sl. No`, `Patient File No.`, `Unnamed: 44`).
2. **Type correction** — coerced object-typed `AMH(ng/mL)` and `II beta-HCG(mIU/mL)` to numeric.
3. **Missing values** — imputed 4 columns (`Marriage Status`, `II beta-HCG`, `AMH`, `Fast food`) with their **median**.
4. **Exploratory analysis** — correlation heatmap, box plots (numerical), and bar plots (categorical) to identify the strongest signals.
5. **Modeling** — trained four classifiers on a **70/30 train-test split** (`random_state=0`):
   - **Linear models:** Logistic Regression, Support Vector Machine (SVM)
   - **Non-linear models:** Gaussian Naive Bayes, Random Forest
6. **Evaluation** — compared models on accuracy, precision, recall, F1-score, specificity, and Matthews Correlation Coefficient (MCC), prioritizing **recall** to minimize missed positive cases in a screening context.

---

## Key Findings from EDA

Features most correlated with a PCOS diagnosis:

| Feature | Correlation |
|---|---|
| Follicle No. (Right) | 0.65 |
| Follicle No. (Left) | 0.60 |
| Skin darkening | 0.48 |
| Hair growth | 0.46 |
| Weight gain | 0.44 |
| Cycle (Regular/Irregular) | 0.40 |
| Fast food consumption | 0.38 |

**Interpretation:** a higher follicle count and the classic hyperandrogenic symptoms (skin darkening, excess hair growth, weight gain) are the dominant predictors — consistent with clinical understanding of PCOS.

---

## Results

Evaluated on the held-out test set (163 patients). Metrics are for the **PCOS-positive class**.

| Model | Accuracy | Precision | Recall | F1-Score |
|---|---|---|---|---|
| **Random Forest** | **0.91** | **0.93** | **0.80** | **0.86** |
| Logistic Regression | 0.84 | 0.82 | 0.67 | 0.73 |
| Naive Bayes | 0.83 | 0.81 | 0.63 | 0.71 |
| SVM | 0.67 | 0.00 | 0.00 | 0.00 |

**Extended metrics:**

| Model | Specificity | False Negative Rate | MCC |
|---|---|---|---|
| Random Forest | 0.97 | 0.20 | 0.80 |
| Logistic Regression | 0.93 | 0.33 | 0.63 |
| Naive Bayes | 0.93 | 0.37 | 0.60 |
| SVM | 1.00 | 1.00 | 0.00 |

### Analysis

- **Random Forest is the clear winner** — highest accuracy (0.91), F1 (0.86), and MCC (0.80), with the lowest false-negative rate among usable models. Its ensemble of trees captures the non-linear interactions between follicle counts, hormones, and symptoms.
- **Logistic Regression and Naive Bayes** are solid, interpretable baselines (~0.83–0.84 accuracy) but miss roughly a third of positive cases (recall 0.63–0.67).
- **SVM failed to learn** — it predicted the majority class for every sample (0.67 accuracy but **0.00 recall**). This is a textbook consequence of training an unscaled SVM on features spanning wildly different ranges (e.g., hormone levels vs. binary flags). It's included here as an honest baseline and a reminder that **feature scaling is essential for margin-based models**.

---

## Tech Stack

- **Language:** Python (Jupyter Notebook)
- **Libraries:** pandas, NumPy, scikit-learn, Matplotlib, Seaborn, openpyxl

---

## Getting Started

```bash
# 1. Clone the repository
git clone https://github.com/Ashi777/PCOS-Prediction-Using-Classification-Algorithms.git
cd PCOS-Prediction-Using-Classification-Algorithms

# 2. Install dependencies
pip install pandas numpy scikit-learn matplotlib seaborn jupyter openpyxl

# 3. Launch the notebook
jupyter notebook PCOS-Prediction-Using-Classification-Algorithms.ipynb
```

> Download `PCOS_data_without_infertility.xlsx` from the [Kaggle dataset](https://www.kaggle.com/datasets/shreyasvedpathak/pcos-dataset) and update the file path in the data-loading cell.

---

## Future Work

- **Feature scaling** (StandardScaler) so SVM and Logistic Regression can compete fairly
- **Hyperparameter tuning** (GridSearchCV) and **k-fold cross-validation** for robust estimates
- **Class-imbalance handling** (SMOTE / class weights) to further improve positive-class recall
- **ROC-AUC comparison** and feature-importance ranking from the Random Forest
- Deploying the best model behind a simple prediction API

---

## Disclaimer

This project is for **educational and research purposes only** and is **not** a substitute for professional medical diagnosis.

---

## License

Released under the [MIT License](LICENSE).

## Author

**Ashi Malaiya** — [GitHub](https://github.com/Ashi777) · [LinkedIn](https://linkedin.com/in/ashi-malaiya)
