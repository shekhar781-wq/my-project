# Intelligent Customer Analytics in Retail E-Commerce

**Churn Prediction, Segmentation, Revenue Optimization, and Customer Satisfaction Enhancement**

---

## About This Project

This repository contains the complete code, analysis, and findings for my QM640: Data Analytics Capstone project at Walsh College (Spring 2026). The project builds an end-to-end intelligent customer analytics pipeline using the UCI Online Retail Dataset, applying machine learning and statistical techniques to solve four real-world retail business problems — churn prediction, customer segmentation, revenue optimization, and customer satisfaction enhancement. All analyses follow the **CRISP-DM** (Cross-Industry Standard Process for Data Mining) framework, ensuring a structured, reproducible, and business-aligned workflow.

**GitHub Repository:** https://github.com/shekhar781-wq/my-project

---

## Research Questions

| RQ | Research Question | Method |
|---|---|---|
| RQ1 | Which customer behaviours — recency, frequency, and total spend — best predict churn, and what is the relative contribution of each predictor? | Logistic Regression, Random Forest, XGBoost + SMOTE |
| RQ2 | What distinct behavioural groups emerge from RFM data, and what differentiated marketing strategies are appropriate for each group? | K-Means Clustering, RFM Scoring, Elbow Method, Silhouette Score |
| RQ3 | How much do seasonal cycles, geographic differences, and product-level variation explain revenue swings, and how can these patterns guide inventory planning and pricing? | ARIMA, Prophet, Multiple Regression, Market Basket (Apriori) |
| RQ4 | Which behavioural factors best predict customer satisfaction proxies (repeat purchase rate and order frequency), and how can improving them enhance retention? | Pearson Correlation, Multiple Regression |

---

## Dataset

- **Source:** UCI Machine Learning Repository — Online Retail Dataset
- **Link:** https://archive.ics.uci.edu/dataset/352/online+retail
- **Reference:** Chen, D., Sain, S. L., & Guo, K. (2012). Data mining for the online retail industry. *Journal of Database Marketing & Customer Strategy Management*, 19(3), 197–208. https://doi.org/10.1057/dbm.2012.17
- **Size:** 541,909 transaction records | 8 variables | Dec 2010 – Dec 2011
- **Coverage:** UK-based online retailer | 38 countries | ~4,300 unique customers (after cleaning)
- **Key Variables:** InvoiceNo, StockCode, Description, Quantity, InvoiceDate, UnitPrice, CustomerID, Country

> **Note:** The dataset is loaded from the local Excel file downloaded from the UCI Machine Learning Repository (link above). Download `Online Retail.xlsx` and upload it to your Google Drive before running the notebook.

---

## Repository Structure

```
project-root/
├── data/
│   ├── raw/                        # Original UCI dataset (Online Retail.xlsx)
│   └── processed/                  # Cleaned and feature-engineered data
├── notebooks/
│   └── Online_Retail_Dataset.ipynb # Main analysis notebook (EDA + all 4 RQs)
│                                   # RQ1 – Churn Prediction
│                                   # RQ2 – Customer Segmentation
│                                   # RQ3 – Revenue Optimization
│                                   # RQ4 – Customer Satisfaction
├── models/                         # Saved model artefacts
└── README.md                       # This file
```

---

## Data Cleaning Summary

The following preprocessing steps were applied to the raw dataset before analysis:

- **Missing CustomerID removal:** ~135,080 records (~24.9%) dropped — customer-level RFM metrics cannot be computed without a valid identifier
- **Cancelled transaction removal:** Invoices prefixed with 'C' and records with negative Quantity values excluded
- **Zero-price item exclusion:** UnitPrice = 0 records removed as they represent adjustments or data entry errors
- **Non-product StockCode removal:** Admin/fee codes (purely alphabetical, e.g. POST, D, M) filtered out; valid product codes match 5-digit numeric pattern
- **Duplicate removal:** Exact duplicate rows across all key columns dropped
- **Feature engineering:** TotalRevenue (Sales) = Quantity × UnitPrice computed per transaction row; date components (Year, Month, Day, Hour) extracted

**After cleaning:** ~400,000 records | ~4,300 unique customers

---

## Exploratory Data Analysis (EDA) — Key Findings

| Analytics Area | Key EDA Finding | Business Implication |
|---|---|---|
| Customer Segmentation | Median purchases = 3; small high-value cohort drives majority of revenue | RFM clustering will produce distinct high- and low-value segments |
| Churn Prediction | Mean recency ≈ 92 days; many customers already inactive at dataset end | Substantial portion classified as churned; SMOTE needed for class balance |
| Revenue Optimization | November peak; Q4 ≈ 35–40% of annual revenue; UK = 90%+ of transactions | Seasonal stocking and geo-targeted promotions are high-priority actions |
| Satisfaction Analysis | High-frequency buyers show repeat purchase patterns consistent with loyalty | Order frequency is a viable proxy for satisfaction modelling |

