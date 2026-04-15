**Business Case Analysis**

**B1. Problem Formulation**

**(a) Machine Learning Formulation**
-  Target Variable:
items_sold (number of items sold per store per month)
Input Features (examples):
  Promotion type (Flat Discount, BOGO, etc.)
  Store size
  Location type (urban, semi-urban, rural)
  Footfall
  Competition density
  Customer demographics
  Month / seasonality
  Festival / weekend indicators
Type of ML Problem:
Supervised Regression Problem
Justification:
  The target variable (items_sold) is a continuous numerical value. The goal is to predict how many items will be sold given different conditions,
  which makes this a regression problem rather than classification.

**(b) Why Use Items Sold Instead of Revenue**
Using items sold (sales volume) is more reliable because:
  Revenue can be affected by price changes and discounts
  A promotion like “Flat Discount” may reduce revenue but increase sales volume
  Different products have different prices, which can distort comparisons

**Broader Principle:**
The target variable should align directly with the business objective and should not be distorted by external factors.
In this case, the business goal is to maximise sales activity, not necessarily revenue per transaction.

**(c) Alternative Modelling Strategy**
Instead of one global model, use:
**Segmented Models (or Hierarchical Modelling)**
  Build separate models for:
    Urban stores
    Semi-urban stores
    Rural stores

Justification:
  Customer behaviour differs significantly across locations
  Promotions may work differently depending on demographics and competition
  Improves model accuracy by capturing local patterns

**B2. Data and EDA Strategy**

**(a) Data Joining and Dataset Design**

**Tables**:
  Transactions
  Store attributes
  Promotion details
  Calendar data
**Join Strategy**:
  Join transactions with store attributes using store_id
  Join promotion details using promotion_id
  Join calendar data using transaction_date**
**Final Dataset Grain**:
   One row = one store per month
**Aggregations**:
  Total items sold per store per month
  Average basket size
  Total visits (footfall)
  Promotion used in that month
  Competition density (store-level)

**(b) Exploratory Data Analysis (EDA)****

**1. Target Distribution Plot**  
  Check distribution of items_sold
  Identify skewness or outliers
  Helps decide if transformation is needed

**2. Promotion vs Sales Analysis**
  Bar chart of average items sold by promotion type
  Identifies which promotions perform better

**3. Correlation Heatmap**
  Examine relationships between numerical variables
  Helps detect multicollinearity and useful predictors

**4. Time Series Trend**
  Plot sales over time
  Identifies seasonality and trends

**5. Store Segment Comparison**
  Compare urban vs rural store performance
  Helps justify segmented modelling

**(c) Handling Promotion Imbalance**
**Problem:**
  80% of data has no promotion
  Model may learn to ignore promotions

**Solutions**:
  Use balanced sampling or weighting
  Add a binary feature (promotion vs no promotion)
  Ensure model sees enough promotion examples

 **B3. Model Evaluation and Deployment**

**(a) Train-Test Split and Metrics**
  **Use time-based split:**
    Train: First ~2.5 years
    Test: Last ~6 months

  **Why not random split?**
    Random split mixes past and future data
    Causes data leakage
    Unrealistic evaluation

**Evaluation Metrics**:

**RMSE (Root Mean Squared Error)**:
    Penalises large errors
    Useful for understanding big mistakes
**MAE (Mean Absolute Error)**:
    Easy to interpret (average error in items sold)

**Interpretation:**
  Lower RMSE and MAE = better predictions
  Helps estimate how far predictions are from actual sales

**(b) Explaining Different Promotion Recommendations**

Use **feature importance:**

Steps:

1.Check which features influenced predictions
2.Compare:
  December (likely festival season)
  March (normal period)

**Explanation to business**:
  December may favour Loyalty Points Bonus due to repeat customers
  March may favour Flat Discount to attract new buyers

Shows that seasonality and context drive decisions

**(c) Deployment Process**

**1. Save Model**
  Use joblib or pickle to save trained pipeline

**2. Monthly Prediction Process**
  Collect new monthly data
  Apply same preprocessing pipeline
  Generate predictions for each store

**3. Recommendation System**
  For each store:
    Try all promotion options
    Select the one with highest predicted sales

**4. Monitoring**
Track:
  Prediction error over time
  Data drift (changes in input features)
  Promotion effectiveness changes

**When to retrain:**
  Performance drops significantly
  Business conditions change (e.g., new competitors)
