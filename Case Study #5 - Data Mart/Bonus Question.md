# Case Study #5 - Data Mart

## 4.Bonus Questions

#### Which areas of the business have the highest negative impact in sales metrics performance in 2020 for the 12 week before and after period?

▪️region <br>
▪️platform <br>
▪️age_band <br>
▪️demographic <br>
▪️customer_type

#### by Region
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
    END) AS after_sales,
      region
  FROM clean_weekly_sales, base_date
  WHERE EXTRACT(YEAR FROM week_date)=2020
  GROUP BY region
)


SELECT
    region,
  before_sales,
  after_sales,
  after_sales - before_sales AS growth_value,
  ROUND(100*(after_sales - before_sales) / NULLIF(before_sales, 0), 2) AS growth_percentage
FROM before_after;
```
