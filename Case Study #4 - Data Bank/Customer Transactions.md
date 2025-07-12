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
<img width="1597" height="255" alt="image" src="https://github.com/user-attachments/assets/65dc9fc5-864b-4a7f-a919-f117dac7a549" />

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
```sql

WITH transaction_counts as (
SELECT
 TO_CHAR(txn_date, 'YYYY-MM') AS year_month,
  customer_id,
  SUM(CASE WHEN txn_type='deposit' THEN 1 ELSE 0 END) as  deposit_count,
  SUM(CASE WHEN txn_type='withdrawal' THEN 1 ELSE 0 END) as  withdrawal_count,
  SUM(CASE WHEN txn_type='purchase' THEN 1 ELSE 0 END) as purchase_count
FROM customer_transactions
GROUP BY year_month, customer_id)

SELECT year_month,
       COUNT(distinct customer_id) as customer_count
FROM transaction_counts
WHERE deposit_count>1 AND (purchase_count >= 1 OR withdrawal_count >= 1)
GROUP BY year_month;
```
<img width="1907" height="310" alt="image" src="https://github.com/user-attachments/assets/5d311e86-7124-40bd-b7db-078f4e9f35ec" />

#### 4️⃣ What is the closing balance for each customer at the end of the month?
#### 5️⃣ What is the percentage of customers who increase their closing balance by more than 5%?
