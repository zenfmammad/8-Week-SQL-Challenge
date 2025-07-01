# Case Study #5 - Data Mart
## 2. Data Exploration

#### 1️⃣ What day of the week is used for each week_date value?
```sql
SELECT 
    DISTINCT TO_CHAR(week_date, 'FMDay') as week_name
FROM clean_weekly_sales;
```

#### 2️⃣ What range of week numbers are missing from the dataset?
```sql

```

#### 3️⃣ How many total transactions were there for each year in the dataset?
```sql
SELECT 
	calendar_year,
    SUM(transactions) as transaction_count

FROM clean_weekly_sales
GROUP BY calendar_year
ORDER BY calendar_year ASC;
```
#### 4️⃣ What is the total sales for each region for each month?
```sql
SELECT 
	region,
    SUM(transactions) as transaction_count
FROM clean_weekly_sales
GROUP BY region;
```

#### 5️⃣ What is the total count of transactions for each platform
```sql
SELECT 
	platform,
    SUM(transactions) as transaction_count
FROM clean_weekly_sales
GROUP BY platform;
```

#### 6️⃣ What is the percentage of sales for Retail vs Shopify for each month?
```sql
WITH shopify_retail_sales as (
  SELECT 
	platform,
    month_number,
    SUM(sales) as shopify_retail_sales
FROM clean_weekly_sales
WHERE platform IN ('Shopify', 'Retail')
GROUP BY platform, month_number)

SELECT 
	platform,
	month_number,
	ROUND(100*SUM(shopify_retail_sales)/ (SELECT sum(sales) FROM clean_weekly_sales), 2)
    
FROM shopify_retail_sales
GROUP BY platform, month_number;
```

#### 7️⃣ What is the percentage of sales by demographic for each year in the dataset?
```sql
WITH all_sales AS (
  SELECT SUM(sales) as all_sales
  FROM clean_weekly_sales),

by_demographic_sales as (
  SELECT 
	demographic,
    calendar_year,
    SUM(sales) as by_demographic_sales 
FROM clean_weekly_sales
GROUP BY demographic, calendar_year)

SELECT 
	demographic
    calendar_year,
    ROUND(100*SUM(by_demographic_sales) /(SELECT sum(all_sales) FROM all_sales), 2) as percentage
FROM by_demographic_sales
GROUP BY demographic, calendar_year;
```

#### 8️⃣ Which age_band and demographic values contribute the most to Retail sales?
```sql
--Age Band
SELECT age_band,
       sum(sales) as sales
FROM clean_weekly_sales
WHERE platform='Retail'
GROUP BY age_band
ORDER BY sales desc
LIMIT 1;

--Demographic
SELECT demographic,
       sum(sales) as sales
FROM clean_weekly_sales
WHERE platform='Retail'
GROUP BY demographic
ORDER BY sales desc
LIMIT 1;

--Together
SELECT  age_band,
        demographic,
       sum(sales) as sales
FROM clean_weekly_sales
WHERE platform='Retail'
GROUP BY age_band, demographic
ORDER BY sales desc
LIMIT 1;
```
