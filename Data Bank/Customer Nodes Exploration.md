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

#### 5️⃣ What is the median, 80th and 95th percentile for this same reallocation days metric for each region?
