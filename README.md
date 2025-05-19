# Analyzing Short-Term Hospital Readmissions Using the MIMIC-III Dataset

## Introduction

In any hospital, the main goal is to take in sick patients and have them walk away happy and healthy. Doctors aim to stabilize their patient if necessary, identify the underlying cause of their symptoms, treat or help them manage their disease, and discharge them as quickly as possible. In the case of a broken leg, this can be very simple. The patient receives surgery if needed, a cast, crutches, some pain medication, a referral for physiotherapy, and is sent on their way. Unfortunately, this is not always the case. Sometimes a patient presents with a headache, which is diagnosed as a migraine and they are sent home with a prescription, only to return hours later because the early warning signs of a stroke were missed. The same broken leg patient from earlier might return in a few days to complain of pain in their leg and discover that their surgery caused an infection. These examples highlight the importance of tracking hospital readmission as a key indicator of patient outcomes.

When a patient returns to hospital shortly after being discharged, it indicates that the hospital may have failed to properly treat the underlying cause of the patient's disease. This could lead to a worse outcome for the patient, or, for something severe enough, their death. As a result, doctors and hospitals must study their patient's readmissions closely to understand what they might have missed, and how they could have caught it. This indicator, typically measured on a 30-day scale, is important for government compliance organizations, hospitals, and doctors to assess and improve their performance in regard to patient outcomes. 

The goal of this project is to use de-identified medical record data to analyze 30-day readmissions and identify what factors may correlate to a short term re-admission. By providing this information to medical professionals, they may be better able to understand areas where they come up short in regard to patient outcomes, and work to understand how they may better serve these patients.

## Analysis Questions
Key analysis questions that we seek to answer with this data analysis project are:

1. What is the overall 30-day re-admission rate for the patient population within the MIMIC-III data set?
2. Do available demographic groups (age, gender, enthnicity) show higher rates of readmission?
3. Does insurance type effect the number of readmissions?
4. Do pre-existing co-morbities impact the likelihood of readmission?
5. Is there a relationship between length of stay and the probability of readmission?

## Data Sourcing and Justification

For this analysis, I will be using the Medical Information Mart for Intensive Care III (MIMIC-III) available via PhysioNet, a data resource provider for medical and physiological research. This dataset contains de-identified and comprehensive patient information from patients admitted to Beth Israel Deaconess Medical Center in Boston, Massachusetts between 2001 and 2012. The dataset was assembled from several resources including the critical care information systems of the hospital, electonic health record software, and the Social Security Administration Death master file. The set consists of many interconnected tables stored in csv files, and represents data for over 40,000 patients. 

This dataset was selected as it is a well known source in medical research. Medical information is protected, and subject to many compliance laws governing its storage, access, and use. Because of this, hospitals and healthcare organizations will only make data available to qualified and vetted researchers. This data set is a rare exception to this, granting students and amateur researchers an opportunity to study quality medical data without risk. 

In a more rigorous study, additional datasets from other hospitals would need to be used. Since this data is from one hospital in one city, conclusions drawn from studying this set may not be generalizable to a greater population. 

## Data Cleaning

### Cleaning

As the MIMIC-III dataset was assembled and provided for the purposes of research, it has very little data cleaning that is needed to carry out this work. There was no missing data in essential columns, except where it made logical sense (ie, no time of death for a patient that did not die). This was confirmed in Python using the `pandas` library and the `.isnull` method. Numerical columns like patient age or dates were inspected for outliers using `pandas`, `numpy`, and `scipy.stats` to perform Z-test. The only outliers found were expected, as patients with an age of 89 or over were aged up to appear as 300 years old on their first admission to provide additional protection during the de-identification process. We planned to filter those of young age and advanced age out of the set, so we did not need to attempt to address this issue. 

### Filtering and Feature Additions

In order to assess data between similar patients, we chose to include only patients aged 18-88, thus excluding the young and old extremes who have different health needs and may have very different health needs. In order to filter by age, we used the patient's date of birth to calculate their age at admission by finding the difference between the admission time and date of birth. We then used this age at admission column to filter to our upper and lower bounds.

