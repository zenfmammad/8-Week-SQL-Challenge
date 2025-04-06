# Case Study #1 - Danny's Diner
<img src="https://github.com/user-attachments/assets/5c8e9d58-2e46-4acb-a82e-8d573c935019" alt="Danny's Diner" width="400"/>

## Problem Statement
Danny wants to use the data to answer a few simple questions about his customers, especially about their visiting patterns, how much money they’ve spent and also which menu items are their favourite. 

## Entity Relationship Diagram
<img src="https://github.com/user-attachments/assets/4078b633-6e09-4922-994f-0a7ab9de3190" alt="Dany's Diner" width="700"/>

**Table 1: sales**
<br>
The sales table captures all customer_id level purchases with an corresponding order_date and product_id information for when and what menu items were ordered.

**Table 2: menu**
<br>
The menu table maps the product_id to the actual product_name and price of each menu item.

**Table 3: members**
<br>
The final members table captures the join_date when a customer_id joined the beta version of the Danny’s Diner loyalty program.

## Business Questions and Solutions

**1. What is the total amount each customer spent at the restaurant?**

```sql
SELECT 
     customer_id,
     sum(price) as total_amount_spent
FROM sales s
LEFT JOIN menu m ON s.product_id= m.product_id
GROUP BY customer_id
ORDER BY customer_id ASC;
```

**2. How many days has each customer visited the restaurant?**

```sql
SELECT 
     customer_id,
     COUNT(DISTINCT order_date) as visit_count
FROM sales
GROUP BY customer_id
ORDER BY customer_id ASC;
```

**3. What was the first item from the menu purchased by each customer?**

```sql
WITH ranked_orders AS (
  SELECT 
    customer_id,
    product_name,
    order_date,
    DENSE_RANK() OVER(PARTITION BY customer_id ORDER BY order_date) AS rank
  FROM sales s
  LEFT JOIN menu m ON s.product_id = m.product_id
)

SELECT 
  customer_id,
  product_name
FROM ranked_orders
WHERE rank = 1
GROUP BY customer_id, product_name
ORDER BY customer_id ASC;
```

**4. What is the most purchased item on the menu and how many times was it purchased by all customers?**
```sql
SELECT product_name,
       COUNT(order_date) as purchased_count
                        
FROM sales s
LEFT JOIN menu m ON s.product_id= m.product_id
GROUP BY product_name
ORDER BY COUNT(order_date) DESC
LIMIT 1;
```

**5. Which item was the most popular for each customer?**
```sql
WITH ranked_purchased_count as
(SELECT customer_id,
	   product_name,
       COUNT(order_date) as purchased_count,
       DENSE_RANK() OVER(PARTITION BY customer_id ORDER BY COUNT(order_date) DESC) as rank
                        
FROM sales s
LEFT JOIN menu m ON s.product_id= m.product_id
GROUP BY product_name, customer_id
ORDER BY customer_id )

SELECT customer_id,
	   product_name,
       purchased_count
FROM ranked_purchased_count
WHERE rank=1
GROUP BY customer_id, product_name, purchased_count;
```

**6. Which item was purchased first by the customer after they became a member?**
```sql
WITH ranked_order_dates as 
(SELECT  s.customer_id,
 		product_name,
        DENSE_RANK() OVER(PARTITION BY s.customer_id ORDER BY order_date) as rank
FROM sales s
LEFT JOIN members mem ON s.customer_id= mem.customer_id
 LEFT JOIN menu m ON s.product_id= m.product_id
WHERE order_date>join_date ) 

SELECT customer_id,
	   product_name
FROM ranked_order_dates
WHERE rank=1;
```

**7. Which item was purchased just before the customer became a member?**
```sql
WITH ranked_order_dates as 
(SELECT  s.customer_id,
 		product_name,
        ROW_NUMBER() OVER(PARTITION BY s.customer_id ORDER BY order_date) as rank
FROM sales s
LEFT JOIN members mem ON s.customer_id= mem.customer_id
LEFT JOIN menu m ON s.product_id= m.product_id
WHERE order_date join_date)          
SELECT customer_id,
	   product_name
FROM ranked_order_dates
WHERE rank=1;
```


**8.What is the total items and amount spent for each member before they became a member?**
```sql
SELECT  s.customer_id,
        SUM(price)
FROM sales s
LEFT JOIN members mem ON s.customer_id= mem.customer_id
LEFT JOIN menu m ON s.product_id= m.product_id
WHERE order_date join_date 
GROUP BY s.customer_id
ORDER BY s.customer_id ASC
 ```

**9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?**
```sql
SELECT  s.customer_id,
        SUM(CASE 
             WHEN product_name='sushi' THEN price*20
         ELSE price*10 END) as points
FROM sales s
LEFT JOIN menu m ON s.product_id= m.product_id
GROUP BY s.customer_id
ORDER BY s.customer_id ASC
```

**10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?**
```sql
SELECT  s.customer_id,
        SUM(CASE 
             WHEN join_date BETWEEN join_date AND join_date + INTERVAL '7 days' THEN price*20
         ELSE (
           CASE WHEN product_name='sushi' THEN price*20
           ELSE price*10 END)
         END) as points
FROM sales s
LEFT JOIN menu m ON s.product_id= m.product_id
LEFT JOIN members mem ON s.customer_id= mem.customer_id
WHERE join_date IS NOT NULL
GROUP BY s.customer_id
ORDER BY s.customer_id ASC;
```
