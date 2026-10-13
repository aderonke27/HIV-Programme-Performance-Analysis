## HIV Programme Performance Analysis
![Power BI](https://img.shields.io/badge/Tool-Power%20BI-F2C811?style=flat&logo=powerbi&logoColor=black)
![Domain](https://img.shields.io/badge/Domain-Healthcare%20Analytics-red)

## Table of Contents
1. [Project Overview](#project-overview)
2. [Project Statement](#project-statement)
3. [Project Aim](#project-aim)
4. [About the Dataset](#about-the-dataset)
5. [Tools Used](#tools-used)  
6. [Importing the Dataset](#importing-the-dataset)  
7. [Data Cleaning and Transformation](#data-cleaning-and-transformation)
8. [Data Modeling](#data-modeling)  
9. [Data Analysis](#data-analysis)  
10. [Data Visualization](#data-visualization)  
11. [Key Insights](#key-insights)  
12. [Recommendations](#recommendations)
13. [Limitations](#limitations)
14. [Conclusion](#conclusion)
15. [Project Files](#project-files)
16. [Contact Information](#contact-information)

## Project Overview
EndHIV Foundation is a Nigerian NGO running an HIV treatment and care programme across six facilities in Lagos and Abuja. This project replaces their manual Excel reporting with a four-page Power BI dashboard covering programme performance, viral load testing and suppression, drug stock and appointment follow-up, and patient demographics. The dataset covers 400 patients, 737 viral load tests (2023 to 2024), and 12 months of 2024 pharmacy stock data.

## Project Statement
Until now, EndHIV Foundation tracked programme data in one Excel spreadsheet per facility, updated by hand every month. A programme coordinator copied and pasted data across those files to build a summary for leadership and donors. That process was slow and easy to get wrong. By the time a report reached leadership, the numbers were already weeks old, so decisions about retention, testing and drug supply were being made on outdated data.

## Project Aim
Build a dashboard that any authorised team member can open to see current programme performance, without anyone compiling a report by hand. When the underlying data changes, the dashboard should reflect it automatically or with a simple refresh.

Specific goals:
* Show programme size, treatment status and retention at a glance.
* Track viral load testing and suppression, by facility and over time.
* Compare facility performance against their targets.
* Flag missed appointments, follow-up outcomes and drug stockouts.
* Break key metrics down by gender, age group, regimen and facility.
* Prove the live update works by changing the data source and watching the dashboard respod.

## About the Dataset
Five CSV files, representing EndHIV Foundation's programme data. The patient records are synthetic.
* patients.csv (400 patients): one row per patient, with demographics, regimen, treatment status, and appointment and follow-up data.
* viral_load_tests.csv (737 tests): one row per viral load test. A patient can have several tests over time.
* facilities.csv (6 facilities): a lookup table with name, state, LGA and facility type.
* pharmacy_stock.csv (216 rows): monthly stock per facility and drug, including opening, received, dispensed and closing stock, and stockout days.
* targets.csv (6 rows): 2024 testing, ART initiation and viral suppression targets per facility.
The six facilities are Brightfield Medical Centre, Greenleaf Community Health Centre, Hopeville Primary Health Clinic, Silverstone General Hospital, Crestview District Hospital and Maplewood Community Clinic.

Three ART regimens appear in the data: TLD, TLE and ABC+3TC+LPV/r.

## Tools Used
* Power BI Desktop: data modeling, DAX measures and report design
* Power Query: data cleaning and transformation

## Importing the Dataset
The five CSV files were loaded into Power BI through Get Data, then opened in Power Query to check column types and clean the data before loading.

## Data Cleaning and Transformation

## Data Modeling
The five tables are linked as a star-style model with facilities as the central lookup.
<img width="1591" height="785" alt="Screenshot (259)" src="https://github.com/user-attachments/assets/5671aaba-f1dd-4a84-a88b-ca23e1cd4c24" />
* facilities to patients: one to many, linked on facility_id.
* facilities to pharmacy_stock: one to many, linked on facility_id.
* facilities to targets: one to one, linked on facility_id.
* patients to viral_load_tests: one to many, linked on patient_id.
* DateTable to patients: one to many, linked on art_start_date
Why it's built this way: a patient can have several viral load tests, so tests sit on the "many" side. Facility is the shared dimension, which is what lets every page be filtered or split by facility. Targets are one row per facility per year, so they join one to one.

## Data Analysis

## Data Visualization

## Key Insights

## Recommendations

## Limitations

## Conclusion
The dashboard gives EndHIV Foundation one place to see programme performance and removes the manual copy-and-paste reporting process. The data points to three priorities: viral suppression and ART initiation are well below the 90% targets, one in four patients misses appointments with incomplete follow-up, and TLD stockouts at two facilities need attention. Because the report reads from a cloud data source, leadership can see these numbers update as new data comes in.

## Project Files

## Contact Information
Email: aladeloyeesther616@gmail.com
LinkedIn: https://linkedin.com/in/estheraderonke
