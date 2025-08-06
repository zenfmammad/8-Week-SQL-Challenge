# Case Study #7 - Balanced Tree Clothing Co.
## High Level Sales Analysis


#### 1️⃣ What was the total quantity sold for all products?
```sql
SELECT SUM(qty) as total_quantity
FROM  balanced_tree.sales;
```
<img width="858" height="106" alt="image" src="https://github.com/user-attachments/assets/f5fc5ba0-b10e-4e85-8148-e0d3698aeafe" />

For individual products:

```sql
SELECT
	product_name,
	SUM(qty) as total_quantity
FROM balanced_tree.product_details pd
LEFT JOIN balanced_tree.sales s ON pd.product_id= s.prod_id
GROUP BY product_name;
```
<img width="1902" height="594" alt="image" src="https://github.com/user-attachments/assets/d7596119-9b77-4ff0-99c9-d2be1418fd79" />

#### 2️⃣ What is the total generated revenue for all products before discounts?
#### 3️⃣ What was the total discount amount for all products?

