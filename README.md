# Predicting Flight Delays

## Description

This R project aims to predict whether a U.S. domestic flight will arrive late. The primary motivation is to provide a predictive tool for travelers, especially during peak seasons, to better manage their travel plans, such as connecting flights and ground transportation. The model is developed from the perspective of a passenger who is already on their flight and wants to know if their arrival will be delayed.

The analysis focuses on arrival delay rather than departure delay because a flight's status can change mid-air; a flight that departs on time can still arrive late, and a delayed departure does not always result in a late arrival.

## Data

The data for this project was sourced from Kaggle and includes information on domestic U.S. flights from 2019 to 2023. The original dataset contains 3 million records and 32 columns. A smaller sample of 50,000 records was used for the final modeling after data cleaning and preprocessing[cite: 90].

## Methodology

### 1. Data Cleaning and Preprocessing

* **Response Variable**: The target variable, `ARR_DELAY`, was converted into a binary factor, with `1` representing a delayed flight (arrival delay > 0) and `0` for on-time or early arrivals.
* **Feature Engineering**: The `FL_DATE` was split into `YEAR`, `MONTH`, and `DAY` to capture potential temporal effects. Time-based variables like `CRS_DEP_TIME` and `CRS_ARR_TIME` were converted into minutes past midnight for easier analysis.
* **Feature Removal**:
    * Redundant identifiers such as `AIRLINE_DOT`, `AIRLINE_CODE`, and `DOT_CODE` were removed in favor of `AIRLINE`.
    * `ORIGIN_CITY` and `DEST_CITY` were removed to avoid overlap with airport codes (`ORIGIN`, `DEST`).
    * Variables related to cancellations (`CANCELLED`, `CANCELLATION_CODE`) were dropped as they are irrelevant for predicting delays of flights already in the air.
    * Information not known mid-flight (e.g., `WHEELS_ON`, `TAXI_IN`, `ARR_TIME`) was excluded.
    * Variables detailing the cause of delay (e.g., `DELAY_DUE_CARRIER`, `DELAY_DUE_WEATHER`) were also removed.
* **Data Reduction**: To handle high cardinality, categorical variables like `AIRLINE`, `ORIGIN`, and `DEST` were filtered to keep only the most frequent levels (top 4 for airlines and top 10 for airports).

### 2. Exploratory Data Analysis (EDA)

* Visualizations such as correlation heatmaps, histograms, and bar graphs were created to understand the relationships between variables and their distributions.
* The EDA revealed right-skewness in variables like `CRS_ELAPSED_TIME`, `DEP_DELAY`, and `TAXI_OUT`, indicating a need for transformations.

### 3. Modeling

1.  **Data Splitting**: A random sample of 50,000 observations was split into an 80% training set and a 20% testing set.
2.  **Variable Transformation**:
    * A logarithmic transformation was applied to `CRS_ELAPSED_TIME` and `TAXI_OUT`.
    * A cube root transformation was applied to `DEP_DELAY` to handle negative values.
3.  **Variable Selection**: Stepwise selection using BIC was performed to create a more interpretable model. The final selected predictors were `DEP_DELAY`, `TAXI_OUT`, `ORIGIN`, `AIRLINE`, `WHEELS_OFF`, and `DEST`.
4.  **Regularization**: Ridge Regression was used on the selected variables to prevent overfitting. This served as the final model.

## Results

The final Ridge Regression model achieved an accuracy of **84%** on the test data. While the default logistic regression model had a slightly higher accuracy of 86.52%, the final model is more interpretable with 27 coefficients compared to the default's 40.

### Key Findings:

* **Airports**:
    * Departing from Denver (DEN), Orlando (MCO), and Phoenix (PHX) increases the odds of a delayed arrival compared to Atlanta (ATL).
    * Los Angeles (LAX) and Charlotte (CLT) are the airports that contribute most to on-time arrivals.
* **Airlines**:
    * Compared to American Airlines, flying with Southwest Airlines increases the odds of a late arrival.
    * United Airlines has the lowest odds of a late arrival among the top four airlines.
* **Flight Metrics**:
    * `TAXI_OUT` (time between leaving the gate and takeoff) has the largest positive coefficient, indicating a strong impact on delays. A one-unit increase in log(TAXI_OUT) multiplies the odds of a delayed arrival by 6.45.
    * A one-unit increase in the cube root of `DEP_DELAY` multiplies the odds of a delayed arrival by 1.85.

These findings align with domain knowledge that air traffic control and taxi-out times are significant contributors to flight delays.

## Technologies Used

* R
* `tidyverse`
* `caret`
* `glmnet`
* `ggcorrplot`
* `MASS`
* `car`
* `skimr`
