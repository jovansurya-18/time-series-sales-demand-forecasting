# Time Series Sales & Demand Forecasting

A time series forecasting project exploring book sales and demand patterns using SARIMA, XGBoost, LSTM, and hybrid forecasting models.

## Overview

The objective of this project was to analyse book sales data and evaluate different forecasting approaches for predicting future demand.

The analysis uses sales data for **The Alchemist** and **The Very Hungry Caterpillar**, representing two different demand profiles.

The project evaluates:

- Weekly sales forecasting
- Monthly sales forecasting
- Trend and seasonality
- Time series decomposition
- Autocorrelation
- Stationarity
- SARIMA forecasting
- XGBoost forecasting
- LSTM forecasting
- Hybrid SARIMA-LSTM forecasting
- Model performance comparison

Forecasts were generated for a **32-week weekly horizon** and an **8-month monthly horizon**.

## Dataset

The analysis uses Nielsen BookScan sales data containing book-level sales information identified by ISBN.

The datasets were combined and prepared for time series analysis by:

- Converting ISBNs and dates into appropriate formats
- Aggregating sales to a weekly frequency
- Assigning zero sales to missing weeks
- Identifying ISBNs with sales records extending beyond 1 July 2024

A total of **62 ISBNs** were retained for the initial visual analysis.

Two titles were selected for detailed forecasting:

- **The Alchemist**
- **The Very Hungry Caterpillar**

These books were selected because they represented different sales patterns.

## Methodology

The analysis followed a structured time series forecasting workflow:

- Data preparation
- Exploratory data analysis
- Sales pattern analysis
- Time series selection
- Time series decomposition
- Autocorrelation analysis
- ACF and PACF analysis
- Stationarity testing
- SARIMA modelling
- XGBoost forecasting
- LSTM forecasting
- Hybrid SARIMA-LSTM modelling
- Model performance comparison
- Weekly versus monthly forecasting

## Exploratory Analysis

Initial analysis showed substantial variation in sales behaviour across the 62 ISBNs.

Some titles demonstrated short-lived demand spikes, while others showed more stable long-term sales and recurring seasonal patterns.

Sales were generally highest earlier in a book's lifecycle before gradually declining, although some titles continued to generate long-term demand.

### Selected Titles

**The Very Hungry Caterpillar** showed:

- Higher sales volume
- Greater variability
- Stronger long-term growth
- Clear seasonal patterns

**The Alchemist** showed:

- Lower overall sales
- More consistent recurring peaks
- A relatively stable trend
- Seasonal demand patterns

## Time Series Characteristics

### Decomposition Analysis

Time series decomposition was used to separate sales into:

- Trend
- Seasonality
- Residual components

The Very Hungry Caterpillar showed a strong upward trend and clear annual seasonality.

The Alchemist showed a relatively stable trend with recurring seasonal peaks, although some residual demand shocks remained.

### Autocorrelation Analysis

ACF and PACF plots were used to investigate relationships between past and future sales.

Both books showed strong autocorrelation across multiple lags, indicating that previous sales were useful for explaining future demand.

The PACF plots also showed strong lag-1 effects and seasonal spikes, supporting the use of SARIMA models.

### Stationarity

The Augmented Dickey-Fuller test showed different statistical characteristics between the two books:

- **The Very Hungry Caterpillar:** Non-stationary, with p = 0.206
- **The Alchemist:** Stationary, with p < 0.001

The results reflected the different trend and seasonal structures within the two time series.

## Forecasting Models

Four forecasting approaches were evaluated:

- SARIMA
- XGBoost
- LSTM
- Hybrid SARIMA-LSTM

Model performance was evaluated using:

- Mean Absolute Error (MAE)
- Mean Absolute Percentage Error (MAPE)

## SARIMA Forecasting

Auto ARIMA was used to identify suitable SARIMA configurations for each book.

For the 32-week forecast horizon:

- **The Very Hungry Caterpillar:** ARIMA(1,1,1)(1,0,1)[52]
- **The Alchemist:** ARIMA(0,1,2)(1,0,1)[52]

The seasonal terms captured annual sales patterns, while differencing helped improve model fit.

SARIMA achieved MAPE values of:

| Book | MAPE |
|---|---:|
| The Alchemist | 0.2239 |
| The Very Hungry Caterpillar | 0.1841 |

The forecasts captured the overall trend and seasonal behaviour well, although sudden demand spikes were more difficult to predict.

## XGBoost Forecasting

An XGBoost forecasting pipeline was developed using lagged sales features.

GridSearchCV was used to optimise the forecasting pipeline.

The best configurations used:

- **12-week window** for The Very Hungry Caterpillar
- **6-week window** for The Alchemist

XGBoost achieved:

| Book | MAPE |
|---|---:|
| The Alchemist | 0.2698 |
| The Very Hungry Caterpillar | 0.3540 |

XGBoost underperformed the other forecasting approaches, suggesting that it struggled to capture the strong seasonal patterns present in the sales data.

## LSTM Forecasting

LSTM neural networks were developed to capture complex non-linear patterns in the sales series.

KerasTuner was used to optimise the model architecture and hyperparameters for each book.

The LSTM models produced mixed results:

