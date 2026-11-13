# Customer Eligibility System
### Customer Data Analysis, Segmentation, and Eligibility Assessment

The Customer Eligibility System explores customer data to support eligibility assessments for LangaSat’s satellite internet services.

The project combines data exploration, cleaning, feature engineering, customer segmentation, predictive modelling, and an interactive R Shiny application. It examines financial and demographic information to move beyond an assessment based only on annual salary.

## Project Overview

The project follows the CRISP-DM methodology:

1. Business understanding.
2. Data understanding.
3. Data preparation.
4. Modelling.
5. Evaluation.
6. Application delivery.

The workflow includes both unsupervised and supervised machine learning:

- **PCA and K-means clustering** to explore groups of similar customers.
- **Random Forest classification** to model defined eligibility criteria.
- **Decision-tree modelling** to help explain classification decisions.

The final application allows users to enter customer information, check eligibility, inspect the qualifying factors, and review assessment history.

## Problem Statement

Annual salary alone provides a limited view of customer suitability for satellite internet services.

The project explores additional information, including year-to-date earnings, residence duration, household size, and age, to understand customer characteristics and support more consistent eligibility assessments.

The classification target is created from defined eligibility rules. It does not represent independently observed repayment behaviour or credit risk.

## Project Objectives

- Investigate the structure and quality of the customer dataset.
- Clean inconsistent, missing, and duplicated analytical data.
- Prepare financial and demographic variables for analysis.
- Explore relationships between customer characteristics.
- Identify customer segments using K-means clustering.
- Train and evaluate a Random Forest classifier.
- Explain the factors influencing model decisions.
- Develop an interactive eligibility-checking application.

## Dataset

The source data contains payroll information alongside a combined demographic field.

Relevant attributes include:

- Year of birth.
- Marital status.
- Education.
- Occupation.
- Annual salary.
- Gross pay from the last paycheck.
- Gross year-to-date earnings.
- FRS contributions.
- Household size.
- Years of residence.

### Dataset Versions

| Dataset | Records | Purpose |
| --- | ---: | --- |
| `CustData2.csv` | 191,323 | Original source data |
| `CustData2(Data Cleaning).csv` | 191,323 | Input used for data preparation |
| `Prepared_Data.csv` | 185,056 | Prepared analytical dataset |
| `Prepared_Data_Cleaned.csv` | 185,056 | Reduced dataset referenced by the application |
| `Prepared_Data (1).csv` | 185,056 | Prepared dataset copy stored alongside the application |

The exploration and preparation reports contain separate processing runs. Their intermediate row counts should not be combined as though they represent one continuous pipeline.

## Data Exploration and Initial Cleaning

Cleaning began during the first exploration stage because the original data required restructuring before meaningful analysis could be performed.

### Restructuring the Combined Column

The source contained a column with multiple demographic attributes stored together.

The initial workflow:

1. Converted the combined field to character format.
2. Split its contents using commas.
3. Checked each record against an expected 13 fields.
4. Identified 1,228 records with inconsistent field counts.
5. Removed those records in the exploration run.
6. Assigned names to the separated fields.
7. Combined the separated fields with the payroll data.
8. Removed the original combined column.

### Numeric Formatting

Commas were removed from financial values before conversion to numeric types.

Numeric conversion was applied to:

- Annual salary.
- Gross pay from the last paycheck.
- Gross year-to-date earnings.
- FRS contributions.
- Household size.
- Years of residence.

### Data Quality Checks

The exploration included:

- Missing-value counts by column.
- Missing-data visualisation.
- Duplicate-row checks.
- Summary statistics.
- Boxplots.
- Outlier investigation.

The exploration-stage duplicate check reported zero duplicate rows. A later preparation stage identified duplicates after the analytical fields had been selected and cleaned.

### Outlier Exploration

Boxplots and an IQR-based investigation were used to examine unusual values in financial and demographic fields.

Outlier flags helped identify records requiring investigation. An unusual value was not automatically evidence of an incorrect record.

## Data Cleaning and Preparation

### Field Selection

Ten analytical fields were retained for detailed preparation. Financial column names were also standardised, including:

- `Gross_Pay_Last_Paycheck`
- `Gross_Year_To_Date`
- `Gross_FRS_Contribution`

### Categorical Cleaning

