# Case Study #2 - Pizza Runner
## B- Runner and Customer Experience

#### 1️⃣ How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)
        
#### 2️⃣ What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?
```sql
SELECT runner_id,
	  AVG( EXTRACT(MINUTE FROM (CASE 
    WHEN pickup_time<>'null' THEN TO_TIMESTAMP(pickup_time, 'YYYY-MM-DD HH24:MI:SS')
    ELSE NULL
  END))-
       EXTRACT(MINUTE FROM order_time))
FROM customer_orders co
LEFT JOIN runner_orders ro ON  co.order_id= ro.order_id
GROUP BY runner_id
ORDER BY runner_id ???
```
#### 3️⃣ Is there any relationship between the number of pizzas and how long the order takes to prepare?
#### 4️⃣ What was the average distance travelled for each customer?
#### 5️⃣ What was the difference between the longest and shortest delivery times for all orders?
#### 6️⃣ What was the average speed for each runner for each delivery and do you notice any trend for these values?
#### 7️⃣ What is the successful delivery percentage for each runner?  
