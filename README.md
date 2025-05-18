# Analyzing Short-Term Hospital Readmissions Using the MIMIC-III Dataset

## Introduction

In any hospital, the main goal is to take in sick patients and have them walk away happy and healthy. Doctors aim to stabilize their patient if necessary, identify the underlying cause of their symptoms, treat or help them manage their disease, and discharge them as quickly as possible. In the case of a broken leg, this can be very simple. The patient receives a surgery if needed, a cast, crutches, some pain medication, a referral for physiotherapy, and is sent on their way. Unfortunately, this is not always the case. Sometimes a patient presents with a headache, which is diagnosed as a migraine and they are sent home with a prescription, only to return hours later because the early warning signs of a stroke were missed. The same broken leg patient from earlier might return in a few days to complain of pain in their leg and discover that their surgery caused an infection. These examples highlight the importance of tracking hospital re-admission as a key indicator of patient outcomes.

When a patient returns to hospital shortly after being discharged, it indicates that the hospital may have failed to properly treat the underlying cause of the patient's disease. This could lead to a worse outcome for the patient, or, for something severe enough, their death. As a result, doctors and hospitals must study their patient's readmissions closely to understand what they might have missed, and how they could have caught it. This indicator, typically measured on a 30-day scale, is important for government compliance organizations, hospitals, and doctors to assess and improve their performance in regard to patient outcomes. 

The goal of this project is to use de-identified medical record data to analyze 30-day readmissions and identify what factors may correlate to a short term re-admission. By providing this information to medical professionals, they may be better able to understand areas where they come up short in regard to patient outcomes, and work understand how they may better serve these patients.

## Analysis Questions
Key analysis questions that we seek to answer with this data analysis project are:

1. What is the overall 30-day re-admission rate for the patient population within the MIMIC-III data set?
2. Do available demographic groups (age, gender, enthnicity) show higher rates of readmission?
3. Does insurance type effect the number of readmissions?
4. Do pre-existing co-morbities impact the likelihood of readmission?
5. Is there a relationship between length of stay and the probability of readmission?

## Data Sourcing and Justification

For this analysis, I will be using the Medical Information Mart for Intensive Care III (MIMIC-III) available via PhysioNet, a data resource provider for medical and physiological research. This dataset contains de-identified and comprehensive patient information from patients admitted to Beth Israel Deaconess Medical Center in Boston, Massacusetts between 2001 and 2012. The dataset was assembled from several resources including the critical care information systems of the hospital, electonic health record software, and the Social Security Administration Death master file. The set consists of many interconnected tables stored in csv files, and represents data for over 40,000 patients. 

This dataset was selected as it is a well known source in medical research. Medical information is protected, and subject to many compliance laws governing its storage, access, and use. Because of this, hospitals and healthcare organizations will only make data available to qualified and vetted researchers. This data set is a rare exception to this, granting students and amateur researchers an opportunity to study quality medical data without risk. 

In a more rigorous study, additional datasets from other hospitals would need to be used. Since this data is from one hospital in one city, conclusions drawn from studying this set may not be generalizable to a greater population. 

## Data Cleaning

### Cleaning

As the MIMIC-III dataset was assembled and provided for the purposes of research, it has very little data cleaning that is needed to carry out this work. There is no missing data in essential columns, except where it made logical sense (ie, no time of death for a patient that did not die). This was confirmed in Python using the `pandas` library and the `.isnull` method. Numerical columns like patient age or dates were inspected for outliers using `pandas`, `numpy`, and `scipy.stats` to perform Z-test. The only outliers found were expected, as patients with an age of 89 or over were aged up to over 300 years old to provide additional protection during the de-identification process.  

### Filtering and Feature Additions




## Visualizations and Findings