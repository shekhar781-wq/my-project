Intelligent Customer Analytics in Retail E-Commerce
Churn Prediction, Segmentation, Revenue Optimization, and Customer Satisfaction Enhancement

About This Project
This repository contains the complete code, analysis, and findings for my QM640: Data Analytics Capstone project at Walsh College (Spring 2026). The project builds an end-to-end intelligent customer analytics pipeline using the UCI Online Retail Dataset, applying machine learning and statistical techniques to solve four real-world retail business problems — churn prediction, customer segmentation, revenue optimization, and customer satisfaction enhancement. All analyses follow the CRISP-DM (Cross-Industry Standard Process for Data Mining) framework, ensuring a structured, reproducible, and business-aligned workflow.

Research Questions
RQResearch QuestionMethodRQ1Which customer behaviours — recency, frequency, and total spend — best predict churn, and what is the relative contribution of each predictor?Logistic Regression, Random Forest, XGBoost + SMOTERQ2What distinct behavioural groups emerge from RFM data, and what differentiated marketing strategies are appropriate for each group?K-Means Clustering, RFM Scoring, Elbow Method, Silhouette ScoreRQ3How much do seasonal cycles, geographic differences, and product-level variation explain revenue swings, and how can these patterns guide inventory planning and pricing?ARIMA, Prophet, Multiple Regression, Market Basket (Apriori)RQ4Which behavioural factors best predict customer satisfaction proxies (repeat purchase rate and order frequency), and how can improving them enhance retention?Pearson Correlation, Multiple Regression

Dataset

Source: UCI Machine Learning Repository — Online Retail Dataset
Link: https://archive.ics.uci.edu/dataset/352/online+retail
Reference: Chen, D., Sain, S. L., & Guo, K. (2012). Data mining for the online retail industry. Journal of Database Marketing & Customer Strategy Management, 19(3), 197–208. https://doi.org/10.1057/dbm.2012.17
Size: 541,909 transaction records | 8 variables | Dec 2010 – Dec 2011
Coverage: UK-based online retailer | 38 countries | ~4,300 unique customers (after cleaning)
Key Variables: InvoiceNo, StockCode, Description, Quantity, InvoiceDate, UnitPrice, CustomerID, Country


Repository Structure
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

Data Cleaning Summary
The following preprocessing steps were applied to the raw dataset before analysis:

Missing CustomerID removal: ~135,080 records (~24.9%) dropped — customer-level RFM metrics cannot be computed without a valid identifier
Cancelled transaction removal: Invoices prefixed with 'C' and records with negative Quantity values excluded
Zero-price item exclusion: UnitPrice = 0 records removed as they represent adjustments or data entry errors
Non-product StockCode removal: Admin/fee codes (purely alphabetical, e.g. POST, D, M) filtered out; valid product codes match 5-digit numeric pattern
Duplicate removal: Exact duplicate rows across all key columns dropped
Feature engineering: TotalRevenue (Sales) = Quantity × UnitPrice computed per transaction row; date components (Year, Month, Day, Hour) extracted

After cleaning: ~400,000 records | ~4,300 unique customers

Exploratory Data Analysis (EDA) — Key Findings
Analytics AreaKey EDA FindingBusiness ImplicationCustomer SegmentationMedian purchases = 3; small high-value cohort drives majority of revenueRFM clustering will produce distinct high- and low-value segmentsChurn PredictionMean recency ≈ 92 days; many customers already inactive at dataset endSubstantial portion classified as churned; SMOTE needed for class balanceRevenue OptimizationNovember peak; Q4 ≈ 35–40% of annual revenue; UK = 90%+ of transactionsSeasonal stocking and geo-targeted promotions are high-priority actionsSatisfaction AnalysisHigh-frequency buyers show repeat purchase patterns consistent with loyaltyOrder frequency is a viable proxy for satisfaction modelling

Modelling Strategy
RQModel(s)Key FeaturesJustificationRQ1 ChurnLogistic Regression, XGBoost, Random Forest + SMOTERecency, Frequency, Monetary, Avg Order Value, Product Diversity Score, Seasonal Purchase IndexEnsemble methods outperform linear models on imbalanced churn data (Vafeiadis et al., 2015). SMOTE corrects class imbalance.RQ2 SegmentationK-Means (k=3–6), Hierarchical Clustering (validation)Z-score normalised RFM scoresK-Means is computationally efficient; Elbow Method and Silhouette Score determine optimal k.RQ3 RevenueARIMA, Prophet (time series); Multiple Regression; Apriori (market basket)Monthly revenue, temporal features, country dummies, product categoriesAdditive decomposition captures trend + seasonality. Apriori identifies high-lift product bundles.RQ4 SatisfactionPearson Correlation; Multiple RegressionAvg inter-purchase interval, product variety, order value volatility, peak-season frequencyBehavioural proxies validated by Anderson & Srinivasan (2003). Regression quantifies independent effects.