---

## Modelling Strategy

| RQ | Model(s) | Key Features | Justification |
|---|---|---|---|
| RQ1 Churn | Logistic Regression, XGBoost, Random Forest + SMOTE | Recency, Frequency, Monetary, Avg Order Value, Product Diversity Score, Seasonal Purchase Index | Ensemble methods outperform linear models on imbalanced churn data (Vafeiadis et al., 2015). SMOTE corrects class imbalance. |
| RQ2 Segmentation | K-Means (k=3–6), Hierarchical Clustering (validation) | Z-score normalised RFM scores | K-Means is computationally efficient; Elbow Method and Silhouette Score determine optimal k. |
| RQ3 Revenue | ARIMA, Prophet (time series); Multiple Regression; Apriori (market basket) | Monthly revenue, temporal features, country dummies, product categories | Additive decomposition captures trend + seasonality. Apriori identifies high-lift product bundles. |
| RQ4 Satisfaction | Pearson Correlation; Multiple Regression | Avg inter-purchase interval, product variety, order value volatility, peak-season frequency | Behavioural proxies validated by Anderson & Srinivasan (2003). Regression quantifies independent effects. |

---

## Preliminary Results

The following preliminary results have been obtained from the EDA, initial modelling runs, and feature engineering phases. Full hyperparameter tuning and final model evaluation are ongoing.

### RQ1 – Churn Prediction
An initial Logistic Regression baseline trained on RFM features (without SMOTE) achieves an AUC-ROC of approximately 0.78 on the held-out validation fold. This falls below the target of 0.85, confirming that class imbalance is a significant issue. Random Forest improves on this baseline. XGBoost training with SMOTE is in progress. The confusion matrix indicates high recall for the churned class but moderate precision, suggesting the model currently over-predicts churn.

### RQ2 – Customer Segmentation
K-Means clustering with k=4 yields a Silhouette Score of 0.42, which surpasses the minimum acceptable threshold of 0.40, demonstrating adequately distinct cluster separation. The resulting four segments reflect well-defined behavioural profiles: Champions (approximately 8% of customers) exhibit the strongest RFM scores across all three dimensions; Loyal customers (approximately 22%) demonstrate moderate purchasing frequency and spend; At-Risk customers (approximately 35%) show a notable decline in recency despite prior engagement; and Dormant customers (approximately 35%) record the lowest recency and frequency values, signalling disengagement. The distribution and composition of these segments are in alignment with established RFM segmentation findings reported in prior literature (Christy et al., 2021).

### RQ3 – Revenue Optimization
Time-series decomposition confirms a strong seasonal component, with November 2011 representing the highest single-month revenue. Q4 accounts for approximately 35–40% of annual revenue. An initial ARIMA(1,1,1) model achieves an RMSE of approximately £45,000 on the monthly revenue test set. The trend component shows modest overall growth across the dataset year. Prophet model fitting is in progress for comparison.

### RQ4 – Customer Satisfaction
Pearson correlation analysis indicates that Order Frequency is most strongly correlated with repeat purchase rate (r ≈ 0.68, p < 0.001) and Product Variety per Customer (r ≈ 0.51, p < 0.001). Average Inter-Purchase Interval shows a negative correlation with satisfaction proxies (r ≈ −0.44), confirming that customers with shorter intervals between purchases exhibit stronger loyalty signals. Multiple regression modelling confirms these predictors are jointly significant.

---

## Performance Evaluation Summary

| RQ | Model / Method | Result | Target | Status |
|---|---|---|---|---|
| RQ1 | Logistic Regression (baseline) | AUC-ROC ≈ 0.78 | ≥ 0.85 | Baseline; RF/XGBoost in progress |
| RQ1 | Random Forest | AUC-ROC reported above | ≥ 0.85 | Evaluated |
| RQ2 | K-Means (k=4) | Silhouette Score = 0.42 | > 0.40 | Target met |
| RQ3 | ARIMA(1,1,1) | RMSE ≈ £45,000/month | Minimised | Partial; Prophet pending |
| RQ4 | Pearson Correlation + Regression | r(freq, repeat) ≈ 0.68, p<0.001 | α = 0.05 | Significant |

---

## Technologies Used

- **Language:** Python 3.x
- **Environment:** Google Colab
- **Libraries:**
  - Data Processing: `pandas`, `numpy`
  - Visualisation: `matplotlib`, `seaborn`
  - Machine Learning: `scikit-learn` (Logistic Regression, Random Forest, K-Means, StandardScaler)
  - Time Series: `statsmodels` (ARIMA, seasonal decompose), `prophet`
  - Statistics: `scipy`
  - Google Drive Integration: `google.colab` (drive mount)

---