| Field | Issue | Treatment |
| --- | --- | --- |
| Marital status | Inconsistent spellings, abbreviations, and blanks | Standardised categories and replaced missing entries with `Unknown` |
| Education | Email-like text and missing entries | Removed invalid text and filled missing values with the mode, `Bach.` |
| Occupation | Education labels appearing in the occupation field | Removed misplaced labels and filled missing values with the mode, `Cleric.` |

Marital-status variations were grouped into consistent categories such as Married, Divorced, Single, and Widow.

### Missing Numerical Values

| Field | Missing Values Reported | Treatment |
| --- | ---: | --- |
| Household size | 1,228 | Median imputation using 2 |
| Annual salary | 6 | Mean imputation |
| Gross pay from the last paycheck | 6 | Mean imputation |
| Gross year-to-date earnings | 6 | Mean imputation |
| FRS contributions | 6 | Mean imputation |

A subsequent check reported no remaining missing values across the ten selected fields.

### Duplicate Removal

After field selection and cleaning, the preparation run identified 1,071 duplicate analytical rows.

Removing these duplicates left 190,252 records at that stage.

These were duplicates across the selected analytical fields, rather than confirmed duplicate customer identities.

### Outlier Filtering

The preparation code examined financial distributions before and after filtering.

The displayed filtering implementation uses the 30th and 70th percentiles and extends their difference by a factor of 1.5. This differs from the conventional 25th/75th-percentile IQR rule described in the narrative report.

Each financial filter restarts from the same dataset and overwrites the filtered object. Consequently, the displayed final object retains the last filter, based on FRS contribution, rather than all four filters cumulatively.

The prepared dataset contains 185,056 records.

## Feature Engineering

### Age

An `Age` variable was calculated by subtracting year of birth from the year at execution time.

### Age Groups

Customers were grouped into:

| Group | Age Range |
| --- | --- |
| Youth | 16–24 |
| Adult | 25–64 |
| Senior | 65 and above |

The recorded preparation output contains:

- 79,113 Adults.
- 105,943 Seniors.

### Correlation Analysis

A correlation matrix explored relationships among:

- Annual salary.
- Gross year-to-date earnings.
- FRS contributions.
- Age.
- Household size.
- Years of residence.

The documentation describes strong positive relationships among the financial variables.

These relationships helped explain the dataset but do not establish causation or prove a customer’s ability to repay.

## Customer Segmentation

### Principal Component Analysis

The segmentation workflow selected:

- Annual salary.
- Years of residence.
- Age.

Incomplete rows for those features were removed, and the features were standardised using `scale()`.

PCA was then applied, with the first two principal components retained for clustering and visualisation.

Standardisation helped prevent salary values from dominating the distance calculations simply because they use a larger numerical scale.

### K-means Clustering

K-means was used to investigate groups of customers with similar characteristics.

The process included:

1. Preparing the selected numeric features.
2. Standardising the values.
3. Applying PCA.
4. Calculating within-cluster sum of squares for cluster counts from 1 to 10.
5. Plotting an elbow curve.
6. Fitting a six-cluster model.
7. Visualising the clusters and comparing them with a salary-based affordability label.

The recorded run used:

- `set.seed(123)`
- `centers = 6`
- `nstart = 25`

### Clustering Results

| Cluster | Records |
| --- | ---: |
| 1 | 40,227 |
| 2 | 25,757 |
| 3 | 31,537 |
| 4 | 26,292 |
| 5 | 31,158 |
| 6 | 30,085 |
| **Total** | **185,056** |

The recorded between-cluster sum of squares divided by total sum of squares was 76.8%.

This measures separation in the two-component clustering space. It is not classification accuracy.

The run also recorded Quick-TRANSfer warnings, so convergence should be checked before relying on the segmentation operationally.

### Affordability Exploration

A separate affordability label was created using annual salary of at least 50,000.

This label supported visual interpretation of relationships involving:

- Annual salary.
- Gross year-to-date earnings.
- Years of residence.
- Household size.

It was not a verified customer outcome or a training target for K-means.

## Eligibility Modelling

### Target Definition

The modelling experiment created a binary eligibility target using three conditions:

- Annual salary greater than 50,000.
- Years of residence greater than 3.
- Gross year-to-date earnings greater than 45,000.

