## Project : GDP Estimation and Forecasting Analytics Financial year (Base Year 2022–23)

# Step 1: SQL Data Extraction from NAS

SELECT
    year,
    quarter,
    sector_code,
    sector_name,
    gva_current_prices,
    gdp_current_prices
FROM nas_gdp_statistics
WHERE year >= '2022-23';

# Step 2: SQL Data Extraction from GST

SELECT
    filing_period,
    state_code,
    industry_code,
    taxable_value,
    cgst_amount,
    sgst_amount,
    igst_amount
FROM gst_transactions
WHERE filing_period >= '2022-04';

# Step 3: SQL Data Extraction from ASI

SELECT
    survey_year,
    state_code,
    nic_code,
    total_output,
    intermediate_consumption,
    gross_value_added
FROM asi_industries
WHERE survey_year >= 2022;
# Step 4: SQL Data Extraction from HCES

SELECT
    survey_year,
    state_code,
    sector,
    expenditure_category,
    monthly_consumption_expenditure
FROM hces_household_expenditure;

# Step 5: SQL Data Extraction from CPI

SELECT
    reference_month,
    state_code,
    commodity_group,
    cpi_index,
    inflation_rate
FROM cpi_statistics
WHERE reference_month >= '2022-04';

# Step 6: Load Data into Python

import pandas as pd

nas = pd.read_csv("nas.csv")
gst = pd.read_csv("gst.csv")
asi = pd.read_csv("asi.csv")
hces = pd.read_csv("hces.csv")
cpi = pd.read_csv("cpi.csv")

# Step 7: Data Profiling/ EDA/ data understanding

print(nas.shape)
print(nas.info())

print(gst.shape)
print(gst.info())

print(asi.shape)
print(asi.info())

print(hces.shape)
print(hces.info())

print(cpi.shape)
print(cpi.info())

# Step 8: Missing Value Analysis/ individual ran code for datasets

print(nas.isnull().sum())
print(gst.isnull().sum())
print(asi.isnull().sum())
print(hces.isnull().sum())
print(cpi.isnull().sum())

# Step 9: Missing Value Treatment – ran code for all datasets 

gst["taxable_value"] = gst["taxable_value"].fillna(
    gst["taxable_value"].median()
)

cpi["inflation_rate"] = cpi["inflation_rate"].fillna(
    cpi["inflation_rate"].mean()
)

# Step 10: Duplicate Detection– ran code for all datasets 

gst.duplicated().sum()

# Step 11: Remove Duplicates / business requirement is to keep only one record per GSTIN  

gst = gst.drop_duplicates()
gst = gst.drop_duplicates(
    subset=["GSTIN"],
    keep="first"
)

# Step 12: Data Type Conversion- – ran code for all datasets 

gst["filing_period"] = pd.to_datetime(
    gst["filing_period"]
)

nas["year"] = nas["year"].astype(str)

# Step 13: Outlier Detection (IQR)- – ran code for all datasets 

Q1 = gst["taxable_value"].quantile(0.25)
Q3 = gst["taxable_value"].quantile(0.75)

IQR = Q3 - Q1

lower = Q1 - 1.5 * IQR
upper = Q3 + 1.5 * IQR

outliers = gst[
    (gst["taxable_value"] < lower) |
    (gst["taxable_value"] > upper)
]

# Step 14: Data Integration

gdp_df = (
    nas
    .merge(
        gst,
        on=["state_code"],
        how="left"
    )
)
Multiple datasets:
gdp_df = (
    nas
    .merge(gst, on="state_code")
    .merge(asi, on="state_code")
    .merge(hces, on="state_code")
)

# Step 15: Feature Engineering

gdp_df["gst_growth"] = (
    gdp_df["total_turnover"].pct_change()
)

gdp_df["consumption_growth"] = (
    gdp_df["total_consumption"].pct_change()
)

# Step 16: Exploratory Data Analysis

gdp_df.describe()

gdp_df.corr(numeric_only=True)

# Step 17: Prepare Data for ARIMA

ARIMA works only on the target time-series (GDP values).
gdp_series = gdp_df["gdp_current_prices"]

# Step 18: Train-Test Split for ARIMA (Chronological Split)

Since ARIMA is a Time Series model, we do not use train_test_split().
train = gdp_series[:-5]
test = gdp_series[-5:]
Explanation
•	Training Data = All historical GDP values except the last 5 periods.
•	Test Data = Last 5 periods.
•	Maintains chronological order.

# Why did you keep the last 5 periods as test data?

The last 5 periods simulate future unseen data. This allows us to evaluate how well the model predicts upcoming GDP values.

# Step 19: ARIMA Model Training

from statsmodels.tsa.arima.model import ARIMA

model = ARIMA(
    train,
    order=(2,1,2)
)

result = model.fit()
Explanation
•	p = 2 → Auto-Regressive terms
•	d = 1 → Differencing
•	q = 2 → Moving Average terms
Explanation: 

# How did you choose ARIMA(2,1,2)? What do p, d, q mean?

