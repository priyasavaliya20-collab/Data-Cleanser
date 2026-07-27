<p align="center">
  <img src="https://capsule-render.vercel.app/api?type=waving&color=0:8B0000,100:1B5E20&height=220&section=header&text=Data%20Cleanser&fontSize=55&fontColor=ffffff&animation=fadeIn&fontAlignY=38&desc=Missing%20Value%20Imputation%20%2B%20Outlier%20Treatment%20on%20Patient%20Health%20Records&descAlignY=58&descSize=18"/>
</p>


This project presents a complete **Missing Value Imputation + Outlier Treatment** pipeline on a real-world **Patient Health Records** dataset containing 500 records. The objective is to detect and quantify data quality issues, compare multiple statistical treatment techniques against each other, and produce a fully clean, analysis-ready dataset suitable for disease-risk prediction.

The project combines classical statistical theory with hands-on implementation in Python (Jupyter Notebook), covering the complete cleaning workflow — from missing-value diagnosis and skewness-based imputation strategy to IQR/Z-score outlier detection and non-destructive capping.

---

## 🎯 Objective

To clean the `patient_health_records_dataset.csv` file by:
- Identifying and imputing missing values using the technique best suited to each column's distribution
- Detecting and treating outliers without unnecessarily discarding patient records
- Comparing every technique's trade-offs (data loss vs. statistical stability)
- Producing a final dataset with **0 missing values** and **0 flagged outliers**

---

## 🛠️ Tools & Libraries

<p align="center">
  <img src="https://img.shields.io/badge/Python-1B5E20?style=for-the-badge&logo=python&logoColor=white"/>
  <img src="https://img.shields.io/badge/Pandas-8B0000?style=for-the-badge&logo=pandas&logoColor=white"/>
  <img src="https://img.shields.io/badge/NumPy-1B5E20?style=for-the-badge&logo=numpy&logoColor=white"/>
  <img src="https://img.shields.io/badge/Scikit--Learn-8B0000?style=for-the-badge&logo=scikit-learn&logoColor=white"/>
  <img src="https://img.shields.io/badge/SciPy-1B5E20?style=for-the-badge&logo=scipy&logoColor=white"/>
  <img src="https://img.shields.io/badge/Jupyter-8B0000?style=for-the-badge&logo=jupyter&logoColor=white"/>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Status-Completed-1B5E20?style=flat-square"/>
  <img src="https://img.shields.io/badge/Records-500-8B0000?style=flat-square"/>
  <img src="https://img.shields.io/badge/Missing%20Cells%20Fixed-250%20%E2%86%92%200-1B5E20?style=flat-square"/>
  <img src="https://img.shields.io/badge/Outliers%20Treated-62%20%E2%86%92%200-8B0000?style=flat-square"/>
  <img src="https://img.shields.io/badge/License-MIT-1B5E20?style=flat-square"/>
</p>

---

## 🗂️ Project Files

| File | Description |
|---|---|
| 📓 `Data_Cleanser.ipynb` | Complete missing-value imputation & outlier treatment notebook |
| 📊 `patient_health_records_dataset.csv` | Raw patient dataset — 500 records, 9 columns |
| 📄 `Part_C_Theory_Report.pdf` | Data cleaning theory, diagrams & final report (9 pages) |
| 📘 `README.md` | Project documentation (this file) |

---

## 🏗️ Project Architecture

```
Raw Dataset (500 rows × 9 cols, 250 missing cells, 62 outliers)
     │
     ▼
┌─────────────────────────────────────────────────────────┐
│  PART A — HANDLING MISSING VALUES                         │
│  Mean / Median / Mode → Random Sample → KNN → MICE         │
└─────────────────────────────────────────────────────────┘
     │
     ▼
┌─────────────────────────────────────────────────────────┐
│  PART B — HANDLING OUTLIERS                                │
│  Z-score → IQR → Percentile Capping → Winsorization         │
└─────────────────────────────────────────────────────────┘
     │
     ▼
┌─────────────────────────────────────────────────────────┐
│  PART C — FINAL CLEAN DATASET                               │
│  0 missing · 0 outliers · 500 rows retained                 │
└─────────────────────────────────────────────────────────┘
```

---

## 📊 Dataset

| Property | Value |
|---|---|
| Source | `patient_health_records_dataset.csv` |
| Total records | 500 |
| Columns | 9 — `patient_id`, `age`, `gender`, `region`, `bmi`, `blood_pressure`, `cholesterol`, `glucose`, `disease_risk` |
| Missing cells (before cleaning) | 250 across 6 columns |
| Outliers detected (IQR) | 62 across `bmi`, `blood_pressure`, `cholesterol`, `glucose` |
| Target concept | `disease_risk` (0 / 1) |

**Missing values per column**

| Column | Missing Count | % Missing | Skewness | Strategy Chosen |
|---|---|---|---|---|
| bmi | 51 | 10.2% | 1.24 (skewed) | Median |
| age | 50 | 10.0% | 0.06 (near symmetric) | Mean |
| cholesterol | 41 | 8.2% | 2.46 (skewed) | Median |
| region | 39 | 7.8% | Categorical | Mode |
| glucose | 38 | 7.6% | 3.09 (highly skewed) | Median |
| gender | 31 | 6.2% | Categorical | Mode |
| blood_pressure | 0 | — | — | No imputation needed |

---

## 🗺️ Project Roadmap

### 🧩 Part A — Handling Missing Values

