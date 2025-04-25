# 🍕 Case Study #2 - Pizza Runner
![image](https://github.com/user-attachments/assets/8e052443-f02c-4438-bd9e-90910d116b73)

## Business Task
Danny was inspired by an Instagram post: “80s Retro Styling and Pizza Is The Future!” He loved the idea but knew pizza alone wouldn’t secure funding. So he combined it with another big idea—Uber-style delivery—and launched Pizza Runner, delivering fresh pizzas straight from his house with the help of freelance devs and local “runners.”

## Entity Relationship Diagram
![Screenshot 2025-04-25 235241](https://github.com/user-attachments/assets/b8595498-f923-4979-b13c-115d187342b5)



## 🧹 Data Cleaning and Manipulation

```sql
CREATE TEMP TABLE customer_orders_temp AS
SELECT 
  order_id, 
  customer_id, 
  pizza_id, 
  CASE
	  WHEN exclusions IS null OR exclusions LIKE 'null' OR exclusions='' THEN NULL
	  ELSE exclusions
	  END AS exclusions,
  CASE
	  WHEN extras IS NULL or extras LIKE 'null' OR extras='' THEN NULL
	  ELSE extras
	  END AS extras,
	order_time
FROM pizza_runner.customer_orders;


CREATE TABLE runner_orders_temp AS
SELECT 
  order_id,
  runner_id,

  -- Cast to TIMESTAMP
  CASE 
    WHEN pickup_time = 'null' OR pickup_time = '' THEN NULL
    ELSE pickup_time::timestamp
  END AS pickup_time,

  -- Cast to FLOAT
  CASE 
    WHEN distance = 'null' OR distance = '' THEN NULL
    ELSE TRIM(BOTH 'km' FROM distance)::float
  END AS distance,

  -- Cast to INTEGER
  CASE 
    WHEN duration = 'null' OR duration = '' THEN NULL
    ELSE REGEXP_REPLACE(duration, '[^0-9]', '', 'g')::int
  END AS duration,

  -- Leave as TEXT
  CASE 
    WHEN cancellation = 'null' OR cancellation = '' THEN NULL
    ELSE cancellation
  END AS cancellation

FROM runner_orders;
```
