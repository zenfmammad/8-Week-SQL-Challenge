# Case Study #2 - Pizza Runner
## B- Runner and Customer Experience

#### 1️⃣ How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)
        
#### 2️⃣ What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?
```sql
SELECT runner_id,
	  AVG(EXTRACT(MINUTE FROM pickup_time-order_time)) as avg_prep_time
FROM customer_orders_temp co
LEFT JOIN runner_orders_temp ro ON  co.order_id= ro.order_id
GROUP BY runner_id
ORDER BY runner_id;
```
#### 3️⃣ Is there any relationship between the number of pizzas and how long the order takes to prepare?
#### 4️⃣ What was the average distance travelled for each customer?
```sql
SELECT runner_id,
	  AVG(distance) as avg_distance	
FROM customer_orders_temp co
LEFT JOIN runner_orders_temp ro ON  co.order_id= ro.order_id
GROUP BY runner_id
ORDER BY runner_id ASC;
```
#### 5️⃣ What was the difference between the longest and shortest delivery times for all orders?
#### 6️⃣ What was the average speed for each runner for each delivery and do you notice any trend for these values?
```sql
SELECT runner_id,
	   ROUND(AVG(CAST(distance AS NUMERIC) / CAST(duration AS NUMERIC)), 2) || ' km/min' as avg_speed
FROM runner_orders_temp
GROUP BY runner_id
ORDER BY runner_id ASC;
```
#### 7️⃣ What is the successful delivery percentage for each runner?  
```sql
WITH runner_orders as 
(SELECT r.runner_id,
	   SUM(CASE WHEN cancellation IS NULL THEN 1 ELSE 0 END ) as succesful_order_count,
       COUNT(order_id) as all_order_count
FROM runners r
RIGHT JOIN runner_orders_temp ro ON r.runner_id= ro.runner_id
GROUP BY r.runner_id
ORDER BY r.runner_id ASC)

SELECT runner_id,
       ROUND(succesful_order_count:: NUMERIC/all_order_count *100, 1) || '%' as percentage
FROM runner_orders;
```
