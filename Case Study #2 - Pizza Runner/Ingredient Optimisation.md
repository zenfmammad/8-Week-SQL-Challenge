# 🍕 Case Study #2 - Pizz Runner
## 🍅 Ingredient Optimisation

#### 1️⃣ What are the standard ingredients for each pizza?
```sql
WITH pizza_recipes_temp as
( SELECT
    pizza_name,
    trim(unnest(string_to_array(toppings, ','))) :: int AS toppings
  FROM pizza_recipes pr
  LEFT JOIN pizza_names pn ON pr.pizza_id= pn.pizza_id )

 SELECT
     pizza_name,
     string_agg(topping_name::text, ',' ORDER BY topping_name) AS toppings
 FROM pizza_recipes_temp pr
 LEFT JOIN pizza_toppings pt ON pr.toppings= pt.topping_id
 GROUP BY pizza_name
 ORDER BY pizza_name ASC;
```
![image](https://github.com/user-attachments/assets/4a891316-3780-48cb-aff7-5ff39a3b9f25)


#### 2️⃣ What was the most commonly added extra?
```sql
WITH clean_customer_orders as(
  SELECT pizza_id,
       (case when extras='null' or extras='' then NULL ELSE extras END) as extras_clean
FROM customer_orders)


 SELECT
    trim(unnest(string_to_array(extras_clean, ','))) :: int AS extras,
    count(pizza_id) as count
    
FROM clean_customer_orders
GROUP BY extras
ORDER BY count DESC
LIMIT 1;
```
![image](https://github.com/user-attachments/assets/be1928a2-89a6-4605-815a-cd51c1119b51)

#### 3️⃣ What was the most common exclusion?
```sql

 SELECT
    trim(unnest(string_to_array(exclusions, ','))) :: int AS exclusion,
    count(pizza_id) as count
    
FROM customer_orders_temp
GROUP BY exclusions
ORDER BY count DESC
LIMIT 1;
```
![image](https://github.com/user-attachments/assets/ef021b9e-8c83-4058-881f-5ef80f523de4)

#### 4️⃣ Generate an order item for each record in the customers_orders table in the format of one of the following:
- Meat Lovers <br>
- Meat Lovers - Exclude Beef <br>
- Meat Lovers - Extra Bacon <br>
- Meat Lovers - Exclude Cheese, Bacon - Extra Mushroom, Peppers
#### 5️⃣ Generate an alphabetically ordered comma separated ingredient list for each pizza order from the customer_orders table and add a 2x in front of any relevant ingredients
- For example: "Meat Lovers: 2xBacon, Beef, ... , Salami"
#### 6️⃣ What is the total quantity of each ingredient used in all delivered pizzas sorted by most frequent first?

