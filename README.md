# UPES Hostel Food Waste Analysis

**Statistical Testing, Linear Regression, PCA and LDA, implemented from first principles**

Mini Project for *Fundamentals of Data Science* | B.Tech CSE, Semester 3 | School of Computer Science, UPES Dehradun

**Submitted to:** Dr. Virendra Kadyan

| Name | SAP ID |
|---|---|
| Ronak Agarwal | 590022238 |
| Ronit Batra | 590022034 |
| Ananya Mishra | 590021691 |

---

## Overview

Large hostel kitchens cook in bulk before demand is known, so the gap between meals prepared and meals eaten becomes food waste. This project analyses a hostel-wise food waste dataset for UPES to find where waste happens, what drives it, and how well it can be predicted, summarised and classified.

Three models are written **directly from their mathematical formulas in NumPy** and then verified against scikit-learn:

- **Multiple Linear Regression** using the Normal Equation, `β = (XᵀX)⁻¹Xᵀy`
- **Principal Component Analysis** using eigen-decomposition of the covariance matrix
- **Linear Discriminant Analysis** using the within/between-class scatter-matrix eigenproblem

## Repository Structure

```
FDS-PROJECT/
├── README.md
├── FDS_Food_Waste_Analysis_PCA_LDA_LR.ipynb          # Full analysis notebook
├── data/
│   └── UPES_Hostel_Wise_Food_Waste_Synthetic_Dataset.xlsx
└── report/
    └── FDS_Food_Waste_Report.pdf                      # Final project report
```

## Dataset

A **synthetic** record of hostel mess operations. Each row is one hostel-meal-day combination.

- 5 hostels (A to E, capacity 95 to 160 residents)
- 4 meals per day (Breakfast, Lunch, Snacks, Dinner)
- 46 days, 1 August to 15 September 2026
- 923 raw rows, **920 after cleaning**, 18 columns

Key columns: `Hostel`, `Meal`, `Students_Expected`, `Meals_Prepared`, `Meals_Served`, `Consumed_Meals`, `Leftover_Meals`, `Food_Waste_kg` (target), `Weekend`, `Exam_Period`, `Special_Event`, `Temperature_C`.

## Methodology

1. **Data cleaning:** date parsing, dropping blank/summary rows, Yes/No encoding, duplicate removal
2. **Feature engineering:** `Extra_Meals = Meals_Prepared - Students_Expected`
3. **Exploratory analysis:** histogram, group bar charts, hostel x meal heatmap, scatter plot, correlation heatmap, daily time series
4. **Hypothesis testing (alpha = 0.05):** Welch's t-test, one-way ANOVA, Pearson correlation
5. **Linear regression:** simple (closed form) and multiple (Normal Equation with pseudo-inverse), 80/20 split, seed 42
6. **PCA:** standardise, covariance matrix, `np.linalg.eigh`, project onto top components
7. **LDA:** waste cut at the 33rd and 67th percentiles into Low / Medium / High, nearest-centroid classification in LDA space
8. **Verification:** compare against scikit-learn's `LinearRegression`, `PCA` and `LinearDiscriminantAnalysis`

## Key Results

| Finding | Result |
|---|---|
| Average waste per record | 1.71 kg (total 1,569 kg) |
| Highest-waste hostel | Hostel_D (2.37 kg); lowest Hostel_C (1.05 kg) |
| Highest-waste meal | Dinner (1.93 kg); lowest Snacks (1.47 kg) |
| Worst hostel-meal combination | Hostel_D at Dinner (2.76 kg) |
| Leftovers vs waste correlation | r = 0.742 (p < 0.0001) |
| Special events | About 33% more waste (significant) |
| Weekends / exam periods | No significant effect |
| Multiple regression (test) | R² = 0.709, RMSE = 0.418 kg, MAE = 0.308 kg |
| PCA | 3 components keep at least 90% of the variance of 7 columns |
| LDA (test accuracy) | 69.6% (random baseline about 33%) |

### Verification against scikit-learn

| Model | Check | Result |
|---|---|---|
| Linear Regression | Max coefficient difference | 1.69e-15 |
| PCA | Max explained-variance difference | 0.00e+00 |
| LDA | Test accuracy (ours vs library) | 69.6% vs 67.4% |

The small LDA gap is expected: scikit-learn uses a Bayes rule with class priors, while this implementation uses nearest centroid.

## Recommendations

- Cook close to `Students_Expected` and track `Extra_Meals` daily as a kitchen KPI
- Prioritise portion control and waste logging in Hostel_D
- Review Dinner and Lunch quantities
- Plan special-event quantities from confirmed attendance

## Limitations

- The dataset is synthetic, so results demonstrate the method rather than real UPES behaviour
- `Leftover_Meals` is only known after the meal, so a true forecasting model should use planning-time inputs only
- Hostel and Meal were not one-hot encoded in the models
- A random split ignores time order

## How to Run

**Requirements:** Python 3.9+

```bash
pip install pandas numpy matplotlib seaborn scipy scikit-learn openpyxl jupyter
```

```bash
git clone https://github.com/ronak-star-2006/FDS-PROJECT.git
cd FDS-PROJECT
jupyter notebook FDS_Food_Waste_Analysis_PCA_LDA_LR.ipynb
```

If the notebook cannot find the dataset, update the file path in the data-loading cell to `data/UPES_Hostel_Wise_Food_Waste_Synthetic_Dataset.xlsx`.

## Tools

pandas, NumPy, Matplotlib, seaborn, SciPy, and scikit-learn (used only for verification).

## Report

The full write-up, with equations, tables and figures, is in [`report/FDS_Food_Waste_Report.pdf`](report/FDS_Food_Waste_Report.pdf).
