# Analyzing the Severity and Causes of Major Power Outages in the U.S.

**Name**: Samsoo Seo  
**Course**: DSC 259R - Data Science Lifecycle  
**Website**: [https://github.com/SAMSOOSEO/dsc259r-final-project/](https://github.com/SAMSOOSEO/dsc259r-final-project/)

---

## Introduction
This project conducts an open-ended investigation into the **Major Power Outages** dataset, which contains records of significant power outages in the United States from 2000 to 2016. The goal is to analyze the factors that influence restoration speed and build a model to predict outage duration.

**Primary Question**: > "Do power outages caused by Severe Weather last longer on average than those caused by other reasons?"

---

## Data Cleaning and Exploratory Data Analysis
### **Data Cleaning**
* **Temporal Integration**: Combined separate date and time columns into `pd.Timestamp` objects to accurately calculate `OUTAGE.DURATION`.
* **Logical Validation**: Handled cases where restoration times were logged before start times by treating them as errors (NaN).
* **Outlier Filtering**: To improve model stability, I focused on the majority of cases by filtering out the top 10% of extreme durations (90th percentile).

### **EDA Highlights**
* **Frequency**: Severe Weather is the most frequent cause of major outages.
* **Skewness**: Outage durations are heavily right-skewed, requiring robust metrics like Median for initial analysis.

---

## Assessment of Missingness
* **NMAR Analysis**: I believe `DEMAND.LOSS.MW` is **NMAR**. Large-scale outages are often too chaotic to measure accurately in real-time, leading to systemic missingness that is inherently tied to the severity of the event.
* **Missingness Dependency**: A permutation test on `OUTAGE.DURATION` missingness against `CAUSE.CATEGORY` yielded a **p-value of 0.0000**, confirming the missingness is **MAR** (Missing at Random).
* **Imputation**: Implemented a hierarchical median imputation based on the cause category.

---

## Hypothesis Testing
* **Null Hypothesis ($H_0$)**: The distribution of outage durations for Severe Weather is the same as for other causes.
* **Alternative Hypothesis ($H_a$)**: Severe Weather outages have significantly longer average durations.
* **Result**: Observed difference of **2,344.65 min** with a **p-value of 0.0000**.
* **Conclusion**: We reject the null hypothesis. There is strong evidence that weather-related outages take significantly longer to restore.

---

## Framing a Prediction Problem
* **Task**: Predict `OUTAGE.DURATION` (Regression).
* **Evaluation Metric**: **RMSE (Root Mean Squared Error)**.
* **Features**: Includes `CAUSE.CATEGORY`, `ANOMALY.LEVEL`, `MONTH`, `HOUR`, `U.S._STATE`, and `CUSTOMERS.AFFECTED`.

---

## Baseline Model
* **Algorithm**: Linear Regression.
* **Preprocessing**: Standard Scaling for numerical features and One-Hot Encoding for categorical features.
* **Result**: Achieved an **16.84% improvement** over the naive mean prediction on the filtered dataset.

---

## Final Model
* **Algorithm**: **Random Forest Regressor** (Optimized via GridSearchCV).
* **Hyperparameters**: Tuned `max_depth` (20), `min_samples_split` (10), and `n_estimators` (300).
* **Performance**: Achieved a **20.69% improvement** over the naive mean, successfully capturing non-linear patterns between environmental features and restoration time.

---

## Fairness Analysis
* **Groups**: Urban vs. Rural (based on the median of `POPDEN_URBAN`).
* **Evaluation Metric**: RMSE.
* **Results**: 
    * **Fairness Ratio**: **0.8828**
    * **P-value**: **0.0740**
* **Conclusion**: Since the p-value is > 0.05, the difference in RMSE between Urban and Rural areas is not statistically significant. The model is considered **fair** across these demographics.
