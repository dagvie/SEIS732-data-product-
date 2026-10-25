# SEIS732-data-product
1.  Title: 
Hospital Readmission Drivers and Care Outcomes in Diabetic Patients

3. Audience and need 
Audience: Hospital administrators, clinical directors, and healthcare quality assurance teams.

Need: They need to evaluate how patient demographics, hospital stay durations, medication regimens (e.g., insulin, metformin), and lab procedures correlate with hospital readmission rates for diabetic patients.

Impact: If the data product is useful, administrators can identify clinical pathways that require intervention (e.g., better discharge planning for patients with specific HbA1c results or frequent medication adjustments) to reduce costly hospital readmissions and improve patient care outcomes.

3. Proposed analytical product and questions 
The product will be an interactive clinical dashboard allowing healthcare administrators to analyze patient readmission risks based on treatment variables, demographics, and hospital resource utilization.

Questions it should answer:

How does the length of a hospital stay (time_in_hospital) and the volume of administered treatments (num_medications, num_procedures) correlate with a patient's likelihood of being readmitted?

Do patients who experience a change in diabetic medications (change flag) during their stay have a demonstrably higher or lower readmission rate compared to those on a steady regimen?

How do primary diagnoses (diag_1) and HbA1c test results (A1Cresult) impact the urgency of hospital readmission (e.g., <30 days vs. >30 days)?

4. Source identification and access 
Source dataset name: diabetic_data.csv (Clinical dataset capturing hospital encounters, procedures, and medication tracking for diabetic patients).

Format and Size: CSV format, approximately 101,766 rows and 50 columns.

Proposed Ingestion Plan:

Upload diabetic_data.csv to a secure course Google Cloud Storage bucket (e.g., gs://[project-id]-raw-data/healthcare/).

Use the bq load command-line tool with --autodetect to ingest the CSV from Cloud Storage into a dedicated BigQuery dataset as a native table.

Query the resulting BigQuery table for P1-P4 modeling.

5. Quick source profile 
Proposed questionNeeded fields or conceptsCandidate source object(s)Why the source appears sufficientGap or assumption to test in P1Q1: Resources vs ReadmissionHospital days, medications, procedures, readmission statusdiabetic_data.csvContains precise numerical tracking of time_in_hospital, num_medications, num_procedures, and readmitted status per encounter.Need to test the distribution of readmissions to see if the dataset is heavily skewed toward non-readmitted patients.Q2: Impact of Med ChangesMedication adjustments, specific drugs, readmission ratesdiabetic_data.csvContains a boolean change column alongside specific dosage statuses (Up, Down, Steady) for 24 different diabetes medications.Need to verify if the change flag indicates dosage adjustments or completely new prescriptions.Q3: Diagnoses and LabsDiagnoses, lab results, readmission timelinediabetic_data.csvContains primary ICD-9 diagnosis codes (diag_1) and specific categorical HbA1c result classifications (A1Cresult).Determine the percentage of missing or unmeasured A1C results across all hospital encounters. 

Q3: Sentiment and Gas Prices 
Climate opinion, gas prices, EV registrations 
EV_Data_2.csv 
Contains worried, regulate, gasoline_price_per_gallon, and EV Registrations. 
Determine if opinion metrics shift year-over-year or if they are static snapshots applied across all years. 

7. Feasibility risks and response 
Risk 1: High Cardinality in Diagnosis Codes. The diag_1 through diag_3 columns contain hundreds of specific ICD-9 codes which may overcomplicate grouping and modeling. Response: I will group ICD-9 codes into broader clinical categories (e.g., Circulatory, Respiratory, Digestive) during the P1 transformation phase. Fallback: Focus solely on diabetes-specific primary diagnoses and exclude secondary comorbidities.

Risk 2: Missing Values in Clinical Tests. Early profiling shows that specific lab columns like A1Cresult and max_glu_serum have a high volume of unmeasured instances. Response: I will treat "None" or "Not Measured" as a distinct, valid category rather than standard nulls, as the absence of a test is clinically relevant for hospital compliance. Fallback: Exclude these specific lab columns and focus strictly on general medication and procedure counts.

9. First P1 decision 
Determining the primary fact grain. I need to decide whether the analytical model should focus on the individual patient level (patient_nbr) or the specific hospital encounter level (encounter_id) as the primary grain for measuring readmission events, since patients can have multiple encounters.

Schema Facts:

The diabetic_data.csv file contains 50 columns.

Identifiers (encounter_id, patient_nbr) are distinct integers, while categorical variables (race, gender) are strings.

Measures (time_in_hospital, num_lab_procedures, num_medications) are clean numeric types (INT64).
Scale and Coverage:

Row count: 101,766 rows.

Identifiers: Tracks tens of thousands of unique patients across multiple encounters.

Nulls: Core metrics such as hospital time, readmission status, and medication counts are heavily populated (0 nulls), though some categorical fields use '?' for unknown values which will need to be cleansed.

Link to my repository: https://github.com/dagvie/SEIS732-data-product-

AI/tool accountability

Gemini: Assisted with restructuring the P0 proposal headings, adapting the target persona to healthcare administrators, framing actionable clinical questions, and mapping the schema gaps for the diabetes dataset. 
