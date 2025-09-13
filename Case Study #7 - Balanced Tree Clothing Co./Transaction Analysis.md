# Case Study #7 - Balanced Tree Clothing Co.
## 💳 Transaction Analysis

#### 1️⃣ How many unique transactions were there?
```sql
SELECT COUNT(DISTINCT txn_id) as unique_transaction_count
FROM balanced_tree.sales;
```
<img width="875" height="156" alt="image" src="https://github.com/user-attachments/assets/21e8ddf1-04e2-48bb-877a-35e189a562f1" />

#### 2️⃣ What is the average unique products purchased in each transaction?
```sql
WITH distinct_product_count_per_txn as (
SELECT txn_id,
  	   count(distinct prod_id) as distinct_product_count_per_txn
FROM balanced_tree.sales
GROUP BY txn_id)

SELECT ROUND (AVG(distinct_product_count_per_txn), 2)
FROM distinct_product_count_per_txn;
```
<img width="749" height="159" alt="image" src="https://github.com/user-attachments/assets/771e53ab-0e3a-4a9f-b268-71508d4d7b2c" />

#### 3️⃣ What are the 25th, 50th and 75th percentile values for the revenue per transaction?
```sql
WITH revenue as (
SELECT
	txn_id,
	sum(qty*price) - sum(qty*price*discount/100) as revenue
FROM balanced_tree.sales
GROUP BY txn_id)

SELECT 
	percentile_cont(0.25) WITHIN GROUP(ORDER BY revenue ASC) as twenty_five_percentile,
    percentile_cont(0.5) WITHIN GROUP(ORDER BY revenue ASC) as fifty_percentile,
    percentile_cont(0.75) WITHIN GROUP(ORDER BY revenue ASC) as seventy_five_percentile
FROM revenue;
```
<img width="1543" height="151" alt="image" src="https://github.com/user-attachments/assets/2513aef3-f31e-464d-8f1d-30a3db9bb1ac" />

#### 4️⃣ What is the average discount value per transaction?
```sql
WITH revenue as (
SELECT
	txn_id,
	sum(qty*price*discount/100) as discount_value
FROM balanced_tree.sales
GROUP BY txn_id)

SELECT 
	ROUND(AVG(discount_value), 2) as avg_discount_per_txn
FROM revenue;
```
<img width="762" height="154" alt="image" src="https://github.com/user-attachments/assets/988e1493-c6cf-47b6-8883-ca4897047404" />

#### 5️⃣ What is the percentage split of all transactions for members vs non-members?
```sql
SELECT
	CASE 
    	WHEN member='t' THEN 'Member'
        WHEN member='f' THEN 'Non-member'
    END as member_or_not,
	COUNT(DISTINCT txn_id) as txn_count
    
FROM balanced_tree.sales
GROUP BY member_or_not;
```
<img width="1492" height="219" alt="image" src="https://github.com/user-attachments/assets/98fdce4c-f9a5-467c-b9ce-e9028533312c" />

#### 6️⃣ What is the average revenue for member transactions and non-member transactions?
```sql
WITH member_revenue as (
SELECT
    (qty*price) - (qty*price*discount/100) as revenue,
	CASE 
		WHEN member='t' THEN 'Member'
		WHEN member='f' THEN 'Non-member'
	END as member_or_not
FROM balanced_tree.sales)

SELECT 
	member_or_not,
	ROUND(AVG(revenue),2) as avg_revenue
FROM member_revenue
GROUP BY member_or_not;
```
<img width="1527" height="215" alt="image" src="https://github.com/user-attachments/assets/588ef50e-dbcb-46e0-9a44-733d0fd386cc" />