Preliminary Results
The following preliminary results have been obtained from the EDA, initial modelling runs, and feature engineering phases. Full hyperparameter tuning and final model evaluation are ongoing.
RQ1 – Churn Prediction
An initial Logistic Regression baseline trained on RFM features (without SMOTE) achieves an AUC-ROC of approximately 0.78 on the held-out validation fold. This falls below the target of 0.85, confirming that class imbalance is a significant issue. Random Forest improves on this baseline. XGBoost training with SMOTE is in progress. The confusion matrix indicates high recall for the churned class but moderate precision, suggesting the model currently over-predicts churn.
RQ2 – Customer Segmentation
K-Means clustering with k=4 yields a Silhouette Score of 0.42, which surpasses the minimum acceptable threshold of 0.40, demonstrating adequately distinct cluster separation. The resulting four segments reflect well-defined behavioural profiles: Champions (approximately 8% of customers) exhibit the strongest RFM scores across all three dimensions; Loyal customers (approximately 22%) demonstrate moderate purchasing frequency and spend; At-Risk customers (approximately 35%) show a notable decline in recency despite prior engagement; and Dormant customers (approximately 35%) record the lowest recency and frequency values, signalling disengagement. The distribution and composition of these segments are in alignment with established RFM segmentation findings reported in prior literature (Christy et al., 2021).
RQ3 – Revenue Optimization
Time-series decomposition confirms a strong seasonal component, with November 2011 representing the highest single-month revenue. Q4 accounts for approximately 35–40% of annual revenue. An initial ARIMA(1,1,1) model achieves an RMSE of approximately £45,000 on the monthly revenue test set. The trend component shows modest overall growth across the dataset year. Prophet model fitting is in progress for comparison.
RQ4 – Customer Satisfaction
Pearson correlation analysis indicates that Order Frequency is most strongly correlated with repeat purchase rate (r ≈ 0.68, p < 0.001) and Product Variety per Customer (r ≈ 0.51, p < 0.001). Average Inter-Purchase Interval shows a negative correlation with satisfaction proxies (r ≈ −0.44), confirming that customers with shorter intervals between purchases exhibit stronger loyalty signals. Multiple regression modelling confirms these predictors are jointly significant.

Performance Evaluation Summary
RQModel / MethodResultTargetStatusRQ1Logistic Regression (baseline)AUC-ROC ≈ 0.78≥ 0.85Baseline; RF/XGBoost in progressRQ1Random ForestAUC-ROC reported above≥ 0.85EvaluatedRQ2K-Means (k=4)Silhouette Score = 0.42> 0.40Target metRQ3ARIMA(1,1,1)RMSE ≈ £45,000/monthMinimisedPartial; Prophet pendingRQ4Pearson Correlation + Regressionr(freq, repeat) ≈ 0.68, p<0.001α = 0.05Significant

Technologies Used

Language: Python 3.x
Environment: Google Colab or Jupyter Notebook
Libraries:

Data Processing: pandas, numpy
Visualisation: matplotlib, seaborn
Machine Learning: scikit-learn (Logistic Regression, Random Forest, K-Means, StandardScaler)
Time Series: statsmodels (ARIMA, seasonal decompose), prophet
Statistics: scipy
Dataset Access: ucimlrepo




How to Run

Clone this repository

bash   git clone https://github.com/shekhar781-wq/my-project.git
   cd your-repo-name

Install required packages

bash   pip install pandas numpy matplotlib seaborn scikit-learn statsmodels scipy ucimlrepo prophet

Open the notebook

bash   jupyter notebook notebooks/Online_Retail_Dataset.ipynb or open in https://colab.research.google.com/

The dataset will be automatically fetched from the UCI ML Repository on first run. If unavailable, place Online Retail.xlsx in the data/raw/ folder and update the file path in the notebook.


Project Context

Course: QM640: Data Analytics Capstone
Institution: Walsh College
Term: 3
Student: Chandra Shekhar Singh
Framework: CRISP-DM (Cross-Industry Standard Process for Data Mining)# my-project
