# 📊 Case Study #4 - Data Bank
## 👩‍💼 Customer Nodes Exploration

#### 1️⃣How many unique nodes are there on the Data Bank system?
```sql
SELECT COUNT(DISTINCT node_id) as node_count
FROM customer_nodes;
```
#### 2️⃣ What is the number of nodes per region?
```sql
SELECT 
	region_name,
	COUNT(node_id) as node_count
FROM customer_nodes cn
LEFT JOIN regions r ON cn.region_id=r.region_id
GROUP BY region_name;
```

#### 3️⃣ How many customers are allocated to each region?
```sql
SELECT 
	region_name,
	COUNT(DISTINCT customer_id) as customer_count
FROM customer_nodes cn
LEFT JOIN regions r ON cn.region_id=r.region_id
GROUP BY region_name;
```

#### 4️⃣ How many days on average are customers reallocated to a different node?
```sql
SELECT
  ROUND(AVG(end_date- start_date), 2) AS avg_days_between_reallocation
FROM
  customer_nodes
WHERE end_date <> '9999-12-31';
```

#### 5️⃣ What is the median, 80th and 95th percentile for this same reallocation days metric for each region?
```sql
SELECT
  ROUND(AVG(end_date- start_date), 2) AS avg_days_between_reallocation,
  percentile_cont(0.5) WITHIN GROUP (ORDER BY end_date-start_date) AS median,
  percentile_cont(0.8) WITHIN GROUP (ORDER BY end_date-start_date) AS p80,
  percentile_cont(0.95) WITHIN GROUP (ORDER BY end_date-start_date) AS p95
FROM
  customer_nodes
WHERE end_date <> '9999-12-31';
```
