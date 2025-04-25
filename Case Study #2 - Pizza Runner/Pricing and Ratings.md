# Pricing and Ratings


#### 1️⃣. If a Meat Lovers pizza costs $12 and Vegetarian costs $10 and there were no charges for changes - how much money has Pizza Runner made so far if there are no delivery fees?
```sql
SELECT 
     SUM(CASE WHEN pizza_name='Meatlovers' THEN 10 ELSE 12 END) as price
FROM customer_orders_temp co
LEFT JOIN pizza_names pn ON pn.pizza_id= co.pizza_id;
```

#### 2️⃣. What if there was an additional $1 charge for any pizza extras?
-Add cheese is $1 extra

#### 3️⃣. The Pizza Runner team now wants to add an additional ratings system that allows customers to rate their runner, how would you design an additional table for this new dataset - generate a schema for this new table and insert your own data for ratings for each successful customer order between 1 to 5.
```sql
DROP TABLE IF EXISTS runner_ratings;
CREATE TABLE runner_ratings (
  "id" INTEGER,
  "order_id" INTEGER,
  "runner_id" INTEGER,
  "rating" INTEGER CHECK (rating >= 1 AND rating <= 5)
);

INSERT INTO runner_ratings
  ("id", "order_id", "runner_id", "rating")
VALUES
  ('1', '1', '1', '1'),
  ('2', '2', '1', '2'),
  ('3', '3', '1', '3'),
  ('4', '4', '2', '4'),
  ('5', '5', '3', '5'),
  ('6', '6', '3', '1'),
  ('7', '7', '2', '2'),
  ('8', '8', '2', '3'),
  ('9', '9', '2', '4'),
  ('10', '10', '1', '5');
```

#### 4️⃣.Using your newly generated table - can you join all of the information together to form a table which has the following information for successful deliveries?
customer_id, order_id, runner_id, rating, order_time, pickup_time, Time between order and pickup, Delivery duration, Average speed, Total number of pizzas
```sql
SELECT co.customer_id,
       co.order_id,
       ro.runner_id,
       rating,
       co.order_time,
       ro.pickup_time,
       pickup_time- order_time,
       duration,
       AVG(distance/duration) avg_speed,
       COUNT(pizza_id) as pizza_count  
FROM customer_orders_temp co
LEFT JOIN runner_orders_temp ro ON co.order_id= ro.order_id
LEFT JOIN runner_ratings rr ON ro.order_id= rr.order_id
WHERE cancellation IS NULL
GROUP BY co.customer_id, co.order_id, ro.runner_id, rating, order_time, pickup_time, duration
```


#### 5️⃣. If a Meat Lovers pizza was $12 and Vegetarian $10 fixed prices with no cost for extras and each runner is paid $0.30 per kilometre traveled - how much money does Pizza Runner have left over after these deliveries?
```sql
WITH pizza_prices AS (
  SELECT 
    order_id,
    SUM(CASE WHEN pizza_name = 'Meatlovers' THEN 10 ELSE 12 END) AS price
  FROM customer_orders_temp co
  LEFT JOIN pizza_names pn ON pn.pizza_id = co.pizza_id
  GROUP BY order_id
),
runner_prices AS (
  SELECT
    order_id, 
    COALESCE(sum(distance * 0.30), 0) AS runner_paid
  FROM runner_orders_temp
  GROUP BY order_id
)

SELECT 
  SUM(price-runner_paid) as revenue
FROM pizza_prices pp
LEFT JOIN runner_prices rp ON pp.order_id = rp.order_id;
```
