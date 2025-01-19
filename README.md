#  Meningitis Disease Outbreak Prediction in Nigeria

![Meningiti’s](https://brainfoundation.org.au/wp-content/uploads/2022/12/Meningitis_generic-diagram.png)

## 📄 Overview

This project analyzes a synthetic Meningitis dataset to explore patterns and predict potential outbreaks in Nigeria. Using SQL queries, it investigates disease distributions, trends, and key factors that may contribute to the spread of meningitis. The goal is to improve outbreak prediction and assist health policy in implementing effective preventive measures such as vaccination strategies.

---

## 📂 Dataset

Access the dataset here: [https://www.kaggle.com/datasets/eiodelami/disease-outbreaks-in-nigeria-datasets).

---

## 🔍 SQL Query Analysis

### 1. Find all records of meningitis cases reported in the state of "Rivers" in the year 2023.
```
select * 
from [meningitis_dataset]
where state = 'Rivers'
```

#### 2. Count the total number of cases for each disease reported in the dataset.
```
select count(malaria) as Malariacases
from [meningitis_dataset]
where malaria = 1

UNION ALL 

select count(rubella_mars) as rubella_marscases
from [meningitis_dataset]
where rubella_mars = 1
```

#### 3. Count cases of multiple diseases
```
SELECT
    COUNT(CASE WHEN malaria = 1 THEN 1 END) AS MalariaCases,
    COUNT(CASE WHEN rubella_mars = 1 THEN 1 END) AS RubellaMarsCases,
    COUNT(CASE WHEN yellow_fever = 1 THEN 1 END) AS YellowFeverCases
FROM [meningitis_dataset];
```

#### 4. Retrieve states that reported more than 1,000 confirmed cases of meningitis.
```
select count(confirmed) as confirmedcases, state
from [meningitis_dataset]
group by state
having count(confirmed) > 1000
order by count(confirmed)
```

#### 5. Calculate the total cases for each gender in the datase
```
select count(confirmed) as confirmedcases, gender_male
from [meningitis_dataset]
group by gender_male
having gender_male = 1

UNION ALL

select count(confirmed) as confirmedcases, gender_female
from [meningitis_dataset]
group by gender_female
having gender_female = 1
```

#### 6. Total male and female cases.
```
select 
  count(case when gender_male = 1 then 1 END) as malecases,
  count(case when gender_female = 1 then 1 END) as femalecases
from [meningitis_dataset]
```

#### 7.  Extract the month and year from the report_date and count cases reported in each month.
```
with cte1 as (
    select *, datepart(month, report_date) as month_report_date, datepart(year, report_date) as year_report_date
    from [meningitis_dataset])
select count(case when report_outcome = 'confirmed' then 1 end) as numberofcases, 
       month_report_date, year_report_date
from cte1
group by year_report_date, month_report_date
order by year_report_date, month_report_date
```

#### 8.  Calculate the percentage of cases for each disease by year and state.
```
with cte1 AS (
    SELECT
        COUNT(CASE WHEN malaria = 1 THEN 1 END) AS MalariaCases,
        COUNT(CASE WHEN malaria = 0 THEN 1 END) AS MalariaNpCases,
        COUNT(CASE WHEN malaria = 1 THEN 1 END) + COUNT(CASE WHEN malaria = 0 THEN 1 END) AS totalnumermalaria,
        COUNT(CASE WHEN malaria = 1 THEN 1 END) * 1.0 / Nullif((COUNT(CASE WHEN malaria = 1 THEN 1 END) + COUNT(CASE WHEN malaria = 0 THEN 1 END)), 0) AS percentageMalaria,
        
        COUNT(CASE WHEN rubella_mars = 1 THEN 1 END) AS RubellaMarsCases,
        COUNT(CASE WHEN rubella_mars = 0 THEN 1 END) AS RubellaMarsNoCases,
        COUNT(CASE WHEN rubella_mars = 1 THEN 1 END) + COUNT(CASE WHEN rubella_mars = 0 THEN 1 END) AS totalnumerrubella,
        COUNT(CASE WHEN rubella_mars = 1 THEN 1 END) * 1.0 / Nullif((COUNT(CASE WHEN rubella_mars = 1 THEN 1 END) + COUNT(CASE WHEN rubella_mars = 0 THEN 1 END)), 0) AS percnetageRubella,
        
        report_year, state
    FROM [meningitis_dataset]
    GROUP BY report_year, state)
select percnetageRubella, percentageMalaria, report_year, state
from cte1
order by report_year
```

#### 9.  Calculate the percentage of cases for each disease by year and state.
```
with cte1 AS (
    SELECT
        COUNT(CASE WHEN malaria = 1 THEN 1 END) AS MalariaCases,
        COUNT(CASE WHEN malaria = 0 THEN 1 END) AS MalariaNpCases,
        COUNT(CASE WHEN malaria = 1 THEN 1 END) + COUNT(CASE WHEN malaria = 0 THEN 1 END) AS totalnumermalaria,
        COUNT(CASE WHEN malaria = 1 THEN 1 END) * 1.0 / Nullif((COUNT(CASE WHEN malaria = 1 THEN 1 END) + COUNT(CASE WHEN malaria = 0 THEN 1 END)), 0) AS percentageMalaria,
        
        COUNT(CASE WHEN rubella_mars = 1 THEN 1 END) AS RubellaMarsCases,
        COUNT(CASE WHEN rubella_mars = 0 THEN 1 END) AS RubellaMarsNoCases,
        COUNT(CASE WHEN rubella_mars = 1 THEN 1 END) + COUNT(CASE WHEN rubella_mars = 0 THEN 1 END) AS totalnumerrubella,
        COUNT(CASE WHEN rubella_mars = 1 THEN 1 END) * 1.0 / Nullif((COUNT(CASE WHEN rubella_mars = 1 THEN 1 END) + COUNT(CASE WHEN rubella_mars = 0 THEN 1 END)), 0) AS percnetageRubella,
        
        report_year, state
    FROM [meningitis_dataset]
    GROUP BY report_year, state)
select percnetageRubella, percentageMalaria, report_year, state
from cte1
order by report_year
```

#### 10.  Identify the top 5 states with the highest number of confirmed cases of meningitis.
```
select TOP 5 state, count(case when confirmed = 1 then 1 end) as numberofconfirmedcases
from [meningitis_dataset]
group by state
order by count(case when confirmed = 1 then 1 end) desc
```

#### 11.  Rank states based on the number of confirmed cases using a window function.
```
select state, count(case when confirmed = 1 then 1 end) as numberofconfirmedcases, 
       row_number() over (order by count(case when confirmed = 1 then 1 end) desc)
from [meningitis_dataset]
group by state
```

#### 12. Retrieve records with "not confirmed" outcomes but non-zero values in any of the serotype columns (NmA, NmC, NmW).
```
select *
from [meningitis_dataset]
where report_outcome = 'Not Confirmed' 
AND (Nma = 1 or NmC = 1 or NmW = 1)
```

#### 13. Identify trends in confirmed meningitis cases over the years using a rolling average.
```
with cte1 as (
    select count(case when meningitis = 1 then 1 end) as confirmedcases, report_year
    from [meningitis_dataset]
    group by report_year)
select *, avg(confirmedcases) over (order by report_year ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) as avgcases
from cte1
order by avgcases desc
```

#### 14. Calculate the cumulative sum of confirmed cases for meningitis per state, sorted by year.
```
with cte1 AS (
    select count(case when meningitis = 1 then 1 end) as confirmedcases, state, report_year
    from [meningitis_dataset]
    group by state, report_year)
select *, sum(confirmedcases) over (partition by state order by report_year ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) as cumulativesum
from cte1
```

#### 15. Build a report showing the average age of patients for each disease in urban vs. rural settlements.
```
select disease, settlement, avg(age)
from [meningitis_dataset]
group by disease, settlement
order by disease
```

#### 16. Simulate a hierarchy of disease severity using confirmed and unconfirmed columns.
```
with cte1 AS (
    select disease, sum(confirmed) as totalconfirmed, sum(unconfirmed) as totalnotconfirmed
    from [meningitis_dataset]
    group by disease)
select *, row_number() over (order by totalconfirmed desc) as hierarchy_of_disease,
    case when row_number() over (order by totalconfirmed desc) BETWEEN 1 AND 3 then 'High severity'
         when row_number() over (order by totalconfirmed desc) BETWEEN 4 AND 7 then 'Medium severity'
         else 'Low severity' end as comments
from cte1
```

#### 17. Identify rows with inconsistent data, such as confirmed = 1 and report_outcome = "not confirmed".
```
select *
from [meningitis_dataset]
where confirmed = 1 and report_outcome = 'not confirmed'
```

#### 18.  Rewrite a query calculating total confirmed cases grouped by state and year to optimize performance.
```
select state, report_year, count(case when confirmed = 1 then 1 end) as sumcases
from [meningitis_dataset]
group by state, report_year
order by report_year asc;
```

