# Predicting hospital admission at emergency department triage using machine learning

## Summary
- This study trains a set of three classifiers on 3 different sets of data to classify patients at the emergency room whether they are going to be admitted or not.
- This study seems to be out of our scope as we are more interested in forecasting through time.
- However, we can utilize the methods in this study to get a rough estimate of admitted subjects at $t=t_0$ and incorporate this information in our forecasting model.
- The study included data from 560,486 patients with overall admission risk of 29.7%.
- Total number of features included in the study is 972 features, which are splitted into two categories:
    - Triage information: (Sorting process usually made by nursing staff to assess the priority of each patient to be seen by a doctor. It is based on patient's demographics, chief complaint, and vital signals).
    - Patient history
- Models utilized in this study are Logistic regression, XGBoost, and Deep Neural Network.
    - Models are implemented in R.
- Minimum performance is achieved using LR with AUC=87% when LR is trained on patient history only
- Maximum performance is achieved using XGBoost with AUC=91% when XGBoost is trained on both Triage data and patient history data.
- Maximum performance is achieved when using only 40% of the available data for trainning.
- A low dimensional XGBoost model built on **ESI (Emergence Severity Index) level, outpatient medication counts, demographics, and hospital usage statistics yielded an AUC of 0.91**

## Introduction
- This paper predicts the number of presented patients who are going to be admitted to alert the administrators and inpatient teams regarding potential admissions to optimize hospital resources. However, it takes into account only the patients who presented themselves to the emergency. Thus, it predicts the % of admissions at a time `t` given the information of the patients in the emergence room. **In other words, it predict hospital admission at the time of ED triage**.

- Studies sought to predict hospital admission at the time of ED triage included information like:
    - **Information collected at triage**: Demographics, vital signs, chief complaint, nursing notes, and early diagnostics.
    - **Hospital usage statistics and past medical history**
- A few models built on triage information have been formalized into clinical decision rules such as:
    - Sydney Triage to Admission Risk Tool and,
    - the Glasgow Admission Prediction Score.
- Progressive modeling approach that uses information available at later time points such as **lab tests ordered, medications given, and diagnoses entered by ED provider during the patient's current visit** has been able to achieve high predictive power and indicates the utility of these features.
- **Study Hypothesis**: Extracting such features from a patient's previous ED visits would lead to a robust model for predicting admission at the time of triage.

## Materials and Methods
### Data collection and preprocessing
- Data is categorized into main categories as follow:
    - **Demographics**:
        - Age
        - Gender
        - Primary Language (English vs. Non-English)
        - Ethnicity
        - Employment status
        - Insurance status
        - Marital status
        - Religion
    - **Triage**:
        - Name of presenting hospital
        - Arrival time
        - Arrival Method
        - Vital signs (Systolic and diastolic pressure, pulse, respiratory rate, oxygen saturation, presence of oxygen device, and temperature).
        - ESI level assigned by triage nurse.
    - **Chief complaint**: contains > 1000 unique values. The top 200 most frequent values (>90%) were retained as unique categories and all other values binned into 'Other'.
    - **Hospital Usage Statistic**: 
        - \# of ED visits within one year.
        - \# of admissions within one year.
        - The disposition of the patient's previous ED visit.
        - \# of procedures and surgeries listed in the patient's record
    - **Past medical history**: ICD-9 codes for past medical history (PMH) were mapped onto 281 clinically meaningful categories using the Agency for Healthcare Research and Quality (AHRQ) Clinical Classification Software (CCS), such that CCS category became a binary variable with the value 1 if patient's PMH contained one or more ICD-9 code belonging in that category and 0 otherwise.

    - **Outpatient medications**: listed in the Electronic Health Record (EHR) as active at the time of patient encounter were binned into 48 therapeutic subgroups (e.g cardiovascular, analgesics)
    - **Historical vitals**: A time-frame of 1 year from the date of patient encounter was used to calculate historical information, which included vital signs, labs and imaging previously ordered.
    - **Historical labs**: Given the diversity of labs ordered within the ED, the 150 most frequent labs comprising 94% of all orders were extracted then divided into labs with numerical values and those with categorical values. 
    - **Imaging and EKG counts**: the number of orders were counted for each of the following categories: 
        - electrocardiogram EKG
        - chest x-ray
        - other x-ray
        - echocardiogram
        - other ultrasound
        - head CT
        - other CT
        - MRI
        - Other imaging


### Model fitting and evaluation
- All analyses were done in R using caret, xgboost, and keras packages.
- A randomly chosen test set of 56,000 (10%) was held out.
- The remaining 90% split randomly 5 times to create 5 independent validation sets.
- Hyper parameters were optimized by maximizing the average validation AUC across the five validation sets.
- Optimized set of hyper parameters are then used to train the model on all 90% samples and tested on the 10% hold out set.
- Test AUC was calculated on the hold-out test set, with 95% CI, constructed using the DeLong method implemented in pROC package.
- Youden’s index was used to find the optimal cutoff point on the ROC curve to calculate the sensitivity, specificity, positive predictive value, and negative predictive value for each model.
- Categorical variables are preprocessed using One-Hot encoding.
- Missing data are imputed with the median. **Sparsity of the data prevents us from using a more sophisticated imputation algorithm such as KNN or random forest**
- **Another alternative to imputation**: transform all continuous variables into categorical variables, binning NA into a separate category, then perform one-hot encoding. However this approach loses all ordinal information and thus was not taken.
- XGBoost learns a default direction for each split in the case that the variable needed for the split is missing, thus it does not require imputation.

### Testing the benefit of additional training samples
- we trained full-variable models using each of the three algorithms on randomly selected fractions of the training set (1%, 10%, 30%, 50%, 80%, 100%), then calculated their AUCs on the held-out test set in order to quantify the incremental gain in performance.

### Variables of importance
- Information Gain (IG) is a metric that quantifies the improvement in accuracy of a tree-based algorithm from a split based on a given variable. 
- We calculated the mean IF for each variable based on 100 training iterations of the full XGBoost model.
- We then trained a low-dimensional XGBoost model using a subset of variables with high IG to test whether such a model could predict hospital admission as robustly as the full model.

## Results
- Models trained on triage information yielded a test AUC of 0.87 for LR (95% CI 0.86–0.87), 0.87 for XGBoost (95% CI 0.87–0.88) and 0.87 for DNN (95% CI 0.87–0.88)
- Models trained on patient history information yielded a test AUC of 0.86 for LR (95% CI 0.86–0.87), 0.87 for XGBoost (95% CI 0.87–0.87) and 0.87 for DNN (95% CI 0.87–0.88)
- Models trained on full set of variables yielded a test AUC of 0.91 for LR (95% CI 0.91–0.91), 0.92 for XGBoost (95% CI 0.92–0.93) and 0.87 for DNN (95% CI 0.87–0.88)