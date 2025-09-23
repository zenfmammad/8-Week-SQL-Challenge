# 🏔️ Case Study #7 - Balanced Tree Clothing Co.
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
```sql
SELECT 
	product_name,
	sum(qty*s.price) as revenue_before_discount
FROM balanced_tree.sales s
LEFT JOIN balanced_tree.product_details pd
ON s.prod_id=pd.product_id
GROUP BY product_name;
```
<img width="1860" height="602" alt="image" src="https://github.com/user-attachments/assets/4bbc2392-adb7-412e-94c4-38f82c26cc57" />

#### 3️⃣ What was the total discount amount for all products?
```sql
SELECT 
	pd.product_name,
	sum(s.qty*s.price*s.discount/100) as total_discount
FROM balanced_tree.sales s
LEFT JOIN balanced_tree.product_details pd
ON s.prod_id=pd.product_id
GROUP BY product_name
ORDER BY total_discount DESC;
```
<img width="1868" height="736" alt="image" src="https://github.com/user-attachments/assets/4ab27d49-bd3d-4e7d-af95-0d869875148e" />

