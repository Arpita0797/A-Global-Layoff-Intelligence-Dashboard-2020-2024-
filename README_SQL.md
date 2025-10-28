# 💼 Global Layoffs SQL Project — Analyzing Workforce Trends Across Industries

![Layoffs Banner](https://keydifferences.com/wp-content/uploads/2014/08/lay-off-vs-retrenchment1.jpg)

---

## 📊 Project Overview

This project dives deep into **global layoff data** to uncover patterns in how different industries, countries, and company stages were affected between **2020 and 2024**.
Using advanced **SQL queries**, this analysis helps answer key business questions such as:

* Which industries faced the largest layoffs?
* Which companies were hit the hardest each year?
* How do funding stages impact layoff rates?
* What is the correlation between company funding and workforce stability?

The project demonstrates your ability to **clean, aggregate, and analyze large datasets** using SQL, turning raw data into valuable business insights.

---

## 🧠 What This Project Showcases

This project highlights your ability to:

* Write **complex SQL queries** involving subqueries, joins, and window functions.
* Perform **data cleaning** and handle missing or inconsistent values.
* Apply **analytical reasoning** to uncover data-driven trends.
* Derive **real-world insights** useful for decision-making in HR analytics, finance, and business strategy.

---

## 🧩 Dataset Overview

The dataset captures detailed information on global layoffs, including:

* Company name and headquarters location
* Industry and stage of funding (Seed, Series A, IPO, etc.)
* Number of employees laid off
* Percentage of workforce affected
* Total funds raised
* Date of layoff event
* Source of information

Each record represents a company layoff event between **2020 and 2024**, providing a multi-year view of global employment dynamics.

---

## ⚙️ SQL Concepts Used

Throughout the project, the following SQL techniques were applied:

* **Aggregate Functions** → `SUM()`, `COUNT()`, `AVG()`, `ROUND()`
* **Grouping & Filtering** → `GROUP BY`, `HAVING`, `WHERE`
* **Sorting & Ranking** → `ORDER BY`, `LIMIT`
* **Joins and Subqueries** → For identifying top companies by year
* **NULL Handling** → Using  `NULLIF()` to prevent divide-by-zero errors

---

## 🔍 Key Insights

* The **technology industry** experienced the highest number of layoffs globally.
* **Early-stage startups** (Seed and Series A) had the **highest layoff-to-funding ratios**, indicating financial instability.
* The **United States** accounted for the largest share of total layoffs.
* **Post-IPO and acquired companies** showed better resilience compared to those in earlier funding rounds.
* Layoff ratios generally **declined as companies matured**, moving from Seed → Series J → IPO.

---

## 🧮 Sample SQL Queries

** 1)view all data**

```sql
SELECT * FROM layoffs_data LIMIT 7; 
```

---

**2)Count total rows (records)**

```sql
SELECT COUNT(*) AS TOTAL_ROWS FROM layoffs_data;
```

---
---
**3)Total layoffs overall**

```sql
SELECT SUM(Laid_Off_Count) AS TOTAL_LAYOFF FROM layoffs_data;
```

---
---
** 4)Total companies affected**

```sql
SELECT SUM(DISTINCT(Company)) AS AFFECTED_COMAPNY FROM layoffs_data;
```
---
---
** 5)Total funds raised overall**

```sql
SELECT SUM(Funds_raised) AS TOTAL_FUNDS FROM layoffs_data;
```
---
---
** 6)Top 10 companies by total layoffsl**

```sql
SELECT Company ,SUM(Laid_Off_Count) AS TOP_AFFECTED_COMAPNY 
FROM layoffs_data 
GROUP BY COMPANY
ORDER BY TOP_AFFECTED_COMAPNY DESC
LIMIT 10;
```
---
---
**7)Layoffs by country**

```sql
SELECT COUNTRY, SUM(Laid_Off_Count) AS TOP_AFFECTED_COUNTRY
FROM layoffs_data
WHERE COUNTRY IS NOT NULL
GROUP BY COUNTRY
HAVING TOP_AFFECTED_COUNTRY IS NOT NULL
ORDER BY TOP_AFFECTED_COUNTRY DESC;
```

---
---
**8)Layoffs by Industry**

```sql
SELECT Industry,SUM(Laid_Off_Count) AS AFFECTED_INDUSTRY 
FROM layoffs_data
GROUP BY Industry
ORDER BY AFFECTED_INDUSTRY DESC;

SET SQL_SAFE_UPDATES = 0;

DELETE FROM layoffs_data
WHERE Industry = 'Unknown' OR Industry IS NULL;

SELECT Industry FROM  layoffs_data 
WHERE Industry = 'Unknown' OR Industry IS NULL;

SET SQL_SAFE_UPDATES = 1;
```
---
---
** 9)Yearly Layoffs Trend (2020–2024)**

```sql
SELECT YEAR(Lay_off_date) AS YEAR ,
SUM(Laid_Off_Count) AS LAYOFF
FROM layoffs_data 
GROUP BY YEAR 
ORDER BY YEAR DESC;
```
---
---
**  10) Company with Highest Layoffs Each Year**

