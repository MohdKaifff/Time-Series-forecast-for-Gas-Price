# J.P.Morgan Time Series Analysis Task Natural Gas Price Forecasting 

#### **Project Overview**

This project builds a time series forecasting pipeline for monthly natural gas prices. By leveraging historical price data, technical indicators (Exponential Moving Averages), and ARIMA/SARIMAX modeling, we compare baseline forecasts to enhanced models that use EMA characteristics as external regressors.The goal is to produce a 12-month outlook, assess model diagnostics, and provide utilities for querying historical and forecasted prices by date.

#### **Table of Contents**
- [Introduction](#introduction)  
- [Data Preprocessing and Feature Engineering](#data-preprocessing-and-feature-engineering)  
- [Model Building and Evaluation](#model-building-and-evaluation)  
- [Results and Analysis](#results-and-analysis)  
- [Future Work](#future-work)  
- [Dependencies](#dependencies)  
- [How to Run](#how-to-run)  
- [Acknowledgements](#acknowledgements)  


#### **Introduction**

Natural gas prices are influenced by supply-demand dynamics, seasonality, geopolitical events, and broader economic factors. Accurate forecasting can aid analysts and stakeholders in planning and risk management.

In this project, we implement and compare:

- A baseline ARIMA model (e.g., ARIMA(0,1,0) or ARIMA(1,1,1)) on 4-Years historical price data.
- A SARIMAX model incorporating Exponential Moving Averages (EMA_3, EMA_6, EMA_12) as exogenous variables.

We conduct thorough diagnostics (stationarity tests, ACF/PACF, residual checks) and generate a 12-month forecast with confidence intervals. Additionally, helper functions enable users to query prices by date.

#### **Data Preprocessing and Feature Engineering**

1. **Loading Data**  
   - Read CSV: ```pd.read_csv('data/natgas_prices.csv', parse_dates=['Date'], index_col='Date')```.  
   - Inspect for missing or duplicate dates.

2. **Handling Missing Dates**  
   - Create a complete monthly index between the earliest and latest date:  
     ```python
     first_date = Natgas.index.min()
     last_date = Natgas.index.max()
     full_index = pd.date_range(start=first_date, end=last_date, freq='M')
     Natgas = Natgas.reindex(full_index)
     Natgas['Prices'].interpolate(method='time', inplace=True) 
     ```
   - Verified no NaNs remain.

3. **Exploratory Data Analysis (EDA)**  
   - Plot the historical price series to visualize trends and volatility.  
   - Compute summary statistics (mean, std, min/max).  
   - Plot any observed seasonality or outliers.


4. **Feature Engineering: Exponential Moving Averages**  
   We have created three different Exponantial Moving average features:
      - 3 months,  "Quarterly"for Short term momentum
      - 6 months for balancing between short reactivity and long term stability  in price.
      - 12 months "Annually" for broad market seasonal price cycle.
  - Compute EMAs on the price series:
    `python
     Natgas['EMA_3'] = Natgas['Prices'].ewm(span=3, adjust=False).mean()
     Natgas['EMA_6'] = Natgas['Prices'].ewm(span=6, adjust=False).mean()
     Natgas['EMA_12'] = Natgas['Prices'].ewm(span=12, adjust=False).mean()
     `
   - Drop initial NaNs introduced by EMA calculation.


 5. **Stationarity Check**

  - Conduct Augmented Dickey-Fuller (ADF) test on the raw monthly price series to assess non-stationarity.
  - Compute the first difference of the price series (`Price_diff = Prices.diff()`) and drop NaNs.
  - Run ADF on the differenced series: record and display the ADF statistic, p-value, and critical values at 1%, 5%, and 10% levels.
  - A significantly negative ADF statistic **-6.844773557477344** and p-value **1.754169685294091e-09** on the differenced series confirms stationarity after one difference (justifying `d=1` in ARIMA/SARIMAX).

6. **ACF/PACF Analysis**  
   - Plot ACF/PACF of differenced price series to guide selection of AR and MA orders for ARIMA.

#### **Model Building and Evaluation**

1. **Baseline ARIMA**  
   - Fit ARIMA models with (1,1,1) on `Prices` only (We have tried other models aswell such as (0,1,1), (0,1,0), (2,1,2) and, (1,1,0):  
     ```python
     from statsmodels.tsa.arima.model import ARIMA
     model_base = ARIMA(Natgas['Prices'], order=(p,1,q), trend='n')  # or trend='t' for drift
     fit_base = model_base.fit()
     print(fit_base.summary())
     ```
   - Examine coefficients, AIC, BIC, sigma², residual diagnostics (Ljung-Box, Jarque-Bera, heteroskedasticity).

2. **SARIMAX with EMA Exogenous Variables**  
   - Define `y = Natgas_model['Prices']`, `exog = Natgas_model[['EMA_3','EMA_6','EMA_12']]`.  
   - Fit SARIMAX, e.g.:  
     ```python
     from statsmodels.tsa.statespace.sarimax import SARIMAX
     model_exog = SARIMAX(y, exog=exog, order=(1,1,1), enforce_stationarity=True, enforce_invertibility=True)
     fit_exog = model_exog.fit(disp=False, maxiter=500)
     print(fit_exog.summary())
     ```
   - Compare AIC/BIC against baseline. Check significance of EMA coefficients (p < 0.001 typically).
   - Note any convergence warnings; consider increasing `maxiter` or testing simpler orders.

3. **Model Comparison**  
   - Summarize:  
     - Baseline ARIMA AIC vs SARIMAX AIC.  
     - Residual variance reduction.  
     - Residual diagnostics: Ljung-Box p-values, normality tests.  
   - Conclude the preferred model is SARIMAX(1,1,1)+EMA often yields much lower AIC and significant regressors).


#### **Results and Analysis**

- **Model Performance**  
  - Baseline ARIMA often shows insignificant coefficients and higher AIC, indicating poor fit.  
  - SARIMAX with EMA features yields highly significant EMA coefficients, lower sigma², and substantially lower AIC, confirming improved fit.

- **Residual Diagnostics**  
  - Residuals from SARIMAX behave like white noise (Ljung-Box p > 0.05), approximately normal (Jarque-Bera p > 0.05), and no major heteroskedasticity.  
  - Convergence warnings noted, but results remain transparent.

- **Forecast Insights**  
  - Generated a 12-month forecast at month-end dates beyond last historical date.  
  - Forecast shows a modest upward trend (e.g., prices moving from **\$11.9** to **$12.3** over the next year) with widening confidence intervals.  
  - Interpretation: Given the absence of external shocks, EMAs should be utilised carefully when forecasting recent momentum.

- **Helper Functions**  
  - `get_price_estimate(date_str)`: Returns historical price if date is within range, else informs supported date range.  
  - `get_forecasted_price_input(forecast_df)`: Interactive prompt for users to enter a date within forecast horizon, returns shows forecasted price.


#### **Future Work**

- **Additional Exogenous Variables**  
  - Collect or get  macroeconomic indicators (inventory levels, economic activity, weather data) or correlated commodity prices to capture broader influences.
- **Seasonality Modeling**  
  - If seasonality is detected (e.g., winter demand peaks), extend SARIMAX to include seasonal terms `seasonal_order=(P,D,Q,S)`.
- **Longer or Higher-Frequency Data**  
  - Gather more historical data (longer time span) or switch to weekly/daily data to allow richer models (e.g., additional AR/MA terms, GARCH volatility modeling).
- **Alternative Models**  
  - Experiment with Prophet for automatic trend/seasonality detection.  
  -  If more data is accessible we can implement machine learning models (Random Forest, XGBoost) on lagged features and technical indicators.  
  - Explore deep learning (LSTM) if sufficient data available.  
  - Investigate regime-switching or structural-break models to handle shifts in dynamics.
- **Volatility Forecasting**  
  - Integrate GARCH or stochastic volatility models to estimate and forecast price volatility.
- **Backtesting & Validation**  
  - If data allows, hold out a validation period (e.g., last 12 months) to compute out-of-sample metrics (MAPE, RMSE).  
  - Use rolling/expanding window validation to test model stability.
- **Automation & Deployment**  
  - Automate data ingestion (fetch latest prices) and periodic model retraining/forecast updating.  
  - Deploy an API endpoint for on-demand forecast retrieval.


#### **Dependencies**

- Python 3.x  
- pandas  
- numpy  
- matplotlib  
- statsmodels  
- jupyterlab or Collab notebook  


#### **Acknowledgements**
  - Dataset: Thanks to the J.P.Morgan& Chase for providing the task and datset.
  - Libraries: Special thanks to the maintainers of the open-source libraries used in this project.



