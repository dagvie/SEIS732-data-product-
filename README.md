# SEIS732-data-product-
1.  Title: 
State-Level EV Adoption and Infrastructure Benchmarking

3. Audience and need 
Audience: State-level Department of Transportation (DOT) planners and regional environmental policymakers. 

Need: They need to evaluate how public charging infrastructure growth (Level 2 vs. DC Fast chargers), state incentives, and public climate sentiment correlate with actual EV adoption over time. 

Impact: If the data product is useful, policymakers can identify whether funding should be prioritized toward more DC Fast chargers, broader tax incentives, or public education to accelerate EV market share growth in their specific state. 

3. Proposed analytical product and questions 
The product will be an interactive dashboard allowing state planners to compare their state's EV adoption trajectory against peer states with similar demographics or infrastructure investments. 

Questions it should answer: 
How does the availability of DC Fast versus Level 2 charging stations correlate with the state's EV Market Share? 
Do states with higher financial incentives show a demonstrably faster adoption curve between 2016 and 2023? 
How do public climate sentiment (e.g., climate change worry, support for regulations) and gasoline prices impact the share of electric vehicle registrations? 

4. Source identification and access 
Source dataset name: EV_Data_2.csv (External dataset aggregating EV registrations, infrastructure, demographics, and climate opinion data). 
Format and Size: CSV format, approximately 408 rows (51 states/districts over 8 years: 2016–2023). 

Proposed Ingestion Plan: 
Upload EV_Data_2.csv to a secure course Google Cloud Storage bucket. 
Use the bq load command-line tool with --autodetect to ingest the CSV from Cloud Storage into a dedicated BigQuery dataset as a native table. 
Query the resulting BigQuery table natively for P1–P4 modeling. 

5. Quick source profile 
(See evidence/p0-feasibility-results.md for the summarized results of the local profiling script). 
6. Need-to-source fit 
Proposed question 
Needed fields or concepts 
Candidate source object(s) 
Why the source appears sufficient 
Gap or assumption to test in P1

Q1: Infrastructure vs Adoption 
EV share, charging stations (L1, L2, DC) 
EV_Data_2.csv 
Contains EV Share (%), Level 2, DC Fast, and Total Charging Outlets by state and year. 
Are the charging station counts cumulative, or do they represent new installations per year? 
Q2: Impact of Incentives 
Financial incentives, adoption curve over time 
EV_Data_2.csv 
Contains Incentives (numeric) and longitudinal year data (2016–2023). 
Need to verify what unit the Incentives column represents (e.g., total dollar amount, number of distinct incentive programs). 

Q3: Sentiment and Gas Prices 
Climate opinion, gas prices, EV registrations 
EV_Data_2.csv 
Contains worried, regulate, gasoline_price_per_gallon, and EV Registrations. 
Determine if opinion metrics shift year-over-year or if they are static snapshots applied across all years. 

7. Feasibility risks and response 
Risk 1: Static Source for Change Data Capture. The course eventually requires change-processing evidence. A static CSV does not naturally update. Response: I will partition the data by year. Fallback: I will hold back the 2023 data during initial P1/P2 modeling, treating 2016-2022 as the historical load, and use the 2023 rows as a bounded delta/change case for later milestones. 

Risk 2: Metric Units and Definitions. The affectweather, worried, and Incentives columns lack explicit data dictionaries in the raw file. Response: I will profile the min/max ranges in P1 to deduce if they are percentages (0-100) or raw counts. Fallback: Exclude the opinion columns and focus strictly on hard infrastructure (chargers) and economic indicators (gas prices/income). 

8. First P1 decision 
Determining the primary fact grain. I need to decide whether the analytical model should focus on absolute EV Registrations or EV Share (%) as the primary measure of success for state performance. 
AI/tool accountability 

Gemini: Assisted with structuring the P0 proposal headings, defining the target persona and actionable decision questions, framing the need-to-source gap analysis. 