```sql
SELECT  S.YEAR,S.Company, S.LAYOFF AS MAX_LAYOFF FROM(
SELECT YEAR(Lay_off_date) AS YEAR, Company,SUM(Laid_Off_Count) AS LAYOFF 
FROM layoffs_data 
GROUP BY YEAR,Company
) AS S
JOIN (
SELECT  YEAR, MAX(LAYOFF_PER_COMPANY) AS MAX_LAYOFF 
FROM (
SELECT YEAR(Lay_off_date) AS YEAR, Company,SUM(Laid_Off_Count) AS LAYOFF_PER_COMPANY
FROM layoffs_data 
GROUP BY YEAR,Company) AS TEMP 
GROUP BY YEAR) AS M
ON S.YEAR = M.YEAR AND S.LAYOFF = M.MAX_LAYOFF 
ORDER BY S.YEAR DESC;
```
---
---
**11)Layoffs by Stage**

```sql
SELECT stage,SUM(Laid_Off_Count) AS LAYOFF
FROM layoffs_data 
GROUP BY stage;

SET SQL_SAFE_UPDATES = 0;
DELETE FROM layoffs_data
WHERE stage = 'Unknown' ;
SET SQL_SAFE_UPDATES = 1;
```
---
---
** 12) Funding Raised vs Layoffs Ratio (how many layoffs happen per unit of fund raised) **

```sql
SELECT Company , SUM(Laid_Off_Count) AS LAYOFF, SUM(Funds_raised) AS TOTAL_FUND,
ROUND((SUM(Laid_Off_Count) / SUM(Funds_raised)),4) AS LAYOFF_RATIO
FROM layoffs_data 
WHERE Laid_Off_Count IS NOT NULL AND Funds_raised IS NOT NULL  
GROUP BY Company
HAVING TOTAL_FUND IS NOT NULL AND TOTAL_FUND > 0
ORDER BY LAYOFF_RATIO DESC;
```
---
---
**13)Peak Layoff Year**

```sql
SELECT YEAR(Lay_off_date) AS YEAR , SUM(Laid_Off_Count) AS PEAK_LAYOFF 
FROM layoffs_data 
GROUP BY YEAR 
ORDER BY PEAK_LAYOFF DESC LIMIT 1;
```
---
---
** 14)Most Affected Company**

```sql
SELECT Company AS Most_Affected_Company , SUM(Laid_Off_Count) AS LAYOFF 
FROM layoffs_data 
GROUP BY  Most_Affected_Company
ORDER BY LAYOFF DESC LIMIT 1;
```
---
---
** 15)Average percentage laid off by stage**

```sql
SELECT stage , ROUND(AVG(Percentage_laid_off),2) AS AVG_PERCENTAGE_LAYOFF 
FROM layoffs_data 
GROUP BY stage
ORDER BY AVG_PERCENTAGE_LAYOFF DESC ;

```
---
## 🧾 Data Cleaning Highlights

Before running analysis queries, several cleaning operations were performed:

* Removed rows where `Industry = 'Unknown'`.
* Standardized inconsistent stage names (e.g., “Uknown” → “Unknown”).
* Filtered out null or invalid values from `Lay_off_date` and `Funds_Raised`.
* Converted `Lay_off_date` to proper **DATE format** for yearly analysis.
* **For Example : **
  
```sql
SET SQL_SAFE_UPDATES = 0;

UPDATE layoffs_raw
SET
  Laid_Off_Count = NULLIF(Laid_Off_Count, ''),
  Funds_raised = NULLIF(Funds_raised, ''),
  Percentage_laid_off = NULLIF(Percentage_laid_off, ''),
  Lay_off_date = NULLIF(Lay_off_date, '');

UPDATE layoffs_raw
SET Laid_Off_Count = NULL
WHERE Laid_Off_Count NOT REGEXP '^[0-9]+$';

UPDATE layoffs_raw
SET Funds_raised = NULL
WHERE Funds_raised NOT REGEXP '^[0-9]+$';

UPDATE layoffs_raw
SET Percentage_laid_off = NULL
WHERE Percentage_laid_off NOT REGEXP '^[0-9.]+$';

UPDATE layoffs_raw
SET Lay_off_date = NULL
WHERE Lay_off_date NOT REGEXP '^[0-9]{2}-[0-9]{2}-[0-9]{4}$';

SET SQL_SAFE_UPDATES = 1;


```
  
---

## 📈 Insights Summary

The analysis revealed that global layoffs are **not only influenced by industry trends**, but also by **funding maturity** and **economic cycles**.
Startups in their early funding stages (Seed, Series A, and B) tend to **overhire during growth phases** and then **downsize sharply** when funding tightens.

By contrast, companies that reached IPO or acquisition stages managed to maintain better workforce stability.

---

## 💡 Learning Outcomes

From this project, I gain hands-on experience in:

* Structuring and optimizing SQL queries for large datasets.
* Using nested subqueries to find ranked insights.
* Translating raw employment data into business intelligence.
* Understanding how funding patterns affect company sustainability.

---

---

## 🏁 Conclusion

The Layoff **SQL Analysis Project** turns raw layoff data into actionable insights using SQL.
It uncovers patterns across industries, funding stages, and regions — revealing how economic shifts impact companies and employees.

This project highlights strong skills in data cleaning, analysis, and storytelling, proving how SQL can transform scattered data into clear business intelligence.

---

✨ *Created with SQL, curiosity, and precision — by Arpita Mishra*

---

