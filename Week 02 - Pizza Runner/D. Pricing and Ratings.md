# D. Pricing and Ratings

**1. If a Meat Lovers pizza costs $12 and Vegetarian costs $10 and there were no charges for changes - how much money has Pizza Runner made so far if there are no delivery fees?**
ALTER pizza_recipes with to insert a new column with pizza prices. Then update it by inserting the corresponding prices.
```sql
ALTER TABLE pizza_recipes
ADD pizza_prices FLOAT;

UPDATE pizza_recipes
SET pizza_prices = 12
WHERE pizza_id = 1;

UPDATE pizza_recipes
SET pizza_prices = 10
WHERE pizza_id = 2;
```

```sql
SELECT
   pn.pizza_name,
   SUM(pr.pizza_prices) AS total_sales
FROM silver_customer_orders AS co
INNER JOIN pizza_recipes AS pr
    ON co.pizza_id = pr.pizza_id
INNER JOIN pizza_names AS pn
    ON co.pizza_id = pn.pizza_id
INNER JOIN silver_runner_orders AS ro
    ON co.order_id = ro.order_id
WHERE ro.cancellation IS NULL
GROUP BY ROLLUP(pn.pizza_name)
ORDER BY pn.pizza_name;
```

| pizza_name | total_sales |
| ---------- | ----------- |
| Meatlovers | 108 |
| Vegetarian | 30 |
| <null>     | 138 |

**2. What if there was an additional $1 charge for any pizza extras?**
- Add cheese is $1 extra
```sql
ALTER TABLE pizza_toppings
ADD topping_prices FLOAT;

UPDATE pizza_toppings
SET topping_prices = 1
WHERE topping_id = 4;
```

```sql
WITH delivered_orders AS (
    SELECT
        co.serial_id,
        co.pizza_id,
        co.extras
    FROM silver_customer_orders co
    JOIN silver_runner_orders ro ON co.order_id = ro.order_id
    WHERE ro.cancellation IS NULL
),

extras_revenue AS (
    SELECT
        d.serial_id,
        COALESCE(SUM(pt.topping_price), 0) AS total_extra_cost
    FROM delivered_orders d
    -- LEFT JOIN pour ne pas perdre les pizzas qui n'ont PAS d'extras
    LEFT JOIN LATERAL REGEXP_SPLIT_TO_TABLE(d.extras, '[,\s]+') AS ex_id ON d.extras IS NOT NULL AND d.extras NOT IN ('', 'null')
    LEFT JOIN pizza_toppings pt ON ex_id::INT = pt.topping_id
    GROUP BY d.serial_id
)

SELECT
   pn.pizza_name,
   SUM(pr.pizza_prices) AS base_pizza_sales,
   SUM(er.total_extra_cost) AS additional_extras_sales,
   SUM(pr.pizza_prices + er.total_extra_cost) AS total_combined_sales
FROM delivered_orders d
JOIN pizza_recipes pr ON d.pizza_id = pr.pizza_id
JOIN pizza_names pn ON d.pizza_id = pn.pizza_id
JOIN extras_revenue er ON d.serial_id = er.serial_id
GROUP BY ROLLUP(pn.pizza_name)
ORDER BY pn.pizza_name;
```
<img width="1108" height="108" alt="D_Q2" src="https://github.com/user-attachments/assets/4cf8f6ed-3dd0-4aef-b454-75bf11370280" />

**3. The Pizza Runner team now wants to add an additional ratings system that allows customers to rate their runner, how would you design an additional table for this new dataset - generate a schema for this new table and insert your own data for ratings for each successful customer order between 1 to 5.**
```sql

```


**4. Using your newly generated table - can you join all of the information together to form a table which has the following information for successful deliveries?**
- customer_id
- order_id
- runner_id
- rating
- order_time
- pickup_time
- Time between order and pickup
- Delivery duration
- Average speed
- Total number of pizzas

```sql

```

**5. If a Meat Lovers pizza was $12 and Vegetarian $10 fixed prices with no cost for extras and each runner is paid $0.30 per kilometre traveled - how much money does Pizza Runner have left over after these deliveries?**
```sql

```

