# Functional Data Analysis for Electricity Demand Forecasting

## Overview

This project implements a **function-on-function regression model** for forecasting daily electricity demand curves across multiple UK cities using Functional Data Analysis (FDA).

The model predicts an entire 24-hour electricity demand profile rather than individual hourly values. It combines historical demand information with weather and activity data to learn how daily demand patterns evolve over time.

The implementation is provided as an R Markdown notebook:

* `ITT24_FDA_Demand.Rmd`

---

## Methodology

The forecasting framework uses:

* **Lagged electricity demand** (7 days prior)
* **Temperature curves**
* **Wind speed curves**
* **Activity curves**

All variables are treated as functional observations over a 24-hour period, transformed from datapoints to functions through PCA fitting.

The model is fitted using the `pffr()` function from the `refund` package, which extends generalized additive models to functional responses.

Functional covariates are incorporated using `ffpc()`, which:

1. Performs Functional Principal Component Analysis (FPCA)
2. Represents each functional predictor through a small number of principal component scores
3. Reduces dimensionality
4. Improves model identifiability and computational efficiency

---

## Data Requirements

The notebook expects a CSV file named:

```text
data.csv
```

which is provided in this repository, with one row per:

```text
date × location × hour
```

and the following columns:

| Column        | Description        |
| ------------- | ------------------ |
| `date`        | Observation date   |
| `location`    | City/location name |
| `hour`        | Hour of day (0–23) |
| `demand`      | Electricity demand |
| `temperature` | Air temperature    |
| `wind_speed`  | Wind speed         |
| `activity`    | Activity indicator |


### Supported Locations

The notebook is currently configured for:

* Birmingham
* Bristol
* Cardiff
* Derby
* Swansea

Additional locations can be added by modifying the `locations` vector in the notebook.

---

## Analysis Pipeline

### 1. Data Loading

Load and clean the input dataset.

### 2. Functional Data Construction

Convert hourly observations into daily curves represented as:

```text
n_days × 24
```

matrices for each variable and location.

### 3. Lag Construction

Create a 7-day lagged demand predictor to capture weekly demand habits.

### 4. Basis Selection

Select the number of Fourier basis functions using cross-validation.

Candidate basis sizes:

```r
c(5, 7, 9, 11, 13, 15)
```

### 5. Functional Smoothing

Represent daily curves using Fourier basis functions.

### 6. Model Fitting

Fit a function-on-function regression model with:

* Lagged demand
* Temperature
* Wind speed
* Activity

as functional predictors.

### 7. Forecasting

Generate demand forecasts for the test set.

### 8. Evaluation

Calculate:

* Root Mean Squared Error (RMSE)
* Mean Absolute Error (MAE)

for each location.

### 9. Visualisation

Produce plots showing:

* Observed demand curves
* Predicted demand curves
* 95% confidence intervals

saved as PDFs into a new file called

```text
fda_plots
```

---

## Required R Packages

Install the required packages before running the notebook if you have not done so already:

```r
install.packages(c(
  "dplyr",
  "tidyr",
  "tidyverse",
  "lubridate",
  "ggplot2",
  "fda",
  "refund"
))
```

---

## Running the Notebook

1. Place `data.csv` in the project directory.
2. Open `ITT24_FDA_Demand.Rmd` in RStudio.
3. Knit the document or run the chunks sequentially.

The notebook will:

* Select an appropriate Fourier basis size
* Train the FDA model
* Generate forecasts
* Compute performance metrics

---

## Outputs

The analysis produces:

### Forecast Accuracy Metrics

* RMSE per location
* MAE per location

### Forecast Visualisations

For each test day and location:

* Observed demand profile
* Predicted demand profile
* 95% confidence interval

---

## Notes

* A chronological train/test split is used to avoid information leakage.
* Demand is lagged by 7 days to capture weekly consumption behaviour.
* Fourier bases are used because electricity demand exhibits strong daily periodicity.
* FPCA-based functional predictors substantially reduce model complexity compared with estimating full bivariate coefficient surfaces.
