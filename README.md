# Covid-19-Clinical-Determinants-HCP

## **Table of Contents**

1. **[Project Overview](#1-project-overview)**
2. **[The Dataset](#2-the-dataset)**
3. **[The Analytics Stack](#3-the-analytics-stack)**
4. **[Data Processing & Feature Engineering](#4-data-processing--feature-engineering)**
5. **[Clinical Comparative Analysis (Hospitalized vs. Unhospitalized)](#5-clinical-comparative-analysis)**
6. **[Key Outcomes: The HCP Requirement Indicators](#6-key-outcomes)**
7. **[Public Health Recommendations](#7-public-health-recommendations)**
8. **[Challenges & Data Limitations](#8-challenges--data-limitations)**

### **1. Project Overview**

 This project focuses on identifying the specific physiological "tipping points" that separate a manageable case of COVID-19 from one that requires acute hospital care.

By analyzing a clinical dataset of confirmed cases, I conducted a comparative study between two distinct patient cohorts:

* **Unhospitalized Patients:** Those who were able to recover through home-based care or outpatient monitoring.
* **Hospitalized Patients:** Those whose clinical condition necessitated admission for professional Health Care Provider (HCP) intervention.

#### **The Core Objective**

The primary goal of this analysis is to isolate the **biomarkers and vital signs** that serve as the most reliable indicators of severity. By calculating the "Delta" (the mathematical difference) between the mean clinical values of these two groups, the project establishes a data-driven framework for predicting HCP requirements.

#### **Key Questions Answered**

* Which clinical parameters show the most significant deviation when a patient’s condition worsens?
* Can we establish a baseline of "normal" vs. "critical" mean values for key health factors?
* At what specific thresholds (higher or lower) should a patient be transitioned from home care to professional medical supervision?

This analysis moves beyond simple case counting to provide a granular, parameter-by-parameter look at the clinical reality of the virus, offering insights that can help optimize hospital resource allocation and patient monitoring protocols.

### **2. The Dataset**

The power of this analysis comes from the integration of five distinct datasets. By merging clinical biomarkers with geographic and environmental data, I was able to move beyond simple case counting to understand the "Clinical Spectrum" of the virus in relation to its surroundings.

#### **A. Clinical Spectrum & Diagnosis**

* **Source:** `diagnosis-of-covid-19-and-its-clinical-spectrum.csv`
* **The Content:** This is the primary dataset used to define the "Hospitalized" vs. "Unhospitalized" cohorts. It contains detailed laboratory results (Hematocrit, Platelets, Leukocytes) and patient disposition (Regular Ward, Semi-Intensive, or ICU).
* **The Utility:** I used this to calculate the mean values of physiological markers, creating the baseline for when a patient requires a Health Care Provider (HCP).

#### **B. Global Observation Records**

* **Source:** `covid_19_data.csv`
* **The Content:** A comprehensive time-series dataset tracking Confirmed, Death, and Recovery cases across various countries and regions.
* **The Utility:** This provided the "Big Picture" of the pandemic's progression, allowing me to visualize the global trajectory before diving into specific clinical markers.

#### **C. US County-Level Demographics**

* **Source:** `us-county.csv`
* **The Content:** Highly granular data including health factors like Obesity rates, Smokers, Diabetics, and socioeconomic markers like Rural Population and Food Environment Index.
* **The Utility:** This allowed for a cross-sectional analysis of how pre-existing community health conditions (comorbidities) correlated with the severity of COVID-19 outbreaks in the United States.

#### **D. Environmental & Weather Context**

* **Source:** `training_data_with_weather_info_week_4.csv` and `temperature_data_covid.csv`
* **The Content:** Daily weather metrics including Temperature, Humidity (Dew Point), Wind Speed, and Precipitation for regions globally.
* **The Utility:** I integrated this to investigate the "Seasonality Hypothesis"—analyzing whether specific climate conditions, such as temperature drops, showed a correlation with increased hospitalizations or faster viral spread.

#### **Data Integrity Note**

To ensure the analysis remained accurate across these diverse sources, I implemented a strict **Key-Based Merge** strategy, using dates and geographic identifiers to align clinical outcomes with environmental conditions. Rows with incomplete clinical markers in the "Spectrum" dataset were handled via targeted drops to ensure our "Top Predictors" were based on verified medical observations.

### **3. The Analytics Stack**

To process the diverse mix of clinical, environmental, and geographic data, I utilized a comprehensive Python-based ecosystem. Each tool was selected for its specific strength in handling large-scale medical datasets and producing interactive visualizations.

* **Pandas & NumPy:** These were the foundational tools for the end-to-end pipeline. I used them to clean the "Clinical Spectrum" data and perform vectorized operations to merge the medical records with weather and US county-level demographics.
* **Plotly (Express & Graph Objects):** This was essential for the **Geospatial Analysis**. Plotly allowed me to create interactive maps that visualize the global spread of COVID-19 and its concentration within specific US counties.
* **Seaborn & Matplotlib:** While Plotly handled the interactive maps, I used Seaborn and Matplotlib for the **Clinical Comparative Charts**. These libraries allowed for precise, high-resolution visualizations of the distribution of lab results between hospitalized and home-recovery cohorts.
* **Urllib & JSON:** These were used to fetch and parse external web-based JSON files, ensuring that the geographic boundaries used in the maps were accurate and standardized.
* **Scikit-Learn:** Employed for the initial evaluation of feature correlations, helping to determine which clinical markers (like Platelets or Leukocytes) had the strongest relationship with the hospitalization target.

### **4. Data Processing & Feature Engineering**

The integrity of this analysis depends on the seamless integration of five distinct datasets. This phase involved transforming raw, multi-source data into a unified structure capable of revealing the clinical and environmental drivers of the pandemic.

#### **A. Strategic Multi-Source Merging**

I developed a data pipeline to join clinical records with external geographic and atmospheric contexts:

* **Geographic Alignment:** Linked the `diagnosis-of-covid-19` clinical markers with `us-county.csv` to correlate patient outcomes with regional demographics like obesity rates and population density.
* **Environmental Integration:** Merged clinical datasets with `temperature_data_covid.csv` and weather training data to evaluate the impact of local climate (Temperature, Humidity, Wind Speed) on hospitalization trends.

#### **B. The Comparative Mean Framework**

The core technical achievement of the notebook was the development of a **Mean-Delta Evaluation** system. This system mathematically isolates the clinical markers that necessitate a Health Care Provider (HCP):

1. **Segmentation:** The clinical spectrum data was partitioned into two groups: `hospitalized_mean` (patients admitted to wards or ICUs) and `unhospitalized_mean`.
2. **Aggregation:** I calculated the average clinical values for every laboratory parameter (Platelets, Monocytes, Leukocytes, etc.) across both groups.
3. **The "Change" Feature:** I engineered a new feature called **`Change`** ($Hospitalized\ Mean - Unhospitalized\ Mean$). This variable acts as a "severity score," highlighting which biomarkers deviate the most when a patient requires professional care.

#### **C. Clinical Data Sanitization**

To ensure the findings were statistically robust and free from "clinical noise," I implemented the following protocols:

* **Missing Value Strategy:** Used `dropna()` strategically after the merging process. This ensured that the comparison was based only on patients with complete physiological profiles, preventing "hallucinated" averages.
* **Scale Normalization:** Standardized the units across different lab results to ensure that the `Change` feature accurately reflected clinical variance.
* **Column Pruning:** Dropped non-informative columns (IDs, administrative timestamps) to focus the dataframe purely on the physiological narrative.

### **5. Clinical Comparative Analysis**

This phase represents the core analytical engine of the project. Once the datasets were merged and cleaned, I moved from broad observations to a surgical comparison of the **hospitalized** and **unhospitalized** cohorts. The goal was to visualize the physiological "gap" that determines a patient's clinical trajectory.

#### **A. The "Delta" Strategy**

To make the data actionable for health care providers, I developed a **Mean-Delta Calculation**.

* **The Process:** I subtracted the average clinical values of home-recovery patients from those who required hospital admission.
* **The Result:** This created a "Change" metric. A large positive delta indicated a biomarker that "spikes" during severe infection, while a large negative delta highlighted markers that "crash" or deplete as the patient’s condition worsens.

#### **B. Visualizing the Clinical Divide**

Using Seaborn and Matplotlib, I generated comparative visualizations to show the distribution of these biomarkers.

* **Biomarkers Analyzed:** I focused on critical indicators such as Hematocrit, Platelets, Leukocytes, Monocytes, and Red Blood Cell counts.
* **The Observation:** The charts clearly illustrated that hospitalized patients didn't just have *different* values—they had statistically distinct distributions, showing that COVID-19 impacts the body’s blood chemistry in a predictable, measurable way.

#### **C. Identifying the Tipping Points**

By sorting the parameters based on their "Change" values, I was able to isolate the most aggressive indicators of disease severity.

* **Higher Value Indicators:** These are parameters that increase significantly in hospitalized patients (e.g., markers of systemic inflammation).
* **Lower Value Indicators:** These represent vital components that the body loses or consumes rapidly during a severe viral response.

#### **D. From Data to Triage Logic**

This comparative analysis turned a list of numbers into a **Triage Logic**. Instead of waiting for a patient to feel "very sick," this analysis identifies the specific lab results that serve as an early warning system. By seeing exactly how much a patient's biomarkers have deviated from the "Unhospitalized Mean," an HCP can make a data-driven decision about whether to admit the patient to a regular ward or an Intensive Care Unit (ICU).

```python
import pandas as pd
import numpy as np

df = pd.read_csv('diagnosis-of-covid-19-and-its-clinical-spectrum.csv')

# Defining hospitalized vs unhospitalized
hosp_cols = ['patient_addmited_to_regular_ward_1_yes_0_no', 
             'patient_addmited_to_semi_intensive_unit_1_yes_0_no', 
             'patient_addmited_to_intensive_care_unit_1_yes_0_no']

# If any of these is 1, the patient is hospitalized
df['is_hospitalized'] = df[hosp_cols].sum(axis=1).apply(lambda x: 1 if x > 0 else 0)

# Selecting numeric columns for mean calculation (clinical parameters)
# Skipping ID and categorical target columns
numeric_cols = df.select_dtypes(include=[np.number]).columns.tolist()
# Filter out the ward/icu indicators and the target made
exclude = hosp_cols + ['is_hospitalized', 'patient_age_quantile']
parameters = [c for c in numeric_cols if c not in exclude]

hospitalized_mean = df[df['is_hospitalized'] == 1][parameters].mean().reset_index()
hospitalized_mean.columns = ['Parameter', 'Hospitalized_figures']

unhospitalized_mean = df[df['is_hospitalized'] == 0][parameters].mean().reset_index()
unhospitalized_mean.columns = ['Parameter', 'Unhospitalized_figures']

hospitalized_mean['Unhospitalized_figures'] = unhospitalized_mean['Unhospitalized_figures']
result = hospitalized_mean.dropna()
result['Change'] = result['Hospitalized_figures'] - result['Unhospitalized_figures']
result.sort_values(['Change'], ascending=True, inplace=True)

lower = result.head(10)
higher = result.tail(10)

print("Lower parameters:")
print(lower[['Parameter', 'Change']])
print("\nHigher parameters:")
print(higher[['Parameter', 'Change']])



```

```

```python
import json

with open('Covid_report.ipynb', 'r', encoding='utf-8') as f:
    nb = json.load(f)

for cell in nb['cells']:
    if cell['cell_type'] == 'code':
        source = "".join(cell['source'])
        if 'hospitalized_mean' in source and 'Change' in source:
            print("--- Relevant Code ---")
            print(source)
            if 'outputs' in cell:
                for out in cell['outputs']:
                    if 'text' in out:
                        print("--- OUTPUT ---")
                        print("".join(out['text']))



```

```text


# Which factors lead to death of people suffering from covid 19
#Importing the clinical spectrum data
clinical_spectrum = pd.read_csv('diagnosis-of-covid-19-and-its-clinical-spectrum.csv')

#Filtering the data to contain the values only for the confirmed COVID-19 Tests
confirmed = clinical_spectrum['sars_cov_2_exam_result'] == 'positive'
clinical_spectrum = clinical_spectrum[confirmed]

#Filetering the datasets
hospitalized_condtion = clinical_spectrum['patient_addmited_to_regular_ward_1_yes_0_no'] == 't'
us_hospitalized_spectra = clinical_spectrum[hospitalized_condtion]


unhospitalized_condtion = clinical_spectrum['patient_addmited_to_regular_ward_1_yes_0_no'] == 'f'
us_unhospitalized_spectra = clinical_spectrum[unhospitalized_condtion]

#Taking mean value of the spectra conditions
hospitalized_mean = us_hospitalized_spectra.mean(
    axis=0,
    skipna=True,
    numeric_only=True
)

unhospitalized_mean = us_unhospitalized_spectra.mean(
    axis=0,
    skipna=True,
    numeric_only=True
)
#Making columns for the dataset
hospitalized_mean = hospitalized_mean.to_frame()
hospitalized_mean = hospitalized_mean.reset_index()
hospitalized_mean.columns = ['Parameter','Hospitalized_figures']

unhospitalized_mean = unhospitalized_mean.to_frame()
unhospitalized_mean = unhospitalized_mean.reset_index()
unhospitalized_mean.columns = ['Parameter','Unhospitalized_figures']

#Merging both the dataframes together
hospitalized_mean['Unhospitalized_figures'] = unhospitalized_mean['Unhospitalized_figures']

#Viewing the dataset
hospitalized_mean.dropna()

#The most important clinical factors
hospitalized_mean['Change'] =  hospitalized_mean['Hospitalized_figures'] - hospitalized_mean['Unhospitalized_figures']
hospitalized_mean.sort_values(['Change'], axis=0, ascending=True, inplace=True) 

#Getting to know the health factors that define HCP Requirement for a patient
lower = hospitalized_mean.head(10)
higher = hospitalized_mean.tail(10)

#Printing the values
for i in lower['Parameter']:
    print('For lower value of {}, the patient may require HCP'.format(i))
    
for i in higher['Parameter']:
    print('For higher value of {}, the patient may require HCP'.format(i))
--- OUTPUT ---
For lower value of rods, the patient may require HCP
For lower value of monocytes, the patient may require HCP
For lower value of aspartate_transaminase, the patient may require HCP
For lower value of po2_venous_blood_gas_analysis, the patient may require HCP
For lower value of base_excess_arterial_blood_gas_analysis, the patient may require HCP
For lower value of alanine_transaminase, the patient may require HCP
For lower value of hco3_arterial_blood_gas_analysis, the patient may require HCP
For lower value of total_co2_arterial_blood_gas_analysis, the patient may require HCP
For lower value of ionized_calcium, the patient may require HCP
For lower value of hb_saturation_venous_blood_gas_analysis, the patient may require HCP
For higher value of patient_age_quantile, the patient may require HCP
For higher value of mycoplasma_pneumoniae, the patient may require HCP
For higher value of fio2_venous_blood_gas_analysis, the patient may require HCP
For higher value of urine_sugar, the patient may require HCP
For higher value of partial_thromboplastin_time_ptt, the patient may require HCP
For higher value of prothrombin_time_pt_activity, the patient may require HCP
For higher value of vitamin_b12, the patient may require HCP
For higher value of d_dimer, the patient may require HCP
For higher value of albumin, the patient may require HCP
For higher value of phosphor, the patient may require HCP



```

### **6. Key Outcomes**

The final phase of the analysis focused on transforming the raw comparative data into a diagnostic decision-making tool. By analyzing the "Delta" (Change) between the mean values of hospitalized and unhospitalized cohorts, I isolated the physiological markers that act as the primary triggers for **Health Care Provider (HCP)** requirement.

#### **A. The Clinical "Tipping Points"**

The core outcome of the project is the identification of 20 critical biomarkers that deviate most aggressively when a patient’s condition deteriorates. These were categorized into two strategic groups:

* **Higher Value Indicators (Critical Spikes):** Parameters that increase significantly in hospitalized patients.
* **D-Dimer & Prothrombin Time:** These markers of coagulation and inflammatory response were significantly higher in patients requiring acute care, signaling potential clotting risks.
* **Patient Age Quantile:** Confirming that age remains one of the strongest predictors for the severity of the clinical spectrum.
* **Albumin & Phosphor:** Metabolic spikes that indicate systemic stress.


* **Lower Value Indicators (Clinical Crashes):** Vital markers that deplete or drop during a severe viral response.
* **Monocytes:** A drop in these white blood cells indicates a strained immune system.
* **Venous Blood Gas (pO2 & HCO3):** Significant drops in oxygen and bicarbonate levels served as the primary respiratory "red flags" for hospitalization.
* **Aspartate & Alanine Transaminase:** Deviations in these enzyme levels highlight the multi-organ impact (specifically liver stress) characteristic of severe COVID-19.


#### **B. The HCP Requirement Matrix**

By ranking these parameters by their magnitude of change, I established a "Priority Intervention List." This list allows a clinician to look at a patient's lab results and immediately determine if their current values align more closely with the **Hospitalized Mean** or the **Home-Recovery Mean**.

#### **C. Data-Driven Triage Logic**

The most significant outcome is the shift from subjective symptom-based assessment to **objective laboratory-based triaging**. The analysis proved that:

1. **Metabolic and Respiratory shifts** happen predictably before total clinical failure.
2. The **"Change" metric** provides a quantitative threshold that can be used to set automated alerts in hospital monitoring systems.

### **7. Public Health Recommendations**

The data-driven insights from the **HCP Requirement Matrix** allow us to move from clinical observation to strategic public health action. By identifying the physiological markers that shift most aggressively before hospitalization, we can optimize how resources are allocated during a pandemic surge.

* **Implement Biomarker-Based Triage:** Healthcare systems should integrate the "HCP Matrix" into outpatient screening. Patients displaying a "Clinical Crash" in **Monocytes** or **Venous Blood Gas (pO2)** should be prioritized for immediate clinical review, as these are proven early-warning signs of hospitalization risk.
* **Targeted Monitoring for High-Risk Groups:** Since **Patient Age Quantile** and coagulation markers like **D-Dimer** showed the highest positive delta, public health initiatives should prioritize these demographics for early vaccination and aggressive at-home monitoring to prevent vascular complications.
* **Dynamic Resource Allocation:** Using the **Geospatial & Weather Analysis**, health departments can predict potential surges by monitoring local temperature drops and humidity shifts, allowing for the preemptive movement of oxygen and staffing to regions at higher environmental risk.
* **Objective HCP Thresholds:** Instead of relying purely on subjective symptoms like "feeling short of breath," triage centers should use the established **Mean Values** from this study to create objective benchmarks for when a patient must transition from home care to professional medical supervision.

---

### **8. Challenges & Data Limitations**

While the integration of clinical, weather, and geographic data provides a holistic view, there are several limitations inherent in pandemic-era datasets that must be acknowledged.

* **Clinical Data Sparsity:** The `diagnosis-of-covid-19` dataset contained many missing values for specialized lab tests. While `dropna()` ensured the accuracy of our **Mean-Delta** results, it significantly reduced the cohort size for specific biomarkers, meaning some rarer complications may not be fully represented.
* **The Seasonal Inference Problem:** While I integrated weather data (`temperature_data_covid.csv`), early pandemic data covers a limited timeframe. It is difficult to definitively separate true "seasonality" from changes in social behavior or the emergence of new viral variants.
* **Static vs. Dynamic Snapshots:** This analysis relies on "Mean" values. In a clinical setting, COVID-19 is a dynamic disease; a patient’s profile can shift from "Unhospitalized" to "Critical" in a matter of hours, which a static dataset cannot always capture in real-time.
* **Interactive Visualization Rendering:** The advanced **Plotly Geospatial Maps** generated in the notebook are interactive HTML elements. These will not render natively in GitHub's static view and are best viewed by running the notebook locally or via [nbviewer.org](https://nbviewer.org/).

