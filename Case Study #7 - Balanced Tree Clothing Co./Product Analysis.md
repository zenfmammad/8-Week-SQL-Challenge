# 🏔️ Case Study #7 - Balanced Tree Clothing Co.
## Product Analysis

#### 1️⃣ What are the top 3 products by total revenue before discount?
```sql
SELECT 
	product_name,
	sum(qty*s.price) as revenue_before_discount
FROM balanced_tree.sales s
LEFT JOIN balanced_tree.product_details pd
ON s.prod_id=pd.product_id
GROUP BY product_name
ORDER BY revenue_before_discount DESC
LIMIT 3;
```
<img width="1530" height="284" alt="image" src="https://github.com/user-attachments/assets/557cd345-1ef3-4044-8268-03351d5567ff" />


#### 2️⃣ What is the total quantity, revenue and discount for each segment?
```sql
SELECT 
	segment_id,
    sum(qty) as total_quantity,
	sum(qty*price) - sum(qty*price*discount/100) as total_revenue,
    sum(qty*price*discount/100) as total_discount
FROM balanced_tree.sales s
LEFT JOIN balanced_tree.product_details pd
ON s.prod_id=pd.product_id
GROUP BY segment_id;
```
<img width="1791" height="351" alt="image" src="https://github.com/user-attachments/assets/7ff7386e-21d0-481f-afcc-b05b168d3e3f" />

#### 3️⃣ What is the top selling product for each segment?
```sql
WITH rank as (
SELECT 
   	segment_id,
    product_name,
    sum(qty) as total_quantity,
    RANK() OVER (PARTITION BY segment_id ORDER BY sum(qty) DESC) as rank  
FROM balanced_tree.sales s
LEFT JOIN balanced_tree.product_details pd
ON s.prod_id=pd.product_id
GROUP BY segment_id, product_name )

SELECT 
	segment_id,
	product_name,
  total_quantity
FROM rank
WHERE rank=1;
```
<img width="1851" height="350" alt="image" src="https://github.com/user-attachments/assets/e4eaf6c3-db34-4598-8fa7-07cd4e37fb6d" />


#### 4️⃣ What is the total quantity, revenue and discount for each category?
```sql
SELECT 
	category_id,
  sum(qty) as total_quantity,
	sum(qty*s.price) - sum(qty*s.price*discount/100) as total_revenue,
  sum(qty*s.price*discount/100) as total_discount
FROM balanced_tree.sales s
LEFT JOIN balanced_tree.product_details pd
ON s.prod_id=pd.product_id
GROUP BY category_id;
```
<img width="1562" height="210" alt="image" src="https://github.com/user-attachments/assets/e26fbf0f-c4dc-4373-844c-1e227a1a6c81" />


#### 5️⃣ What is the top selling product for each category?
```sql
WITH rank as (
SELECT 
	  category_id,
    product_name,
    sum(qty) as total_quantity,
    RANK() OVER (PARTITION BY category_id ORDER BY sum(qty) DESC) as rank
FROM balanced_tree.sales s
LEFT JOIN balanced_tree.product_details pd
ON s.prod_id=pd.product_id
GROUP BY category_id, product_name )

SELECT 
	category_id,
	product_name,
  total_quantity
FROM rank
WHERE rank=1;
```
<img width="1560" height="210" alt="image" src="https://github.com/user-attachments/assets/25b6bf90-3dad-4bee-97b1-8e4c5dc9cefb" />

#### 6️⃣ What is the percentage split of revenue by product for each segment?
```sql
WITH revenue_by_product as (
SELECT 
    segment_id,
	product_name,
	sum(qty*s.price) - sum(qty*s.price*discount/100) as total_revenue
FROM balanced_tree.sales s
LEFT JOIN balanced_tree.product_details pd
ON s.prod_id=pd.product_id
GROUP BY segment_id, product_name)

SELECT
	segment_id,
	product_name,
    ROUND(100.0 * SUM(total_revenue) / SUM(SUM(total_revenue)) OVER (), 2) AS percentage_split
FROM revenue_by_product
GROUP BY segment_id, product_name;
```

#### 7️⃣ What is the percentage split of revenue by segment for each category?
```sql
WITH revenue_by_product as (
SELECT 
    category_id,
	segment_id,
	sum(qty*s.price) - sum(qty*s.price*discount/100) as total_revenue
FROM balanced_tree.sales s
LEFT JOIN balanced_tree.product_details pd
ON s.prod_id=pd.product_id
GROUP BY category_id, segment_id)

SELECT
	category_id,
	segment_id,
    ROUND(100.0 * SUM(total_revenue) / SUM(SUM(total_revenue)) OVER (), 2) AS percentage_split
FROM revenue_by_product
GROUP BY category_id, segment_id;
```

#### 8️⃣ What is the percentage split of total revenue by category?
```
WITH revenue_by_product as (
SELECT 
    category_id,
	sum(qty*s.price) - sum(qty*s.price*discount/100) as total_revenue
FROM balanced_tree.sales s
LEFT JOIN balanced_tree.product_details pd
ON s.prod_id=pd.product_id
GROUP BY category_id)

SELECT
	category_id,
    ROUND(100.0 * SUM(total_revenue) / SUM(SUM(total_revenue)) OVER (), 2) AS percentage_split
FROM revenue_by_product
GROUP BY category_id;
```
<img width="1296" height="134" alt="image" src="https://github.com/user-attachments/assets/0e2ed0d0-8648-4330-a99a-8460bc7d6b8b" />


#### 9️⃣ What is the total transaction “penetration” for each product? (hint: penetration = number of transactions where at least 1 quantity of a product was purchased divided by total number of transactions)

#### 🔟 What is the most common combination of at least 1 quantity of any 3 products in a 1 single transaction?
