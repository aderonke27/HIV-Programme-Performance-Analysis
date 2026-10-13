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
The five datasets were imported as CSV files and were loaded into Power BI through Get Data, then opened in Power Query for cleaning before loading for analysis and visualization.

## Data Cleaning and Transformation
* Blank rows in patients.csv. The file contains 20 empty rows beyond the 400 real patient records. These were removed so they didn't inflate counts or distort rates.
* Date columns stored as text. Columns such as date_of_diagnosis, art_start_date, last_appointment_date and test_date were converted to proper date types so they could connect to a date table.
* Intentional blanks in the follow-up columns. follow_up_call_made and returned_after_follow_up are blank (or "N/A") for patients who kept their last appointment.These were left as they are, since the blank carries meaning: no follow-up was needed.
* Month names as text. Month fields (test_month, month in the stock file) were text. I added sort columns (test month sort, Month Sort) so charts display January to December in order and not alphabetically.
* Calculated columns. A DateTable was created for time filtering.

Calculated Columns
* Rapid ART Started = VAR DaysToART = DATEDIFF(patients[date_of_diagnosis], patients[art_start_date], DAY) RETURN IF(DaysToART <= 7, "Yes", "No")

## Data Modeling
The five tables are linked as a star-style model with facilities as the central lookup.
<img width="1591" height="785" alt="Screenshot (259)" src="https://github.com/user-attachments/assets/5671aaba-f1dd-4a84-a88b-ca23e1cd4c24" />
* facilities to patients: one to many, linked on facility_id.
* facilities to pharmacy_stock: one to many, linked on facility_id.
* facilities to targets: one to one, linked on facility_id.
* patients to viral_load_tests: one to many, linked on patient_id.
* DateTable to patients: one to many, linked on art_start_date.

Why it's built this way: a patient can have several viral load tests, so tests sit on the "many" side. Facility is the shared dimension, which is what lets every page be filtered or split by facility. Targets are one row per facility per year, so they join one to one.

## Data Analysis
Key measures built in DAX:
* Total Patients: count of patient records.
* Active Patients: patients with treatment status "Active".
* Retention Rate %: Active ÷ (Active + LTFU).
* Total viral load tests: count of all tests.
* Latest Suppressed: patients whose most recent test was suppressed.
* % Suppressed (Latest): Latest Suppressed ÷ patients tested.
* ART Initiation Rate %: Total patient to start ART with 7 days ÷ Total patients
* Missed Appointments, Follow-Up Calls Made, Returned After Call: counts that feed the follow-up funnel.

Suppression is measured on each patient's latest test, not across every test. This avoids counting one patient several times and gives a better picture of who is suppressed today.

DAX
* Active patients = CALCULATE(COUNTROWS(patients),patients[treatment_status] = "Active")
* ART Initiation Rate % = DIVIDE(CALCULATE(COUNTROWS(patients), patients[Rapid ART Started] = "Yes"), COUNTROWS(patients))
* Missed Appointments = CALCULATE(COUNTROWS(patients), patients[last_appointment_status] = "Missed")
* Follow-Up Calls Made = CALCULATE(COUNTROWS(patients), patients[follow_up_call_made] = "Yes")
* Returned After Call = CALCULATE(COUNTROWS(patients), patients[returned_after_follow_up] = "Yes")
* Retention rate % = DIVIDE([Active patients], CALCULATE(COUNTROWS(patients), patients[treatment_status] IN {"Active", "LTFU"}))
* Suppressed Patients = CALCULATE(COUNTROWS(viral_load_tests), viral_load_tests[suppressed] = "Yes", FILTER(viral_load_tests,viral_load_tests[test_date] = [Latest Test Date per Patient]))
* Suppression rate = DIVIDE([Suppressed Patients], [Total Test])

## Data Visualization
The dashboard has four pages.
* HIV Programme Overview: Headline cards (total patients, active patients, retention rate), a treatment status donut, ART initiation and retention by facility, and a colour-coded table comparing each facility's suppression, ART initiation and testing against target. A date slicer filters by month.
<img width="1421" height="806" alt="Screenshot (251)" src="https://github.com/user-attachments/assets/60d7eee8-2037-4768-b4f3-d251c62b3128" />
* Viral Load Testing & Suppression: Total tests, suppressed patients and suppression rate, then a breakdown by facility and two trend lines showing tests and suppression over time.
<img width="1436" height="808" alt="Screenshot (252)" src="https://github.com/user-attachments/assets/fcbbdf02-b85e-4dbc-8f09-88aa696a37c4" />
* ART Stock & Appointment Follow-up: Current stock per facility (filterable by drug), stockout days by facility (filterable by month), dispensing trends by regimen, appointment attendance, and a funnel from missed appointments to follow-up calls to patients who returned.
<img width="1428" height="810" alt="Screenshot (253)" src="https://github.com/user-attachments/assets/90931222-4bee-4765-848a-32bfe49debb9" />
* Patient Demographics: Patients by facility, regimen, gender and age group, with suppression and retention rates split by gender and age group.
<img width="1457" height="817" alt="Screenshot (261)" src="https://github.com/user-attachments/assets/4c91286b-5341-4b29-8f0a-d9118fea64c3" />

