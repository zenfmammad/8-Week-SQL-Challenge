# 🛒 Case Study #5 - Data Mart
## 🧹 Data Cleansing Steps

#### In a single query, perform the following operations and generate a new table in the data_mart schema named clean_weekly_sales:
- Convert the week_date to a DATE format
- Add a week_number as the second column for each week_date value, for example any value from the 1st of January to 7th of January will be 1, 8th to 14th will be 2 etc
- Add a month_number with the calendar month for each week_date value as the 3rd column
- Add a calendar_year column as the 4th column containing either 2018, 2019 or 2020 values
- Add a new column called age_band after the original segment column using the following mapping on the number inside the segment value: 1: Young Adults, 2: Middle Aged, 3 or 4:	Retirees
- Add a new demographic column using the following mapping for the first letter in the segment values: C: Couples, F: Families
- Ensure all null string values with an "unknown" string value in the original segment column as well as the new age_band and demographic columns: 
- Generate a new avg_transaction column as the sales value divided by transactions rounded to 2 decimal places for each record

```sql
CREATE TABLE data_mart.clean_weekly_sales AS
 SELECT 
		TO_DATE(week_date, 'DD/MM/YY') as week_date,
    EXTRACT(WEEK FROM TO_DATE(week_date, 'DD/MM/YY')) as week_number,
    EXTRACT(MONTH FROM TO_DATE(week_date, 'DD/MM/YY')) as month_number,
    EXTRACT(YEAR FROM TO_DATE(week_date, 'DD/MM/YY')) as calendar_year,
    region,
    platform,
    (CASE WHEN segment IS NULL or segment= 'null' 
     	THEN 'Unknown'
     ELSE segment END) as segment,
    (CASE WHEN RIGHT(segment,1)='1' THEN 'Young Adults'
         	  WHEN RIGHT(segment,1)='2' THEN 'Middle Aged'
         	  WHEN RIGHT(segment,1) IN ('3','4') THEN 'Retirees'
     		 WHEN segment IS NULL or segment= 'null' THEN 'Unknown'
      END) as age_band,
    (CASE WHEN LEFT(segment,1)='C' THEN 'Couples'
         	WHEN LEFT(segment,1)='F' THEN 'Families'
     		WHEN segment IS NULL or segment= 'null' THEN 'Unknown'
    END) as demographic,
    customer_type,
    transactions,
    sales,
    ROUND(sales/transactions,2) as avg_transactions
  FROM weekly_sales;
  ```

![Screenshot 2025-05-03 202814](https://github.com/user-attachments/assets/fd61bc2a-a18f-4d10-8323-90728bfd5681)
