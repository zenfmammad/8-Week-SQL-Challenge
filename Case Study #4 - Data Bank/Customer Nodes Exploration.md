# 📊 Case Study #4 - Data Bank
## 👩‍💼 Customer Nodes Exploration

#### 1️⃣How many unique nodes are there on the Data Bank system?
```sql
SELECT COUNT(DISTINCT node_id) as node_count
FROM customer_nodes;
```
<img width="1592" height="132" alt="image" src="https://github.com/user-attachments/assets/70b27f69-c7f3-42ba-a8d3-29bc4be76522" />

#### 2️⃣ What is the number of nodes per region?
```sql
SELECT 
	region_name,
	COUNT(node_id) as node_count
FROM customer_nodes cn
LEFT JOIN regions r ON cn.region_id=r.region_id
GROUP BY region_name;
```
<img width="1903" height="379" alt="image" src="https://github.com/user-attachments/assets/f5957abb-74b2-424c-918f-709b61b8edf2" />

#### 3️⃣ How many customers are allocated to each region?
```sql
SELECT 
	region_name,
	COUNT(DISTINCT customer_id) as customer_count
FROM customer_nodes cn
LEFT JOIN regions r ON cn.region_id=r.region_id
GROUP BY region_name;
```
<img width="1902" height="382" alt="image" src="https://github.com/user-attachments/assets/665c7bfd-3513-4694-8795-f3796e8fc686" />

#### 4️⃣ How many days on average are customers reallocated to a different node?
```sql
SELECT
  ROUND(AVG(end_date- start_date), 2) AS avg_days_between_reallocation
FROM
  customer_nodes
WHERE end_date <> '9999-12-31';
```
<img width="1601" height="134" alt="image" src="https://github.com/user-attachments/assets/a95d0a92-b51b-46c0-9c77-92a2e1be0bd5" />

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
<img width="1601" height="132" alt="image" src="https://github.com/user-attachments/assets/2bdf011f-cfab-446f-be64-4dc0ce2d68b1" />

