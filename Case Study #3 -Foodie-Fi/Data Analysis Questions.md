# 🥑 Case Study #3 - Foodie-Fi
## ❓Data Analysis Questions

#### 1️⃣ How many customers has Foodie-Fi ever had?
```sql
SELECT COUNT(DISTINCT customer_id) as customer_count
FROM subscriptions;
```
#### 2️⃣ What is the monthly distribution of trial plan start_date values for our dataset - use the start of the month as the group by value
```sql
SELECT TO_CHAR(start_date, 'MM-YYYY') AS months,
       COUNT(customer_id) as customer_count
FROM subscriptions s
LEFT JOIN plans p ON s.plan_id=s.plan_id
WHERE plan_name='trial'
GROUP BY months
ORDER BY MIN(start_date);
```
#### 3️⃣ What plan start_date values occur after the year 2020 for our dataset? Show the breakdown by count of events for each plan_name
```sql
SELECT
       plan_name,
       COUNT(customer_id) as customer_count
FROM subscriptions s
JOIN plans p ON s.plan_id=p.plan_id
WHERE start_date>='2021-01-01'
GROUP BY plan_name;
```
#### 4️⃣ What is the customer count and percentage of customers who have churned rounded to 1 decimal place?
```sql
WITH customer_counts AS (
  SELECT COUNT(CASE WHEN plan_name='churn' THEN customer_id END) as churn_customer_count,
COUNT(DISTINCT customer_id) as all_customer_count
FROM subscriptions s
LEFT JOIN plans p ON s.plan_id=p.plan_id)

SELECT churn_customer_count,
        ROUND(100.0 * churn_customer_count / all_customer_count ,1) AS churn_percentage
FROM customer_counts;
```

#### 5️⃣ How many customers have churned straight after their initial free trial - what percentage is this rounded to the nearest whole number?