Meaning of p, d, q
ARIMA(p,d,q)
1. p = Auto-Regressive (AR) Term
•	Number of past observations used to predict the current value.
•	p = 2 means the model uses GDP values from the previous 2 periods.
2. d = Differencing Order
•	Used to make the time series stationary.
•	Removes trend and non-stationarity.
3. q = Moving Average (MA) Term
•	Uses past forecast errors to improve future predictions.
•	q = 2 means the model considers the previous two error terms.

# How did you choose p=2, d=1, q=2?

I first checked whether the GDP data had a trend over time. Since it was increasing over the years, I applied first-order differencing (d=1) to make the data stable. Then I tried different ARIMA parameter combinations and selected ARIMA(2,1,2). 

# Step 20: ARIMA Forecasting

forecast = result.forecast(steps=5)

Explanation: 

After training the ARIMA model, I used forecast(steps=5) to generate predictions for the next 5 time periods. The model used historical GDP patterns and trends to estimate future GDP values."

# Step 21: ARIMA Evaluation

from sklearn.metrics import (
    mean_absolute_percentage_error,
    mean_squared_error
)

arima_mape = mean_absolute_percentage_error(
    test,
    forecast
)

arima_rmse = mean_squared_error(
    test,
    forecast
) ** 0.5

print(arima_mape)
print(arima_rmse)

# Step 22: Prepare Features for Random Forest

X = gdp_df[
    [
        "total_turnover",
        "gva",
        "total_consumption",
        "cpi_index"
    ]
]

y = gdp_df["gdp_current_prices"]

# Explanation

Features:
•	GST Turnover
•	Gross Value Added (GVA)
•	Consumption
•	CPI
Target:
•	GDP

# Step 23: Train-Test Split for Random Forest

from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(
    X,
    y,
    test_size=0.2,
    random_state=42
)

# Explanation

•	80% Training Data
•	20% Testing Data

# Step 24: Train Random Forest Model

from sklearn.ensemble import RandomForestRegressor

rf = RandomForestRegressor(
    n_estimators=100,
    random_state=42
)

rf.fit(
    X_train,
    y_train
)

# Step 25: Random Forest Prediction

y_pred = rf.predict(
    X_test
)

# Step 26: Random Forest Evaluation

rf_mape = mean_absolute_percentage_error(
    y_test,
    y_pred
)

rf_rmse = mean_squared_error(
    y_test,
    y_pred
) ** 0.5

print(rf_mape)
print(rf_rmse)

# Step 27: Tableau / Power BI Dashboard

# How I Connected the Final Dataset to Tableau & Power BI

After completing data cleaning, feature engineering, and forecasting, I exported the final dataset:
gdp_df.to_csv(
    "GDP_Dashboard_Data.csv",
    index=False
)

# How I Connected the Final Dataset to Tableau & Power BI

After completing data cleaning, feature engineering, and forecasting, I exported the final dataset:
gdp_df.to_csv(
    "GDP_Dashboard_Data.csv",
    index=False
)

# Tableau Dashboard Development Steps

Step 1: Connect Data Source
•	Open Tableau Desktop
•	Click Connect → Text File
•	Select GDP_Dashboard_Data.csv

# Power BI Dashboard Development Steps

Step 1: Import Data
•	Open Power BI Desktop
•	Get Data → CSV
•	Import GDP_Dashboard_Data.csv

# You built both Tableau and Power BI dashboards on the same GDP project. Why did you create different dashboards instead of replicating the same visualizations?

# Tableau Dashboard Development 

I used Tableau for exploratory analysis, forecasting, and trend visualization. I created three dashboards: a GDP Forecasting Dashboard to analyze Year-wise and Quarter-wise GDP trends, GDP Growth %, and ARIMA forecasted GDP values; an Economic Driver Analysis Dashboard to study the impact of GST Turnover, GVA, Consumption Growth, and CPI on GDP using correlation and trend analysis; and a State-wise Economic Analysis Dashboard to compare GDP, GST Collection, Consumption Expenditure, and Inflation Rate across states. Tableau helped me create interactive visualizations, forecasting views, and geographical analysis for deeper economic insights.

# Power BI Dashboard Development 

I used Power BI for executive reporting and KPI monitoring. I developed three dashboards: an Executive Economic Performance Dashboard containing Total GDP, GDP Growth %, Total GVA, Total Consumption, Inflation Rate, and Forecast GDP; a Sector Performance Dashboard analyzing Manufacturing, Services, and Agriculture GVA along with Sector Contribution %; and a Tax & Consumption Dashboard focusing on GST Collection, GST Growth %, Rural Consumption, Urban Consumption, and Category-wise Consumption. Power BI's DAX measures and KPI-focused reporting made it ideal for management-level decision-making and performance tracking.

# I used Tableau for analytical exploration and forecasting, while Power BI was used for executive reporting, KPI monitoring, and business decision support.

# Interview Summary (Project Flow):

# SQL → Data Extraction → Python (Cleaning, Missing Values, Duplicates, Outliers, Integration) → EDA → Feature Engineering → ARIMA/Random Forest Forecasting → Model Evaluation → Tableau/Power BI Dashboard Creation.

                                           *****
