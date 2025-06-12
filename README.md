# Time-Series-forecast-for-Gas-Price
### Project Overview

This repository contains a time series forecasting pipeline for natural gas prices using ARIMA/SARIMAX models with technical indicators (EMA) as exogenous regressors. The goal is to compare baseline ARIMA forecasts against enhanced SARIMAX forecasts incorporating EMA features, perform diagnostics, and produce a 12-month outlook. Helper functions allow querying historical and forecasted prices by date.

### Repository Structure

├── data/
│   └── natgas_prices.csv          # Historical price data (date, price)
├── notebooks/
│   └── JP_Morgan_Quantitative_training_task1.ipynb  # Main analysis notebook
├── src/
│   ├── data_preprocessing.py     
│   ├── feature_engineering.py     
│   ├── modeling.py                
│   ├── forecast_utils.py          
│   └── visualization.py          
├── figures/
│   ├── historical_prices.png
│   ├── ema_overlay.png
│   ├── acf_pacf.png
│   ├── residuals_diagnostics.png
│   ├── forecast_plot.png
│   ├── comparison_forecast.png
│   └── coefficients_bar.png
├── requirements.txt               
├── README.md                      
└── LICENSE                        
