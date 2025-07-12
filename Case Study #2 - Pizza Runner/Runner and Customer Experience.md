# 🍕 Case Study #2 - Pizza Runner
## 🙋‍♀️ Runner and Customer Experience

#### 1️⃣ How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)
```sql
SELECT 
	count(runner_id) as runner_count,
	DATE_TRUNC('WEEK', registration_date) as weeks
FROM runners
GROUP BY weeks
ORDER BY weeks ASC;
```
<img width="1270" height="256" alt="image" src="https://github.com/user-attachments/assets/981b67a2-e35d-46dc-b06d-3dccca646b15" />

#### 2️⃣ What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?
```sql
SELECT runner_id,
	  AVG(EXTRACT(MINUTE FROM pickup_time-order_time)) as avg_prep_time
FROM customer_orders_temp co
LEFT JOIN runner_orders_temp ro ON  co.order_id= ro.order_id
GROUP BY runner_id
ORDER BY runner_id;
```
<img width="1136" height="257" alt="image" src="https://github.com/user-attachments/assets/108ca6b1-c2e8-4557-bd67-7d3e8399452d" />

#### 3️⃣ Is there any relationship between the number of pizzas and how long the order takes to prepare?
```sql
WITH  pizza_count_per_order as (
  SELECT  
  	order_id,
    order_time,
  	COUNT(pizza_id) as pizza_count
  FROM customer_orders_temp
  GROUP BY order_id, order_time)

SELECT 
       pizza_count,
       EXTRACT( MINUTES FROM  AVG(pickup_time- order_time)) as preperation_time_in_minutes
FROM  pizza_count_per_order pc
LEFT JOIN runner_orders_temp ro ON pc.order_id= ro.order_id
GROUP BY pizza_count
ORDER BY pizza_count ASC;
```
<img width="1054" height="266" alt="image" src="https://github.com/user-attachments/assets/49ab45af-5074-47eb-a00c-a3d3c89eb057" />


#### 4️⃣ What was the average distance travelled for each customer?
```sql
SELECT runner_id,
	  AVG(distance) as avg_distance	
FROM customer_orders_temp co
LEFT JOIN runner_orders_temp ro ON  co.order_id= ro.order_id
GROUP BY runner_id
ORDER BY runner_id ASC;
```
<img width="1195" height="255" alt="image" src="https://github.com/user-attachments/assets/b17e7d52-667b-4167-a074-941f96a957a0" />

#### 5️⃣ What was the difference between the longest and shortest delivery times for all orders?
```sql
SELECT 
    MAX(duration)  - MIN(duration) as difference
FROM runner_orders_temp;
```
<img width="990" height="149" alt="image" src="https://github.com/user-attachments/assets/5390a827-acd1-4362-bf90-d5d68dc6671b" />

#### 6️⃣ What was the average speed for each runner for each delivery and do you notice any trend for these values?
```sql
SELECT runner_id,
	   ROUND(AVG(CAST(distance AS NUMERIC) / CAST(duration AS NUMERIC)), 2) || ' km/min' as avg_speed
FROM runner_orders_temp
GROUP BY runner_id
ORDER BY runner_id ASC;
```
<img width="1311" height="248" alt="image" src="https://github.com/user-attachments/assets/7e7e66d4-56a8-4df5-845f-bfa47f1a1241" />

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
<img width="1472" height="251" alt="image" src="https://github.com/user-attachments/assets/8ea7957b-7020-4a2d-a4a1-d75f6dd1b55c" />

