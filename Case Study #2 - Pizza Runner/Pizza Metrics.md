# 🍕 Case Study #2 - Pizza Runner
## A- Pizza Metrics

#### 1️⃣ How many pizzas were ordered?
 ```sql
 SELECT COUNT(order_id) as ordered_pizza_count
 FROM customer_orders;
```

#### 2️⃣ How many unique customer orders were made?
```sql
SELECT COUNT(DISTINCT customer_id) as unique_customer_count
FROM customer_orders;
```
#### 3️⃣ How many successful orders were delivered by each runner?
```sql
SELECT  
		r.runner_id,
		COUNT(order_id) as succesful_delivery_count
FROM runners r
LEFT JOIN runner_orders ro ON r.runner_id= ro.runner_id
WHERE pickup_time IS NOT NULL
GROUP BY r.runner_id
ORDER BY r.runner_id ASC;
```
#### 4️⃣ How many of each type of pizza was delivered?
```sql

SELECT pn.pizza_name,
	   COUNT(order_id) as order_count
FROM pizza_names pn
LEFT JOIN customer_orders co ON  pn.pizza_id= co.pizza_id
GROUP BY pn.pizza_name
ORDER BY pn.pizza_name;
```
#### 5️⃣ How many Vegetarian and Meatlovers were ordered by each customer?
```sql
SELECT co.customer_id,
       pn.pizza_name,
	   COUNT(order_id) as order_count
FROM customer_orders co
LEFT JOIN pizza_names pn ON  pn.pizza_id= co.pizza_id
GROUP BY pn.pizza_name, customer_id
ORDER BY customer_id;
```

OR 

```sql
SELECT co.customer_id,
       COALESCE(CASE WHEN pn.pizza_name='Vegetarian' THEN COUNT(order_id) END,0) as vegetarian_order_count,
       COALESCE(CASE WHEN pn.pizza_name='Meatlovers' THEN COUNT(order_id) END,0) as meatlovers_order_count
FROM customer_orders co
LEFT JOIN pizza_names pn ON  pn.pizza_id= co.pizza_id
GROUP BY pn.pizza_name, customer_id
ORDER BY customer_id;
```

#### 6️⃣ What was the maximum number of pizzas delivered in a single order?
```sql
SELECT  
       COUNT(pizza_id) as pizza_count
FROM customer_orders
GROUP BY order_id
ORDER BY pizza_count DESC
LIMIT 1;
```
#### 7️⃣ For each customer, how many delivered pizzas had at least 1 change and how many had no changes?
```sql
 SELECT DISTINCT customer_id,
 		SUM(CASE WHEN exclusions IS NULL OR exclusions='' OR LOWER(TRIM(exclusions))= 'null' THEN 1 ELSE 0 END) as no_change_order_count,
        SUM(CASE WHEN exclusions IS NOT NULL and exclusions<>'' AND LOWER(TRIM(exclusions)) <> 'null' THEN 1 ELSE 0 END) as at_least_one_change_order_count
FROM customer_orders
GROUP BY customer_id
ORDER BY customer_id ASC;
```
#### 8️⃣ How many pizzas were delivered that had both exclusions and extras?
```sql
SELECT COUNT(order_id) as order_count_with_extras_and_exclusions
FROM customer_orders
WHERE exclusions IS NOT NULL 
             AND exclusions <> '' 
             AND LOWER(TRIM(exclusions)) <> 'null' AND
             extras IS NOT NULL 
             AND extras <> '' 
             AND LOWER(TRIM(extras)) <> 'null' ;
```
#### 9️⃣ What was the total volume of pizzas ordered for each hour of the day?
```sql
 SELECT EXTRACT(HOUR FROM order_time) as hours,
        COUNT(order_id) as order_count
 FROM customer_orders
 GROUP BY hours
 ORDER BY hours ASC;
```
#### 🔟 What was the volume of orders for each day of the week?
```sql
SELECT 
        EXTRACT(DOW FROM order_time) weekdays,
        COUNT(order_id) as order_count
 FROM customer_orders
 GROUP BY EXTRACT(DOW FROM order_time)
 ORDER BY EXTRACT(DOW FROM order_time);
```
