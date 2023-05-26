# Regression Forecasting of Patient Admission Data (Justin Boyle, Marianne Wallis, Melanie Jessup, et al.)
- Does the problem arise from the increase of patient inflow, or from the high amount of pre-assigned inpatients bed for elective surgery? Can the beds have multiple usages other than waiting in the room for patients? What are the types of beds that we are interested in?

- How good are the bed management systems in predicting the saturation level?

- With population increase, ED presentation increases.
- The peaks of ED presentations are usually on weekends and Monday. The highest admissions are usually on Sunday, Monday and Tuesday. *I assumed that people who arrived during the weekdays would have more serious problems than the people who waits until weekends*

- Would the ED presentations look similar for the US population to that of the Australlians? Would the health care system impact the decision of people to present to ED? Does US have the same/similar health care system to Australia?

- What is the finnest resolution we can look into before the data becomes too noisy to be used for prediction? In this work, the author summarized the data into months.

- Why is there a big drop in the estimation of the monthly admission in figure 4?

# Predicting hospital admission at emergency department triage using machine learning

- This paper predicts the number of presented patients who are going to be admitted to alert the administrators and inpatient teams regarding potential admissions to optimize hospital resources. However, it takes into account only the patients who presented themselves to the emergency. Thus, it predicts the % of admissions at a time `t` given the information of the patients in the emergence room. **In other words, it predict hospital admission at the time of ED triage**.


- 