Customers meeting all three conditions were labelled eligible. Other records were labelled not eligible.

### Random Forest Classifier

The Random Forest experiment used:

| Setting | Value |
| --- | --- |
| Training split | 75% |
| Test split | 25% |
| Random seed | 123 |
| Number of trees | 10 |
| Feature importance | Enabled |

The eight predictors were:

- Gross pay from the last paycheck.
- Gross year-to-date earnings.
- FRS contributions.
- Household size.
- Years of residence.
- Annual salary.
- Marital status.
- Age.

### Decision Tree

An additional decision tree was fitted using `rpart`.

The tree provided an interpretable view of the classification rules and supported explanation of how customer characteristics relate to eligibility decisions.

### Model Interpretation

The analysis included:

- A confusion matrix.
- Precision, recall, and F1-score.
- Feature-importance rankings.
- An error-rate plot.
- A decision-tree visualisation.
- Partial dependence plots for residence duration and annual salary.

## Model Results

### Confusion Matrix

The recorded Random Forest evaluation contains 46,263 test records.

| Prediction | Actual Not Eligible | Actual Eligible |
| --- | ---: | ---: |
| Not eligible | 34,945 | 4 |
| Eligible | 0 | 11,314 |

### Reported Metrics

| Metric | Recorded Result |
| --- | ---: |
| Accuracy | Approximately 99.99% |
| Precision | 0.9998855 |
| Recall | 1.0000000 |
| F1-score | 0.9999428 |

The exported report treats class 0, not eligible, as the positive class for these precision, recall, and F1-score values.

### Important Features

The three highest-ranked features by Mean Decrease in Gini were:

1. Years of residence.
2. Annual salary.
3. Gross year-to-date earnings.

These are also the variables used to construct the eligibility target.

### Interpretation of Results

The model reproduced the project’s eligibility rules with very few test-set errors.

Because the labels were generated from the input features, the reported accuracy measures agreement with those rules. It does not establish equivalent accuracy for real-world affordability, credit risk, or repayment behaviour.

## Application Features

The **Satellite Service Eligibility Checker** was developed using R Shiny.

It allows users to enter customer details, check eligibility, review the qualifying factors, and inspect assessment history.

<!--
SCREENSHOT INSTRUCTIONS

Create an images folder in the root of your repository.

Upload your chosen screenshots using these filenames, or change each
image path below to match the filename and extension you upload.

images/application-overview.png
images/eligible-result.png
images/not-eligible-result.png
images/eligibility-factors.png
images/model-metrics.png
images/assessment-history.png

The links below display when the matching image files are uploaded.
-->

### Application Overview

The main interface includes:

- Annual salary input.
- Gross year-to-date earnings input.
- Years-of-residence input.
- Check Eligibility button.
- Reset Form button.
- Model metrics panel.
- Results tab.
- Historical Data tab.

<!-- Add your chosen screenshot of the complete application here. -->

![Customer Eligibility System Overview](images/application-overview.png)

### Customer Inputs and Eligibility Check

Users enter the customer’s annual salary, year-to-date earnings, and residence duration, then select **Check Eligibility**.

The application checks whether all required thresholds are met.

| Factor | Application Threshold |
| --- | --- |
| Annual salary | At least 50,000 |
| Gross year-to-date earnings | At least 45,000 |
| Years of residence | At least 2.5 years |

### Eligible Result

When all three conditions are met, the application displays a message confirming that the customer is eligible for the satellite service.

<!-- Add a screenshot showing an eligible assessment here. -->

![Eligible Customer Result](images/eligible-result.png)

### Not-Eligible Result

If one or more conditions are not met, the application displays a message stating that the customer is not eligible.

The factor information helps identify which requirements were not satisfied.

<!-- Add a screenshot showing a not-eligible assessment here. -->

![Not-Eligible Customer Result](images/not-eligible-result.png)

### Eligibility Factors

The results view includes a factor table showing:

- Each eligibility factor.
- Whether its threshold was met.
- The required threshold.

The application also prints a text explanation of the checks.

The panel labelled **Model Prediction** currently displays threshold-check information. It does not invoke the Random Forest model on the submitted customer inputs.

<!-- Add a screenshot of the factor table and explanation here. -->

![Eligibility Factors and Explanation](images/eligibility-factors.png)

### Model Metrics

The application displays:

