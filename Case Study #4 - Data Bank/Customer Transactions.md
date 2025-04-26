# 📊 Case Study #4 - Data Bank
## 💳 B.Customer Transactions

#### 1️⃣ What is the unique count and total amount for each transaction type?
```sql
SELECT 
	txn_type,
    COUNT(DISTINCT customer_id) as unique_user_count,
	SUM(txn_amount) as amount
FROM customer_transactions
GROUP BY txn_type;
```
#### 2️⃣ What is the average total historical deposit counts and amounts for all customers?
```sql
AVG OR SUM??
WITH deposit as(
  SELECT 
    customer_id,
    COUNT(customer_id) as transaction_count,
	SUM(txn_amount) as amount
FROM customer_transactions
WHERE txn_type='deposit'
GROUP BY customer_id)

SELECT 
	ROUND(AVG(transaction_count),2) avg_transaction_count,
	ROUND(AVG(amount), 2) as avg_amount
FROM deposit;
```
#### 3️⃣ For each month - how many Data Bank customers make more than 1 deposit and either 1 purchase or 1 withdrawal in a single month?
#### 4️⃣ What is the closing balance for each customer at the end of the month?
#### 5️⃣ What is the percentage of customers who increase their closing balance by more than 5%?