Because our project is interested in re-admissions,  those patients that died on their first visit to the hospital must be excluded. This was accomplished by filtering out those patients whose time of death was not empty and fell between their admission and discharge times.

A feature to track comorbidities was added, as well. Comorbidities are the simultaneous existence of multiple health conditions. The more comorbidities a patient has, the more confounding factors for a doctor to account for, and the harder it is to make an accurate diagnosis. Medical scientists have developed methods that quantify these comorbidities. This method involves using the patients diagnoses and parsing ICD-9 codes (a coding system for diseases) for identified risky comorbidities. A Charlson score, which is a sum of the total number of these comorbidities, was used. An open source package, `comorbidipy` was used to determine the Charlson score for each patient.

Length of stay was added as a feature, which was simply a difference between the discharge time and the admission time

Finally, a binary flag (0, 1) was added to represent 30 day readmission. To accomplish, the data was grouped by patient and scanned for multiple admission entries. If the patient had multiple admissions, the time between admissions was calculated using the difference between the second admission time and the first discharge time, then represented as a number of days. This column was then filtered by the desired 30 day window to identify those patients who had been readmitted within 30 days. Finally, elective admissions were considered to unflag those readmission was for a voluntary procedure.

## Visualizations and Findings

The overall readmission rate for the selected patient population was 5.78%. This means 5-6 patients out of every 100 will be readmitted to the hospital on an unplanned basis within 30 days of discharge. 

Examining by demographics, we see a few concerning numbers. First, looking by patient enthnicity, we note that Black patients experience the worst readmission rates at about double the rate of White and Asian patients. Hispanic/Latino patients experience slightly higher rates of readmission, as well. Middle Eastern (18%) and Indigenous (10%) patients also showed elevated rates of readmission, but the number of patients was low, and we chose not to include them as the small sample size does not allow us to draw conclusions in good faith. This result shows that some enthicities are not receiving the same level of care as others.

![Readmission rate by enthnicity](/figures/ethnicity.png)

Examining by gender, we see no notable gender gap in this aspect. Males and Females have nearly the same admission rate, and they are both right around the overall average.

![Readmission rate by gender](/figures/gender.png)

Examining by age, there is some variation, but for the most part each age cohort hovers around the overall readmission rate. Unexpectedly, the rate does not increase linearly with age. The age cohort of 30-39 has the highest readmission rate, which may suggest that in young people who are early to develop serious conditions, doctors are quick to write symptoms off because of the patient's age. Overall, though age does not seem to correlate much with readmission.

![Readmission rate by age](/figures/age.png)

Examining by Marital status, we note that those who are separated and single experience the highest levels of readmission, while Married men and women have the best outcomes. This could suggest the presence of a built in patient advocate in your spouse leads to better outcomes, or perhaps that people who have successful relationships are better at communicating their needs.

![Readmission rate by marital status](/figures/marital_status.png)

When inspecting by insurance type, we can see that publically funded insurance options have higher readmission rates than those with private insurance. This is concerning, and could indicate  We also note that Self pay patients are readmitted at a much lower rate than the overall readmission rate. At first glance, this may seem like a good thing, but likely tells us a disturbing story of those who can not afford care avoiding it as much as possible, even when something may be wrong. 

![Insurance type bar chart](/figures/insurance_type.png)

From inspecting comorbidities, we can conclude with certainty that more comorbidities leads to a higher readmission rate. This makes sense, as comorbidities will make it harder for a doctor to diagnose issues and more likely to get it wrong. Perhaps this indicates an opportunity for diagnoses that could leverage machine-learning in these advanced cases where it is difficult for a doctor to process the many factors of a complex patient. 

![Readmission rate by comorbidity score](/figures/comorbidities.png)

Finally, we examine by the patient's length of stay. We might expect to see here that patient's who stayed a short period of time were readmitted more often due to not receiving the attention and care they needed. However, we do not observe this trend and conclude that length of stay does not correlate with readmission. 

![Readmission rate by length of stay](/figures/length_of_stay.png)