**Q1. Identify missing values & summary report**
```python
missing_report = pd.DataFrame({
    "Missing Values": df.isnull().sum(),
    "Percentage (%)": round((df.isnull().sum() / len(df)) * 100, 2)
})
```
💡 `bmi` has the highest missing rate (10.2%), followed by `age` (10.0%) and `cholesterol` (8.2%) — no column exceeds ~10%, so imputation (not deletion) is the right call.

**Q2. Apply & compare imputation techniques**

| Technique | Applied To | Result |
|---|---|---|
| Simple Imputer — Mean | `bmi` | Mean = 27.09, fills all 51 missing values |
| Simple Imputer — Median | `bmi` | Median = 26.7, safer choice given mild right skew |
| Simple Imputer — Mode | `region` | Most frequent = "West", fills all 39 missing values |
| Simple Imputer — Mode | `gender` | Most frequent = "Male", fills all 31 missing values |
| Missing Indicator + Random Sample | `cholesterol` | Adds `cholesterol_missing` flag, then random-samples from existing values |
| KNN Imputer (k=5) | `age`, `bmi`, `blood_pressure`, `cholesterol`, `glucose` | Resolves all 4 target columns together using nearest-neighbor patterns |
| MICE (Iterative Imputer) | Same 5 columns | Most statistically robust — models each column as a function of the others |

💡 KNN and MICE are the standout methods since they fix **multiple columns simultaneously** using multivariate relationships, unlike single-column Mean/Median/Mode.

---

### 📈 Part B — Handling Outliers

**Q3. Detect & remove outliers**

| Method | Applied To | Original Shape | After Treatment | Data Lost |
|---|---|---|---|---|
| Z-score (\|z\| ≤ 3) | `cholesterol`, `glucose` | (500, 9) | (392, 9) | 21.6% |
| IQR (1.5 × IQR rule) | `bmi` | (500, 9) | (439, 9) | 12.2% |
| Percentile capping (1st–99th) | `cholesterol` | (500, 9) | (500, 9) | 0% — capped, not removed |

**IQR Bounds Applied**

| Column | Lower Bound | Upper Bound | Outliers Found |
|---|---|---|---|
| bmi | 12.5 | 40.7 | 10 |
| blood_pressure | 72.5 | 170.8 | 10 |
| cholesterol | 92.1 | 309.6 | 18 |
| glucose | 25.1 | 212.8 | 24 |

**Q4. Winsorization**
```python
from scipy.stats.mstats import winsorize
winsor_df[col] = winsorize(winsor_df[col], limits=[0.01, 0.01])
```
💡 Winsorization produces nearly identical statistics to percentile capping (cholesterol mean 207.70 → 207.71) while keeping **every one of the 500 rows intact** — the safest outlier treatment when sample size matters.

**Q5. Before vs. after comparison**

💡 Removing IQR-flagged outliers shrinks the dataset by 12.2% and tightens spread across most numeric columns — confirming the core trade-off of this project: **removal boosts stability but costs data volume**, while **capping/winsorization achieves stability with zero data loss**.

---

### 🏁 Part C — Final Clean Dataset

**Q6. Present final cleaned dataset**

| Metric | Before | After |
|---|---|---|
| Total records | 500 | 500 (0 dropped) |
| Missing cells | 250 | 0 |
| Outliers (IQR-flagged) | 62 | 0 |

Final approach: **skewness-based imputation** (Mean for symmetric `age`, Median for skewed `bmi`/`cholesterol`/`glucose`, Mode for categorical `gender`/`region`) combined with **IQR-based capping (Winsorization)** for outliers.

---

## 📋 Brief Report Summary

- **Best imputation strategy:** Median for skewed numeric columns (`bmi`, `cholesterol`, `glucose`), Mean for the near-symmetric `age` column, Mode for categorical columns — validated further by KNN/MICE as the most robust multivariate options.
- **Best outlier strategy:** IQR-based capping (Winsorization) over outright removal — removal would have discarded 62 records (12.4%), many of which represent genuinely high-risk patients relevant to `disease_risk` prediction.
- **Outcome:** A complete, stable, and analysis-ready dataset of 500 records with 0 missing values and 0 extreme outliers, suitable for downstream EDA, hypothesis testing, or machine learning.

---

## 🚀 How to Run

```bash
# 1. Install dependencies
pip install pandas numpy scikit-learn scipy jupyter

# 2. Launch Jupyter
jupyter notebook

# 3. Open and run
Data_Cleanser.ipynb
```

---

## ✅ Project Checklist

- [x] Load dataset & inspect structure
- [x] Identify missing values (summary report with percentages)
- [x] Simple Imputer — Mean / Median (BMI)
- [x] Simple Imputer — Most Frequent (Region, Gender)
- [x] Missing Indicator + Random Sample Imputation (Cholesterol)
- [x] KNN Imputer (multivariate)
- [x] MICE Algorithm (multivariate)
- [x] Compare all imputation methods
- [x] Detect outliers — Z-score method
- [x] Detect outliers — IQR method
- [x] Percentile capping (1st–99th)
- [x] Winsorization
- [x] Compare dataset shape & summary before/after outlier treatment
- [x] Present final cleaned dataset
- [x] Notebook included
- [x] Dataset included

---

## 👩‍💻 Author

**Priya Savaliya**
📍 Ahmedabad, Gujarat, India

*"Data-Driven Decisions · Statistical Thinking · Evidence-Based Conclusions"*

<p align="center">⭐ If you found this project helpful, give it a star and feel free to fork!</p>

<p align="center">
  <img src="https://capsule-render.vercel.app/api?type=waving&color=0:1B5E20,100:8B0000&height=100&section=footer"/>
</p>