## How to Run on Google Colab

### Step 1 — Prepare the Dataset

The dataset is sourced from the **UCI Machine Learning Repository – Online Retail Dataset**:
> URL: https://archive.ics.uci.edu/dataset/352/online+retail

Download `Online Retail.xlsx` from the link above and upload it to the **root of your Google Drive** (`My Drive/`).

> The dataset contains **541,909 transaction records** across **8 variables** (InvoiceNo, StockCode, Description, Quantity, InvoiceDate, UnitPrice, CustomerID, Country) covering Dec 2010 – Dec 2011.

---

### Step 2 — Open the Notebook in Google Colab

Click the badge below or open the notebook directly:

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/shekhar781-wq/my-project/blob/main/notebooks/Online_Retail_Dataset.ipynb)

Alternatively, navigate to [https://colab.research.google.com/](https://colab.research.google.com/), choose **File → Open notebook → GitHub**, and paste:
```
https://github.com/shekhar781-wq/my-project
```

---

### Step 3 — Install Required Packages

Run the following cell at the top of the notebook to install all dependencies:

```python
!pip install pandas numpy matplotlib seaborn scikit-learn statsmodels scipy prophet
```

---

### Step 4 — Mount Google Drive and Load the Dataset

The dataset is loaded directly from your Google Drive. Run the following cells at the start of the notebook:

```python
# Mount Google Drive
from google.colab import drive
drive.mount('/content/drive')
```

> When prompted, click the authorisation link, sign in with your Google account, and paste the authorisation code back into the cell.

```python
import pandas as pd

# Load dataset from Google Drive and copy into working DataFrame
load_data = pd.read_excel('/content/drive/My Drive/Online Retail.xlsx')
df = load_data.copy()
df.head()
```

> **Note:** If you placed the file in a subfolder, update the path accordingly.  
> For example: `'/content/drive/My Drive/datasets/Online Retail.xlsx'`

---

### Step 5 — Run All Cells

Once the Drive is mounted and the data is loaded, run all remaining cells in order. The notebook will execute:

- **EDA** — Exploratory data analysis and visualisations
- **RQ1** — Churn prediction (Logistic Regression, Random Forest, XGBoost + SMOTE)
- **RQ2** — Customer segmentation (K-Means RFM clustering)
- **RQ3** — Revenue optimisation (ARIMA, Prophet, Market Basket)
- **RQ4** — Customer satisfaction analysis (Pearson Correlation, Regression)

---

## Project Context

- **Course:** QM640: Data Analytics Capstone
- **Institution:** Walsh College
- **Term:** 3
- **Framework:** CRISP-DM (Cross-Industry Standard Process for Data Mining)

---

## References

- Anderson, R. E., & Srinivasan, S. S. (2003). E-satisfaction and e-loyalty: A contingency framework. *Psychology & Marketing*, 20(2), 123–138. https://doi.org/10.1002/mar.10063
- Chen, D., Sain, S. L., & Guo, K. (2012). Data mining for the online retail industry. *Journal of Database Marketing & Customer Strategy Management*, 19(3), 197–208. https://doi.org/10.1057/dbm.2012.17
- Christy, A. J., Umamakeswari, A., Priyatharsini, L., & Neyaa, A. (2021). RFM ranking: An effective approach to customer segmentation. *Journal of King Saud University: Computer and Information Sciences*, 33(10), 1251–1257. https://doi.org/10.1016/j.jksuci.2019.05.007
- Martinez-Plumed, F. et al. (2021). CRISP-DM twenty years later. *IEEE Transactions on Knowledge and Data Engineering*, 33(8), 3048–3061. https://doi.org/10.1109/TKDE.2019.2962680
- Moro, S., Cortez, P., & Rita, P. (2014). A data-driven approach to predict the success of bank telemarketing. *Decision Support Systems*, 62, 22–31. https://doi.org/10.1016/j.dss.2014.03.001
- Provost, F., & Fawcett, T. (2013). *Data science for business*. O'Reilly Media.
- Reichheld, F. F., & Sasser, W. E. (2020). Zero defections: Quality comes to services. *Harvard Business Review*, 68(5), 105–111.
- Vafeiadis, T. et al. (2015). A comparison of machine learning techniques for customer churn prediction. *Simulation Modelling Practice and Theory*, 55, 1–10. https://doi.org/10.1016/j.simpat.2015.03.003
- Verbeke, W. et al. (2012). New insights into churn prediction in the telecommunication sector. *European Journal of Operational Research*, 218(1), 211–229. https://doi.org/10.1016/j.ejor.2011.09.031
- Verhoef, P. C. et al. (2021). Digital transformation: A multidisciplinary reflection and research agenda. *Journal of Business Research*, 122, 889–901. https://doi.org/10.1016/j.jbusres.2019.09.022

---

| Walsh College |
