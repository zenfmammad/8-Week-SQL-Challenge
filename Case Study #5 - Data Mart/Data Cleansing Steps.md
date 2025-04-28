# 🛒 Case Study #5 - Data Mart
## 🧹 Data Cleansing Steps

#### In a single query, perform the following operations and generate a new table in the data_mart schema named clean_weekly_sales:
- Convert the week_date to a DATE format
- Add a week_number as the second column for each week_date value, for example any value from the 1st of January to 7th of January will be 1, 8th to 14th will be 2 etc
- Add a month_number with the calendar month for each week_date value as the 3rd column
- Add a calendar_year column as the 4th column containing either 2018, 2019 or 2020 values
- Add a new column called age_band after the original segment column using the following mapping on the number inside the segment value

```sql
CREATE TABLE data_mart.clean_weekly_sales AS
 SELECT 
		TO_DATE(week_date, 'DD/MM/YY') as week_date,
    EXTRACT(WEEK FROM TO_DATE(week_date, 'DD/MM/YY')) as week_number,
    EXTRACT(MONTH FROM TO_DATE(week_date, 'DD/MM/YY')) as month_number,
    EXTRACT(YEAR FROM TO_DATE(week_date, 'DD/MM/YY')) as calendar_year,
    region,
    platform,
    segment,
    (CASE WHEN RIGHT(segment,1)='1' THEN 'Young Adults'
         	  WHEN RIGHT(segment,1)='2' THEN 'Middle Aged'
         	  WHEN RIGHT(segment,1) IN ('3','4') THEN 'Retirees'
      END) as age_band,
    (CASE WHEN LEFT(segment,1)='C' THEN 'Couples'
         	WHEN LEFT(segment,1)='F' THEN 'Families'
    END) as demographic,
    customer_type,
    transactions,
    sales,
    ROUND(sales/transactions,2) as avg_transactions
  FROM weekly_sales
  ```

![Screenshot 2025-04-28 201408](https://github.com/user-attachments/assets/e5872d7e-11fb-4ff4-a232-ff960d2075bc)
