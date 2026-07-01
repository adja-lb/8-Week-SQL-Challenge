# C. Ingredient Optimisation

**1. What are the standard ingredients for each pizza?**
```sql
WITH toppings_cte AS (
    SELECT
		pizza_id,
		REGEXP_SPLIT_TO_TABLE(toppings, '[,\s]+')::INTEGER AS topping_id
    FROM pizza_recipes
)
SELECT
    pn.pizza_name,
    STRING_AGG(pt.topping_name, ', ') AS all_ingredients -- Merges all topping names into a single cell, separated by a comma and a space
FROM toppings_cte AS t
JOIN pizza_names As pn
    ON t.pizza_id = pn.pizza_id
JOIN pizza_toppings AS pt
    ON t.topping_id = pt.topping_id
GROUP BY pn.pizza_name, pn.pizza_id
ORDER BY pn.pizza_id;
```

| pizza_name	      | all_ingredients							                              |
| ------------------- | --------------------------------------------------------------------- |
| Meatlovers 		  | Bacon, BBQ Sauce, Beef, Cheese, Chicken, Mushrooms, Pepperoni, Salami |
| Vegetarian     	  | Cheese, Mushrooms, Onions, Peppers, Tomatoes, Tomato Sauce      	  |

The toppings_cte will be used repeatedly in this part. I choose to create a view table to improve code readability:
```sql
CREATE VIEW view_pizza_toppings AS 
SELECT
    pizza_id,
    REGEXP_SPLIT_TO_TABLE(toppings, '[,\s]+')::INTEGER AS topping_id
FROM pizza_recipes;
```

**2. What was the most commonly added extra?**
We modify our previous CTE to add a second CTE `ranked_toppings` dependant from the previous one.
```sql
WITH ranked_toppings AS (
    SELECT
		t.topping_id,
		pt.topping_name,
		COUNT(t.topping_id) AS topping_count,
		-- Rank globally by popularity across all recipes
		DENSE_RANK() OVER (ORDER BY COUNT(t.topping_id) DESC) as rnk
    FROM view_pizza_toppings as t
    INNER JOIN pizza_toppings pt
		ON t.topping_id = pt.topping_id
    GROUP BY t.topping_id, pt.topping_name
)
SELECT
    topping_name,
    topping_count
FROM ranked_toppings
WHERE rnk = 1;
```

| topping_name | topping_count |
| ---- | --------- |
| Cheese    | 2         |
| Mushrooms    | 2         |


**3. What was the most common exclusion?**
```sql
WITH exclusions_cte AS (
    SELECT
        order_id,
        REGEXP_SPLIT_TO_TABLE(exclusions, '[,\s]+')::INTEGER AS exclusion_id
    FROM silver_customer_orders
    WHERE exclusions IS NOT NULL
),
ranked_exclusions AS (
    SELECT
        e.exclusion_id,
        COUNT(e.exclusion_id) AS exclusion_count,
        DENSE_RANK() OVER (ORDER BY COUNT(e.exclusion_id) DESC) as rnk
    FROM exclusions_cte AS e
    GROUP BY e.exclusion_id
)
SELECT
    pt.topping_name AS most_common_exclusion,
    r.exclusion_count
FROM ranked_exclusions AS r
JOIN pizza_toppings AS pt
    ON r.exclusion_id = pt.topping_id
WHERE r.rnk = 1;
```

| most_common_exclusion | exclusion_count |
| ---- | --------- |
| Cheese    | 4         |

**4. pr of one of the following:**
- Meat Lovers
- Meat Lovers - Exclude Beef
- Meat Lovers - Extra Bacon
- Meat Lovers - Exclude Cheese, Bacon - Extra Mushroom, Peppers

```sql
SELECT
	TO_CHAR(registration_date, 'W') as week,
	COUNT(runner_id) as n_signups
FROM runners
GROUP BY week
ORDER BY week;
```


**5. Generate an alphabetically ordered comma separated ingredient list for each pizza order from the customer_orders table and add a 2x in front of any relevant ingredients**
- For example: "Meat Lovers: 2xBacon, Beef, ... , Salami"

```sql
```

**6. What is the total quantity of each ingredient used in all delivered pizzas sorted by most frequent first?**
**Step 1**: Filter out cancelled orders immediately
```sql
WITH delivered_orders AS (
    -- Step 1: Filter out cancelled orders immediately
    SELECT
        co.serial_id,
        co.pizza_id,
        co.exclusions,
        co.extras
    FROM silver_customer_orders co
    JOIN silver_runner_orders ro ON co.order_id = ro.order_id
    WHERE ro.cancellation IS NULL
),


```
**Step 2**: Extract standard toppings for every delivered pizza

```sql
standard_recipe_toppings AS (
    SELECT
        d.serial_id,
        REGEXP_SPLIT_TO_TABLE(pr.toppings, '[,\s]+')::INTEGER AS topping_id
    FROM delivered_orders d
    JOIN pizza_recipes pr ON d.pizza_id = pr.pizza_id
),
```

**Step 3a**: Identify toppings that must be removed
```sql
exclusions AS (
    SELECT
        d.serial_id,
        REGEXP_SPLIT_TO_TABLE(d.exclusions, '[,\s]+')::INTEGER AS topping_id
    FROM delivered_orders d
    WHERE d.exclusions IS NOT NULL AND d.exclusions NOT IN ('', 'null')
),

```
**Step 3b**: Identify toppings that must be added
```sql
extras AS (
    SELECT
        d.serial_id,
        REGEXP_SPLIT_TO_TABLE(d.extras, '[,\s]+')::INTEGER AS topping_id
    FROM delivered_orders d
    WHERE d.extras IS NOT NULL AND d.extras NOT IN ('', 'null')
),
```

**Step 4**: Combine base recipes, add extras, and subtract exclusions using UNION ALL
```sql
combined_ingredients AS (
    SELECT serial_id, topping_id, 1 AS weight FROM standard_recipe_toppings
    UNION ALL
    SELECT serial_id, topping_id, 1 AS weight FROM extras
    UNION ALL
    SELECT serial_id, topping_id, -1 AS weight FROM exclusions
)
```

**Step 5**: Final tally joined with text names
```sql
SELECT
    pt.topping_name,
    SUM(ci.weight) AS total_quantity_used
FROM combined_ingredients ci
JOIN pizza_toppings pt ON ci.topping_id = pt.topping_id
GROUP BY pt.topping_name
ORDER BY total_quantity_used DESC, pt.topping_name ASC;
```

<img width="543" height="352" alt="C_Q6" src="https://github.com/user-attachments/assets/da8dcb4b-6313-426a-b481-b46982f2ad3d" />
