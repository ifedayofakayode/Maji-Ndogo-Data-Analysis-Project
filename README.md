# Maji-Ndogo-Data-Analysis-Project

## Table of Contents
- [Maji Ndogo Part I](#maji-ndogo-part-i)
- [Maji Ndogo Part II](#maji-ndogo-part-ii)
- [Maji Ndogo Part III](#maji-ndogo-part-iii)
- [Maji Ndogo Part IV](#maji-ndogo-part-iv)

## Maji Ndogo Part I


## 📘 Overview

In this initial phase of the Maji Ndogo water project, our mission was to **explore, validate, and clean a national water survey database** consisting of 60,000+ records. This foundational work set the stage for subsequent analysis and data-driven decisions aimed at resolving the water access crisis in the fictional country of Maji Ndogo.

---

## 🛠 Tools & Technologies

- **SQL** (MySQL Workbench)
- **Python** (Jupyter Notebook)
- **Relational Database**: `md_water_services`
- **Tables Used**: `employee`, `location`, `visits`, `water_quality`, `water_source`, `well_pollution`, `data_dictionary`

---

## 🎯 Project Goals

- Understand the data structure and table relationships
- Identify types of water sources and their usage
- Examine visit patterns and queue times
- Assess subjective water quality scores
- Investigate pollution data and correct inconsistencies

---

## 📊 Key Tasks & Insights

### 1. Data Discovery
- Used `SHOW TABLES` to list all tables.
- Ran `SELECT * FROM [table] LIMIT 5;` to examine sample records.
- Located and reviewed the embedded `data_dictionary` table for metadata on columns.

### 2. Water Source Types
- Identified five key water source types:
  - `tap_in_home`
  - `tap_in_home_broken`
  - `shared_tap`
  - `well`
  - `river`
- Noted that household data was aggregated into single records (e.g., 956 people served = approx. 160 homes).

### 3. Visit Log Analysis
- Reviewed the `visits` table to analyze:
  - Repeated visits
  - Time of day
  - `time_in_queue` metrics
- Found extreme queue times > 500 minutes at some shared taps.

### 4. Water Quality Evaluation
- Explored `water_quality` table:
  - Scores ranged from 1 to 10.
  - Unexpected: 218 home taps rated 10 were visited multiple times (likely incorrect).

### 5. Pollution Data Validation
- Reviewed the `well_pollution` table:
  - Key fields: `description`, `pollutant_ppm`, `biological`, `results`
  - Found rows labeled as "Clean" despite high `biological` contamination.

### 6. Data Cleaning & Correction

**Created a copy for safe editing:**

```sql
CREATE TABLE well_pollution_copy AS
SELECT * FROM well_pollution;
```

**Cleaned misleading descriptions:**

```sql
UPDATE well_pollution_copy
SET description = 'Bacteria: E. coli'
WHERE description = 'Clean Bacteria: E. coli';

UPDATE well_pollution_copy
SET description = 'Bacteria: Giardia Lamblia'
WHERE description = 'Clean Bacteria: Giardia Lamblia';

```

**Corrected misclassified results:**
```sql
UPDATE well_pollution_copy
SET results = 'Contaminated: Biological'
WHERE biological > 0.01 AND results = 'Clean';

```

**Verified fixes:**
```sql
SELECT * FROM well_pollution_copy
WHERE description LIKE 'Clean%'
   OR (results = 'Clean' AND biological > 0.01);

```
## Results
- Successfully validated and cleaned the pollution data for wells.
- Identified patterns of queue time delays at shared taps.
- Detected and documented inconsistencies in water quality scores and visit patterns.
- Built a reliable foundation for audit-based investigation and repair prioritization in later phases.

## Lessons learned
- Always back up critical data before applying transformations.
- SQL string operations (LIKE, REPLACE) are invaluable for real-world data cleaning.
- Even high-quality survey data can hide errors—validation is crucial.
- The data_dictionary table is essential for understanding data semantics.



## Maji Ndogo Part II

## 🌍 Project Overview

This phase of the **Maji Ndogo** data analysis project focused on exploring and cleaning survey data, understanding the geographical and structural distribution of water sources, identifying service issues, and proposing a data-driven repair strategy. Through structured SQL queries and data storytelling, we uncovered critical insights that lay the groundwork for policy and infrastructure improvements.

---

## 📂 Key Objectives

- Clean and update employee data.
- Identify top-performing surveyors.
- Map the distribution of water sources across towns and provinces.
- Analyze the types and usage of water sources.
- Investigate water collection queue durations.
- Rank water sources for prioritized repairs.

---

## 🛠 Tools Used

- **Database:** MySQL
- **Interface:** MySQL Workbench, Jupyter Notebook
- **Languages:** SQL, Python
- **Data Types:** Structured relational tables (e.g., `employee`, `location`, `water_source`, `visits`)

---

## 🧼 Data Cleaning Tasks

- Created standardized email addresses for employees using `LOWER()` and `REPLACE()`.
- Removed trailing spaces from phone numbers using `TRIM()`.
- Validated lengths of phone number entries.
- Updated database fields with cleaned data via `UPDATE` queries.

---

## 🎖 Honouring Top Field Workers

Identified the top 3 field workers based on the number of locations surveyed. Extracted their names, emails, and phone numbers to share commendations from leadership.

---

## 📍 Location Insights

- Counted water sources by **town**, **province**, and **location type**.
- Found that **60%** of water sources are located in **rural** areas.
- Observed that most towns and provinces are well-represented in the dataset.

---

## 🚰 Water Source Analysis

- Calculated the number of sources for each water type (e.g., tap_in_home, shared_tap, river).
- Assessed the **average number of people** served per source.
- Evaluated **total population** served by each source type and converted results to percentages:
  
  | Water Source Type    | % of Population Served |
  |----------------------|------------------------|
  | Shared Tap           | 43%                    |
  | Well                 | 18%                    |
  | Tap in Home          | 17%                    |
  | Broken Tap in Home   | 14%                    |
  | River                | 9%                     |

- Highlighted infrastructure inefficiencies (e.g., ~45% of home taps are non-functional).

---

## 🛠 Repair Strategy

Used SQL `RANK()`, `DENSE_RANK()`, and `ROW_NUMBER()` to:

- Rank source types by total people served.
- Prioritize water sources for repair based on how many people rely on them.
- Created ranked tables that can guide engineers on which sources to repair first.

---

## ⏱ Queue Analysis

- Found that the **survey period spanned ~2.5 years**.
- Discovered that **average queue time** was around **123 minutes**, excluding home taps.
- Suggested temporal patterns in queue behavior (weekdays, times of day) using `DateTime` functions.

---

## 🧠 Key Insights

- A significant portion of the population relies on shared taps and wells.
- Repairs should prioritize **high-traffic shared taps**.
- Data-driven ranking ensures maximum impact from limited resources.
- Combining technical SQL skills with contextual reasoning enhances storytelling and impact.

---

## 📎 How to Reproduce

1. Load the md_water_services dataset into MySQL Workbench.
2. Follow the SQL walkthrough to run each query in sequence.
3. Create views and temporary tables as needed to simplify repeatable analysis.

---


## Maji Ndogo Part III

## 📌 Project Overview

This project investigates data integrity issues in the **Maji Ndogo Water Project**, a fictional public water management system. Through structured SQL-based analysis, we examine audit discrepancies in water quality scores and assess the potential presence of human error—or deliberate data manipulation—among field officers.

---

## 🗂️ Project Structure

- **Entity Relationship Diagram (ERD):** Developed to understand the structure and interrelation of the database tables.
- **Auditor Report Integration:** A CSV audit report was ingested and incorporated into the existing MySQL database.
- **Data Linkage:** Joined audit records to operational data using keys from `visits`, `employee`, and `water_quality` tables.
- **Error Analysis:** Compared subjective water quality scores with independently verified scores to assess discrepancies.
- **Corruption Detection:** Flagged employees whose records deviated significantly from auditor results and were linked to suspicious qualitative statements.

---

## 🛠️ Tools & Technologies

- **Database:** MySQL
- **Workbench:** MySQL Workbench
- **Data Format:** CSV (Auditor report)
- **Query Techniques:** Complex JOINs, CTEs, views, grouping, and filtering

---

## 📊 Key Analyses

### 1. Score Comparison
- **Objective:** Compare water quality scores from employees vs auditors.
- **Outcome:** 94% of scores matched. 6% showed discrepancies (102 out of 1620 records).

### 2. Error Attribution
- Identified field officers responsible for incorrect scores.
- Created a view `Incorrect_records` for ongoing validation and simplified querying.

### 3. Employee Performance
- Aggregated number of errors per employee.
- Highlighted those with above-average errors.

### 4. Corruption Investigation
- Cross-referenced employee error rates with qualitative statements in audit logs.
- Identified 4 employees (e.g., Bello Azibo, Zuriel Matembo) with both high error rates and statements suggesting misconduct.
- Verified that only these employees were linked to statements mentioning "cash".

---

## 📈 Results

| Employee Name     | No. of Mistakes | Evidence of Suspicion |
|-------------------|------------------|------------------------|
| Bello Azibo       | 26               | Yes (statements + score mismatch) |
| Zuriel Matembo    | 17               | Yes                    |
| Malachi Mavuso    | 21               | Yes                    |
| Lalitha Kaburi    | 7                | Yes                    |

These findings suggest a strong need for further HR investigation and policy review to safeguard data quality.

---

## 💡 Lessons Learned

- The importance of **ERD-based planning** before querying relational data.
- How **audit trails** and **statements** can reinforce or expose gaps in quantitative data analysis.
- Using **SQL views and CTEs** to modularize and manage complex logic.
- Cross-validating operational data with **independent audits** improves public service accountability.

---

## 🚀 How to Run
-Clone the repo or download the project files.
- Set up the md_water_services database in MySQL Workbench.
- Import the auditor_report.csv using the provided SQL schema.
- Use the queries in the walkthrough (or define Incorrect_records view) to reproduce the analysis.


## Maji Ndogo Part IV


---

## Contact
Feel free to reach out if you want to discuss this project or collaborate!


