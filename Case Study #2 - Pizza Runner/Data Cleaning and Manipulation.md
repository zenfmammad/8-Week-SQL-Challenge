# 🍕 Case Study #2 - Pizza Runner
## 🧹 Data Cleaning and Manipulation

```sql
CREATE TEMP TABLE customer_orders_temp AS
SELECT 
  order_id, 
  customer_id, 
  pizza_id, 
  CASE
	  WHEN exclusions IS null OR exclusions LIKE 'null' THEN ' '
	  ELSE exclusions
	  END AS exclusions,
  CASE
	  WHEN extras IS NULL or extras LIKE 'null' THEN ' '
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
