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
```sql
WITH plan_steps AS (
  SELECT 
    customer_id,
    plan_name,
    start_date,
    LEAD(plan_name) OVER (PARTITION BY customer_id ORDER BY start_date) AS next_plan
  FROM subscriptions s
  LEFT JOIN plans p ON s.plan_id = p.plan_id
)

SELECT 
  COUNT(DISTINCT customer_id) as churn_customer_count,
  100* COUNT(DISTINCT customer_id)/(SELECT COUNT(DISTINCT customer_id) FROM subscriptions) as churn_percentage
FROM plan_steps
WHERE plan_name = 'trial' 
  AND next_plan = 'churn';
```

#### 6️⃣ What is the number and percentage of customer plans after their initial free trial?

#### 7️⃣ What is the customer count and percentage breakdown of all 5 plan_name values at 2020-12-31?
```sql

WITH next_plans as(
  SELECT plan_name,
       customer_id,
       LEAD(plan_name) OVER(PARTITION BY customer_id ORDER BY start_date) as next_plan
FROM subscriptions s
LEFT JOIN plans p
ON s.plan_id=p.plan_id
WHERE start_date<='2020-12-31'
GROUP BY plan_name, customer_Id, start_date)

SELECT  
	plan_name,
	COUNT(DISTINCT customer_id) as customer_count,
    100*COUNT(DISTINCT customer_id)/ (SELECT COUNT(DISTINCT customer_id) FROM subscriptions) as percentage
FROM next_plans 
WHERE next_plan is NULL
GROUP BY plan_name
ORDER BY customer_count ASC;
```

#### 8️⃣ How many customers have upgraded to an annual plan in 2020?
```sql
WITH plan_steps AS (
  SELECT 
    customer_id,
    plan_name,
    start_date,
    LEAD(plan_name) OVER (PARTITION BY customer_id ORDER BY start_date) AS next_plan
  FROM subscriptions s
  LEFT JOIN plans p ON s.plan_id = p.plan_id
  WHERE EXTRACT(YEAR FROM start_date)=2020
)

SELECT 
  COUNT(DISTINCT customer_id) as customer_count
FROM plan_steps
WHERE next_plan = 'pro annual';
```

#### 9️⃣ How many days on average does it take for a customer to an annual plan from the day they join Foodie-Fi?
```sql
  WITH dates as(
    SELECT 
    customer_id,
    MIN(start_date) AS first_start_date,
    MIN(CASE WHEN plan_name = 'pro annual' THEN start_date END) AS annual_plan_start_date
  FROM subscriptions s
  LEFT JOIN plans p ON s.plan_id = p.plan_id
  GROUP BY customer_id
ORDER BY customer_id)

SELECT 
	ROUND(AVG(annual_plan_start_date - first_start_date), 2) as days
FROM dates;
```
#### 🔟 Can you further breakdown this average value into 30 day periods (i.e. 0-30 days, 31-60 days etc)
```sql
WITH dates AS (
  SELECT 
    customer_id,
    MIN(start_date) AS first_start_date,
    MIN(CASE WHEN plan_name = 'pro annual' THEN start_date END) AS annual_plan_start_date
  FROM subscriptions s
  LEFT JOIN plans p ON s.plan_id = p.plan_id
  GROUP BY customer_id
),
datediff AS (
  SELECT 
    customer_id,
    ROUND(AVG(annual_plan_start_date - first_start_date), 1) AS days
  FROM dates
  GROUP BY customer_id
)

SELECT 
  CASE 
    WHEN days BETWEEN 0 AND 30 THEN '0-30'
    WHEN days BETWEEN 31 AND 60 THEN '31-60'
    WHEN days BETWEEN 61 AND 90 THEN '61-90'
    WHEN days BETWEEN 91 AND 120 THEN '91-120'
    ELSE '90+'
  END AS category,
  COUNT(customer_id)
FROM datediff
GROUP BY category;
```

#### 🔟+1️⃣ How many customers downgraded from a pro monthly to a basic monthly plan in 2020?
```sql
WITH plan_steps AS (
  SELECT 
    customer_id,
    plan_name,
    start_date,
    LEAD(plan_name) OVER (PARTITION BY customer_id ORDER BY start_date) AS next_plan
  FROM subscriptions s
  LEFT JOIN plans p ON s.plan_id = p.plan_id
  WHERE EXTRACT(YEAR FROM start_date)=2020
)

SELECT 
  COUNT(DISTINCT customer_id) as customer_count
FROM plan_steps
WHERE plan_name='pro monthly' AND next_plan = 'basic monthly';
```
