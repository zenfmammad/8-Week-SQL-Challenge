# 🛒 Case Study #5 - Data Mart
## 3. Before & After Analysis

#### 1️⃣ What is the total sales for the 4 weeks before and after 2020-06-15? What is the growth or reduction rate in actual values and percentage of sales?
```sql
WITH base_date AS (
  SELECT DATE '2020-06-15' AS ref_date
),
before_after AS (
  SELECT
    SUM(CASE 
        WHEN week_date >= ref_date - INTERVAL '28 days' 
         AND week_date < ref_date 
        THEN sales 
    END) AS before_sales,
    
    SUM(CASE 
        WHEN week_date >= ref_date 
         AND week_date < ref_date + INTERVAL '28 days' 
        THEN sales 
    END) AS after_sales
  FROM clean_weekly_sales, base_date
)

SELECT
  before_sales,
  after_sales,
  after_sales - before_sales AS growth_value,
  ROUND((after_sales - before_sales) * 100.0 / NULLIF(before_sales, 0), 2) AS growth_percentage 
FROM before_after;
```
![image](https://github.com/user-attachments/assets/db1b3eb0-8eab-47f8-8c2d-5ea13ab06e42)


#### 2️⃣ What about the entire 12 weeks before and after?
```sql
WITH base_date AS (
  SELECT DATE '2020-06-15' AS ref_date
),
before_after AS (
  SELECT
    SUM(CASE
        WHEN week_date < ref_date
        THEN sales
    END) AS before_sales,

    SUM(CASE
        WHEN week_date >= ref_date
        THEN sales
    END) AS after_sales
  FROM clean_weekly_sales, base_date
  WHERE EXTRACT(YEAR FROM week_date)=2020
)


SELECT
  before_sales,
  after_sales,
  after_sales - before_sales AS growth_value,
  ROUND(100*(after_sales - before_sales) / NULLIF(before_sales, 0), 2) AS growth_percentage
FROM before_after;
```
![image](https://github.com/user-attachments/assets/3825bdf8-07c6-42e4-8e57-e7814806da5e)

#### 3️⃣ How do the sale metrics for these 2 periods before and after compare with the previous years in 2018 and 2019?
```sql
WITH year_sales AS (
  SELECT 
    calendar_year AS years,
    week_date,
    sales
  FROM clean_weekly_sales
),
before_after AS (
  SELECT
    years,
    SUM(CASE
        WHEN week_date >= DATE(years || '-06-15') - INTERVAL '28 days'
         AND week_date < DATE(years || '-06-15')
        THEN sales
    END) AS before_sales,

    SUM(CASE
        WHEN week_date >= DATE(years || '-06-15')
         AND week_date < DATE(years || '-06-15') + INTERVAL '28 days'
        THEN sales
    END) AS after_sales
  FROM year_sales
  GROUP BY years
)

SELECT
  years,
  before_sales,
  after_sales,
  after_sales - before_sales AS growth_value,
  ROUND((after_sales - before_sales) * 100.0 / NULLIF(before_sales, 0), 2) AS growth_percentage
FROM before_after
ORDER BY years;
```
![image](https://github.com/user-attachments/assets/7974d573-dde1-4079-a519-a59552663d06)



