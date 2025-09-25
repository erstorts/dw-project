# Sports Streamer Analysis

This repository contains a comprehensive analysis of user subscription behavior for Sports Streamer, a sports streaming platform. The analysis follows a systematic workflow from data cleaning through predictive modeling to identify factors that influence user subscription conversions.

## Analysis Overview

The analysis is structured as a four-stage pipeline, each implemented in a separate Jupyter notebook:

1. **Data Cleaning & Preprocessing** (`data-cleaning.ipynb`)
2. **Exploratory Data Analysis** (`analysis.ipynb`) 
3. **Statistical Modeling** (`stats-modeling.ipynb`)
4. **Predictive Modeling** (`predictive-modeling.ipynb`)

## Dataset

- **Source**: Google Sheets API integration
- **Size**: 500,000 user records
- **Target Variable**: User subscription status (binary)
- **Features**: User demographics, team associations, activity metrics, engagement data

## Workflow Description

### 1. Data Cleaning & Preprocessing (`data-cleaning.ipynb`)

**Purpose**: Transform raw data into analysis-ready format

**Key Operations**:
- **Data Acquisition**: Automated download from Google Sheets using OAuth2 authentication
- **Type Conversion**: Convert string arrays to Python lists, fix numeric types according to data dictionary
- **Missing Value Treatment**: 
  - `N_DAYS_USER_ACTIVE`: Imputed with minimum value (1) as users must have logged in at least once
  - Activity metrics: Imputed with 0 (no activity = no engagement)
  - Team association arrays: Imputed with empty lists
- **Feature Engineering**: 
  - Binary subscription status indicator
  - Outlier identification using percentile thresholds (90th for most variables, 99th for user activity)
- **Data Validation**: Correlation analysis to verify logical relationships between variables

**Output**: `dw-ds-takehome-dataset-cleaned.csv`

### 2. Exploratory Data Analysis (`analysis.ipynb`)

**Purpose**: Understand data patterns and relationships with subscription behavior

**Key Analyses**:
- **Correlation Analysis**: Identifies `N_DAYS_USER_ACTIVE` as strongest predictor of subscription
- **Categorical Analysis**: Unpacks list-type features (team roles, sports, age groups, competition levels)
- **Outlier Impact Assessment**: Compares relationships with/without outliers present
- **Feature Engineering**: Creates dummy variables for all categorical features
- **Data Reduction**: Removes outliers and creates modeling-ready dataset

**Key Findings**:
- User activity days show strongest correlation with subscription (0.4+ correlation)
- Team associations have weak but measurable impact
- Outliers don't significantly distort relationships but removed

**Output**: `stats_df.csv` (421,880 records, 40 features)

### 3. Statistical Modeling (`stats-modeling.ipynb`)

**Purpose**: Identify causal relationships and business-actionable insights using rigorous statistical methods

**Methodology**:
- **OLS Regression**: Linear regression with interpretation of coefficients as percentage point changes in subscription probability
- **Quadratic Analysis**: Tests for non-linear relationships in key variables
- **Statistical Significance**: All results filtered for p < 0.05

**Key Findings**:

*Critical Threshold Discovery*:
- **11-Day Activation Point**: Users need 11+ days of platform activity to achieve positive subscription probability
- **Engagement Drives Conversion**: All activity metrics (games scored, videos streamed) show positive linear relationships

*High-Value Segments*:
- **User Roles**: Family members and team managers have highest conversion propensity
- **Competition Levels**: School and club/travel teams outperform recreational
- **Demographics**: Over-18 age group shows stronger conversion rates

**Business Implications**:
- Focus onboarding to achieve 11+ day engagement
- Target family members and team managers in marketing
- Prioritize school and competitive travel teams

### 4. Predictive Modeling (`predictive-modeling.ipynb`)

**Purpose**: Build machine learning models for user-level subscription prediction

**Methodology**:
- **Algorithm Comparison**: Logistic Regression, KNN, Random Forest, Gradient Boosting, Neural Network
- **Class Imbalance Handling**: Balanced class weights, stratified sampling
- **Evaluation Metric**: F1-score (balances precision/recall for business use case)
- **Threshold Optimization**: Maximizes F1-score for operational deployment

**Model Performance**:
```
Algorithm              F1-Score
Gradient Boosting      0.870
Random Forest          0.859 
KNN                    0.849
Logistic Regression    0.721
```

**Deployment Results**:
- **Best Model**: Gradient Boosting (87% F1-score)
- **Prediction Coverage**: 16.7% of unsubscribed users predicted as likely converters
- **Business Application**: Enables targeted marketing campaigns with 5:1 precision advantage over random targeting

**Output**: `test-sample-predicted-subscribers.csv`

## Technical Implementation

### Tools Used
- IDE: Cursor with Pro Plan
- Google Sheets API
- Data Science Python Stack
- Internet Searches & ChatGPT (GPT-5) for helping with errors and issues

### Dependencies
```
pandas, numpy, matplotlib, seaborn
scikit-learn, statsmodels
google-auth-oauthlib, googleapiclient
```

### Environment Setup
Run the following command to install dependencies via the pipenv
```bash
pipenv install
```

### Authentication
- Google Sheets API requires a `creds.json` file. This can be obtained from the Google Cloud Platform when the Google Sheets API is enabled. [More info here.](https://developers.google.com/workspace/sheets)
- OAuth2 flow handles initial authentication

### Reproducibility
- All random operations use `random_state=42`
- Environment managed via Pipfile/Pipfile.lock

## Business Value & Next Steps

**Immediate Applications**:
1. **User Onboarding Optimization**: Focus on 11-day engagement milestone
2. **Targeted Marketing**: Prioritize high-propensity segments (family/managers, school teams, 18+)
3. **Predictive Campaigns**: Deploy ML model for precision targeting

**Recommended Experiments**:
1. **A/B Test Framework**: Validate model predictions through controlled marketing experiments
2. **Engagement Interventions**: Test strategies to increase early-user activity (streaks, notifications, network effects)
3. **Segment-Specific Features**: Develop role-based and age-appropriate product features

**Model Validation Strategy**:
Deploy predictions in production marketing campaigns, track conversion rates over predetermined timeframe (e.g., 1 week), and compare model-identified users vs. random baseline to validate real-world effectiveness.


*For questions about methodology or implementation details, refer to the detailed comments and markdown cells within each notebook.*