## Key Insights
* No facility is close to its suppression target. Every facility has a 90% viral suppression target. The best performer, Crestview District Hospital, sits at 77.14%, and the worst, Maplewood Community Clinic, at 63.33%. Overall suppression is 71.75%.
* ART initiation is the biggest gap against target. Against a 90% target, facilities range from 48.89% (Silverstone General Hospital) to 58.57% (Crestview District Hospital). Silverstone is the largest facility after Brightfield, so its low rate affects a lot of patients.
* One in four appointments is missed, and follow-up is leaving patients behind. 103 patients (25.75%) missed their last appointment. Only 77 received a follow-up call, and 51 returned. That means 26 missed patients were never called at all, and 49.5% of all missed patients came back. Among those who were called, about two in three returned, so the calls work. The gap is that not everyone gets one.
* Missed appointments are concentrated in a few facilities. Hopeville has the highest missed rate (about 36% of its patients), followed by Brightfield (about 31%) and Maplewood (30%).
* Brightfield has the weakest retention. At 82.83%, it has the lowest retention rate and the most patients lost to follow-up (17). It also has the largest patient load (110).
* Stockouts are limited to one drug at two facilities. Hopeville recorded 30 stockout days and Maplewood 22, both for TLD, the most widely used regimen (229 of 400 patients). The other four facilities had none. Hopeville and Maplewood are also two of the weaker performers on missed appointments, so supply and attendance may be linked.
* Seniors are suppressed least but retained best. Patients aged 50+ have the lowest suppression (66.67%) and the highest retention (90.00%). Paediatric patients have the lowest retention (83.33%). Gender differences in suppression are small (71.92% female vs 71.43% male).
* Suppression trends upward on the dashboard. The suppression rate over time line rises from early 2023 through late 2024, with a dip around July 2024.

## Recommendations
* Make follow-up calls universal. Set a rule that every missed appointment gets a call within a fixed number of days. Closing the 26-patient gap is the most direct way to lift retention.
* Focus ART initiation efforts on Silverstone and the other low performers. A rate near 50% against a 90% target points to delays between linkage and treatment start. Review that process facility by facility.
* Prioritise TLD supply at Hopeville and Maplewood. Both ran out of the main regimen. Early reorder triggers would help avoid more stockout days.
* Run a viral load catch-up at Maplewood and Brightfield. They have the lowest suppression rates, so enhanced adherence counselling and repeat testing should go there first.
* Check retention support for Brightfield and for paediatric patients. They have the lowest retention in their groups.
* Review targets alongside results. Targets are set at 90% everywhere. If no facility is near them, leadership may want to agree interim milestones so progress is visible.

## Limitations
* Targets are annual in the data. targets.csv holds one annual target per facility, so monthly targets couldn't be shown. The Targets vs Actuals view on the overview page compares against annual figures.
* Testing target comparison mixes years. "Tests Done" counts tests across 2023 and 2024 (737), while the testing target is for 2024 only. Only 365 tests fall in 2024, so the true gap to target is larger than the table suggests.
* Pharmacy data covers 2024 only, so stock trends can't be compared with earlier years.
* Viral load data has no 2024 December tests. The last test date is 29 November 2024, so December is missing from the trend lines.
* Patients due for refill or review. The model includes Days Until Due and Due This Month columns, but this isn't shown as its own visual yet.

## Conclusion
The dashboard gives EndHIV Foundation one place to see programme performance and removes the manual copy-and-paste reporting process. The data points to three priorities: viral suppression and ART initiation are well below the 90% targets, one in four patients misses appointments with incomplete follow-up, and TLD stockouts at two facilities need attention. Because the report reads from a cloud data source, leadership can see these numbers update as new data comes in.

## Project Files
EndHIV Foundation Datasets
* [Patients Dataset](./patients.csv)
* [Facilities Dataset](./facilities.csv)
* [Pharmacy Stock Dataset](./pharmacy_stock.csv)
* [Targets Dataset](./targets.csv)
* [Viral Load Tests Dataset](./viral_load_tests.csv)

[View Power BI report](./EndHIV_Foundation_PowerBI_report.pbix)

## Contact Information
* Email: aladeloyeesther616@gmail.com
* LinkedIn: https://linkedin.com/in/estheraderonke
