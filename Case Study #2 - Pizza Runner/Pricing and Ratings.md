# Pricing and Ratings


#### 1️⃣. If a Meat Lovers pizza costs $12 and Vegetarian costs $10 and there were no charges for changes - how much money has Pizza Runner made so far if there are no delivery fees?

SELECT 
	SUM(CASE WHEN pizza_name='Meatlovers' THEN 10 ELSE 12 END) as price
FROM customer_orders_temp co
LEFT JOIN pizza_names pn ON pn.pizza_id= co.pizza_id;

#### 2️⃣. What if there was an additional $1 charge for any pizza extras?
-Add cheese is $1 extra

#### 5️⃣. If a Meat Lovers pizza was $12 and Vegetarian $10 fixed prices with no cost for extras and each runner is paid $0.30 per kilometre traveled - how much money does Pizza Runner have left over after these deliveries?
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