| Book | MAPE |
|---|---:|
| The Alchemist | 0.2155 |
| The Very Hungry Caterpillar | 0.2265 |

The LSTM model outperformed SARIMA for The Alchemist but underperformed SARIMA for The Very Hungry Caterpillar.

## Hybrid SARIMA-LSTM Forecasting

Hybrid models were developed to combine the strengths of SARIMA and LSTM.

Two approaches were evaluated:

- Sequential hybrid model
- Parallel hybrid model

The parallel hybrid model combined SARIMA and LSTM forecasts using weighted combinations.

### Optimised Weights

The optimal weights differed between the two books:

- **The Very Hungry Caterpillar:** 90% SARIMA / 10% LSTM
- **The Alchemist:** 40% SARIMA / 60% LSTM

This demonstrated that the relative contribution of statistical and neural forecasting models depended on the characteristics of the individual time series.

## Results

The optimised parallel SARIMA-LSTM model achieved the strongest overall forecasting performance for both books.

| Book | Model | MAE | MAPE |
|---|---|---:|---:|
| The Alchemist | SARIMA | 135.6915 | 0.2239 |
| The Alchemist | LSTM | 152.6189 | 0.2155 |
| The Alchemist | SARIMA + LSTM (Parallel) | 124.0006 | 0.1972 |
| The Alchemist | SARIMA + LSTM (Sequential) | 139.5286 | 0.2355 |
| The Alchemist | XGBoost | 139.3160 | 0.2698 |
| The Alchemist | SARIMA + LSTM (Optimised) | 125.3387 | **0.1958** |
| The Very Hungry Caterpillar | SARIMA | 351.1966 | 0.1841 |
| The Very Hungry Caterpillar | LSTM | 427.3562 | 0.2265 |
| The Very Hungry Caterpillar | SARIMA + LSTM (Parallel) | 354.0863 | 0.1895 |
| The Very Hungry Caterpillar | SARIMA + LSTM (Sequential) | 354.3433 | 0.1859 |
| The Very Hungry Caterpillar | XGBoost | 839.3803 | 0.3540 |
| The Very Hungry Caterpillar | SARIMA + LSTM (Optimised) | 346.0275 | **0.1826** |

The optimised hybrid model achieved MAPE values of **0.1958 for The Alchemist** and **0.1826 for The Very Hungry Caterpillar**.

## Monthly vs Weekly Forecasting

Weekly sales were aggregated into monthly totals to evaluate whether forecasting performance changed at a lower frequency.

An **8-month forecast horizon** was generated using:

- SARIMA
- XGBoost

SARIMA consistently outperformed XGBoost for monthly forecasting.

The comparison also showed that weekly forecasting produced more accurate results than monthly forecasting for both books.

Weekly data captured more detailed sales dynamics and seasonal patterns, while monthly forecasting may be more appropriate for higher-level strategic planning.

## Key Findings

- Both books demonstrated clear seasonal demand patterns.
- The Very Hungry Caterpillar showed stronger growth and greater sales variability.
- The Alchemist displayed more stable recurring seasonal peaks.
- SARIMA performed strongly because it was able to capture trend and seasonal behaviour.
- XGBoost struggled to capture the strong seasonal patterns in the sales data.
- LSTM performance varied depending on the characteristics of the individual book.
- Hybrid SARIMA-LSTM models produced the strongest overall forecasting performance.
- Optimal SARIMA-LSTM weightings differed between the two books.
- Weekly forecasting was more accurate than monthly forecasting.
- Monthly forecasting can still provide value for higher-level strategic planning.

## Business Implications

The findings demonstrate how time series forecasting can support publishing and inventory decisions.

Forecasting models could help publishers:

- Plan inventory levels
- Support reprinting decisions
- Anticipate seasonal demand
- Reduce the risk of stock shortages
- Reduce the risk of overstocking
- Identify long-selling titles with sustained demand

Weekly forecasts can support more detailed operational planning, while monthly forecasts can be used for broader strategic decision-making.

Forecasting demand ahead of seasonal peaks could also help publishers increase stock availability and reduce the risk of missed sales opportunities.

## Technologies & Techniques

### Programming

- Python
- Pandas
- NumPy

### Time Series & Forecasting

- SARIMA
- Auto ARIMA
- XGBoost
- LSTM
- Hybrid SARIMA-LSTM

### Machine Learning

- Scikit-learn
- XGBoost
- TensorFlow / Keras
- KerasTuner
- GridSearchCV

### Statistical Analysis

- Time Series Decomposition
- ACF
- PACF
- Augmented Dickey-Fuller Test
- Trend Analysis
- Seasonality Analysis

### Evaluation

- Mean Absolute Error (MAE)
- Mean Absolute Percentage Error (MAPE)

## Project Files

**Notebook:** Contains the complete time series analysis, data preparation, exploratory analysis, forecasting models, hyperparameter tuning, model evaluation, and visualisations.

**Report:** Provides the written analysis, methodology, model comparison, forecasting results, key findings, business implications, and conclusions.

## Project Structure

```
time-series-sales-demand-forecasting/
│
├── README.md
├── time_series_sales_demand_forecasting.ipynb
└── time_series_sales_demand_forecasting_report.pdf
```

## Author

**Jovan Surya**

Data Science, Machine Learning & AI