- Eligibility class counts.
- A confusion matrix.
- Precision.
- Recall.
- F1-score.

These outputs describe the model trained when the application starts.

<!-- Add a screenshot of the model metrics panel here. -->

![Application Model Metrics](images/model-metrics.png)

### Historical Data

The Historical Data tab records assessments made during the current application session.

The table includes:

- Assessment timestamp.
- Annual salary.
- Gross year-to-date earnings.
- Years of residence.
- Eligibility result.

The history supports review of previous checks within the session. Persistent storage is not implemented in the displayed source.

<!-- Add a screenshot of the Historical Data tab here. -->

![Customer Assessment History](images/assessment-history.png)

### Reset Form

The **Reset Form** button clears the input fields so another customer can be assessed.

## Application Workflow

1. Open the Satellite Service Eligibility Checker.
2. Enter annual salary.
3. Enter gross year-to-date earnings.
4. Enter years of residence.
5. Select **Check Eligibility**.
6. Review the eligibility result and factor table.
7. Open **Historical Data** to inspect previous assessments.
8. Select **Reset Form** before entering another customer’s details.

## Application and Model Differences

The historical modelling experiment and the application use different configurations.

| Setting | Modelling Experiment | Application |
| --- | --- | --- |
| Salary threshold | Greater than 50,000 | At least 50,000 |
| Year-to-date earnings threshold | Greater than 45,000 | At least 45,000 |
| Residence threshold | Greater than 3 years | At least 2.5 years |
| Training/test split | 75% / 25% | 80% / 20% |
| Predictors | Eight | Three |
| Random Forest training | 10 trees | 10 trees with five-fold cross-validation through `caret` |
| Submitted customer decision | Classification experiment | Direct threshold checks |

The application trains and evaluates a Random Forest at startup, but its displayed customer eligibility decision is calculated directly from the thresholds.

The historical 99.99% accuracy should therefore not be presented as the measured result of the application’s different training configuration.

## Technologies Used

| Technology | Purpose |
| --- | --- |
| R | Data exploration, cleaning, modelling, and application logic |
| R Markdown | Analysis documentation and exported reports |
| dplyr / tidyverse | Data manipulation and preparation |
| ggplot2 | Data visualisation |
| corrplot | Correlation visualisation |
| PCA / `prcomp()` | Dimensionality reduction |
| K-means / `kmeans()` | Customer segmentation |
| factoextra | Cluster visualisation |
| randomForest | Eligibility classification |
| caret | Data partitioning, training, and evaluation |
| rpart / rpart.plot | Decision-tree modelling and visualisation |
| Shiny | Interactive application |
| DT | Interactive assessment-history table |
| bslib | Interface styling |

## Results and Project Outcomes

The project produced:

- A prepared analytical dataset containing 185,056 records.
- Documented exploration of financial and demographic variables.
- Missing-value treatment and categorical standardisation.
- Duplicate removal and outlier investigation.
- Engineered age and age-group features.
- Six exploratory customer clusters.
- A Random Forest eligibility experiment.
- Model interpretation through feature importance and visualisations.
- An interactive Shiny eligibility-checking prototype.
- Session-level assessment history.

The original project narrative discusses Shiny Server and CRM integration. The available application source demonstrates the Shiny prototype but does not include CRM connection code or production hosting configuration.

## Intended Business Value

The system is intended to support:

- Consistent application of eligibility criteria.
- Clear explanations of eligibility outcomes.
- Easier assessment through an interactive interface.
- Exploration of customer characteristics and segments.
- A foundation for future integration and monitoring.

Operational time savings, customer engagement improvements, and reductions in financial risk were not measured in the available implementation.

## Future Improvements

- Align the application and modelling eligibility definitions.
- Publish executable analysis files and dependency versions.
- Make the data-preparation workflow reproducible from the original input.
- Clarify and validate the outlier-filtering policy.
- Use a fixed reference year for age calculations.
- Verify K-means convergence and evaluate cluster quality further.
- Fit learned preprocessing on training data when evaluating future models.
- Obtain independently observed outcomes if predicting affordability or repayment is the goal.
- Connect submitted inputs to the trained model if model-based application predictions are required.
- Add persistent assessment history.
- Implement and verify any planned CRM integration.
- Evaluate privacy, fairness, and operational performance before real-world use.
