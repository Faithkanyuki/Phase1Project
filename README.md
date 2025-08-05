# Data-Driven Aircraft Risk Analysis for Safe Fleet Acquisition

## Overview
This project aims to identify the safest aircraft models for launching a new aviation business by analyzing over 60 years of historical accident data. The goal is to translate this data into actionable recommendations to build a reputation for safety in the aviation industry.

### Key Objectives:
- Identify aircraft models with the lowest historical accident rates and fewer fatal incidents.
- Analyze injury severity and aircraft damage to determine the safest models.
- Provide data-driven recommendations for fleet acquisition.

## Business Understanding
### Stakeholder
The primary stakeholder is a new aviation business looking to establish a safe and reliable fleet.

### Key Business Questions:
- Which aircraft make and model have the lowest historical accident rates?
- Which models result in fewer fatal incidents and lower safety risks?
- How can the business minimize risk while maximizing operational efficiency?

## Data Understanding and Analysis
### Source of Data
The analysis is based on 23,000+ incident reports from 1962 to 2023, sourced from an `Aviation_Data.csv` file.

### Description of Data
The dataset includes:
- Aircraft make and model.
- Incident and accident reports.
- Injury severity and aircraft damage data.
- Safety scores calculated using a weighted metric (60% incidents, 40% fatalities).

### Data Cleanup Steps:
- Excluded models with incomplete records.
- Filled missing values where possible.
- Corrected data inconsistencies.
- Focused on key metrics: total incidents, fatal incidents, and safety scores.

### Visualizations
1. **Top Risky Models**  
   - **Finding:** Cessna 152 (94 incidents), Cessna 172 (178 incidents), and Piper PA-28-140 (33 incidents) dominate incident reports, likely due to their use in training and high fleet numbers.  
   - **Visualization:** Bar chart showing incident counts for the top 20 models.

2. **Damage Severity Analysis**  
   - **Finding:** Cessna 152 has a 35% total loss rate, while Cessna 206B has 92% minor damage—ideal for cost control.  
   - **Visualization:** Pie chart or stacked bar chart showing damage severity distribution for selected models.

3. **Safety Scores**  
   - **Finding:** Boeing 737 has a near-perfect safety score (9,999.60) with only 1 incident, while Cessna 206B combines low damage rates with strong safety performance.  
   - **Visualization:** Table or heatmap comparing safety scores, incidents, and fatal accidents across models.

## Conclusion
### Summary of Findings
1. **High-Risk Models:** Cessna 152, Cessna 172, and Piper PA-28-140 are high-risk due to their high incident counts and severe damage rates. These models are not ideal for new operators without risk-sharing agreements.
2. **Low-Damage Option:** Cessna 206B is a standout choice, with 92% of accidents resulting in minor damage, making it ideal for cost control and operational safety.
3. **Commercial Viability:** Boeing 737 has an exceptional safety record (only 1 incident) and is recommended for passenger routes.

### Recommendations
- **Immediate Buys:** Prioritize acquiring Cessna 206B for its low damage rates.
- **Commercial Option:** Lease Boeing 737 for passenger routes due to its validated safety.
- **Conditional Use:** Consider Cessna 152/172 only in partnership with flight schools to transfer risk.

---


# Kindly Find my Attached link to tableau dashboard

https://public.tableau.com/views/Project_Aviation_Phase001/AirSafetyDashboard?:language=en-US&publish=yes&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link

<img width="1302" height="537" alt="Air Safety Dashboard" src="https://github.com/user-attachments/assets/965162e2-a067-4e1c-9121-e212cd09f690" />




