#### **Project Overview**

This project builds a time series forecasting pipeline for monthly natural gas prices. By leveraging historical price data, technical indicators (Exponential Moving Averages), and ARIMA/SARIMAX modeling, we compare baseline forecasts to enhanced models that use EMA characteristics as external regressors.The goal is to produce a 12-month outlook, assess model diagnostics, and provide utilities for querying historical and forecasted prices by date.

#### **Table of Contents**
- [Introduction](#introduction)  
- [Dataset](#dataset)  
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



