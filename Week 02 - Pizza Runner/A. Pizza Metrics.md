# A. Pizza Metrics
**1. How many pizzas were ordered?**
```sql
SELECT count(order_id) AS order_count
FROM customer_orders;
```
| order_count |
| ----------- | 
| 14          |

**2. How many unique customer orders were made?**
```sql
SELECT count(DISTINCT order_id) AS unique_order_count
FROM customer_orders;
```

| unique_order_count |
| ----------- |
| 10           |

**3. How many successful orders were delivered by each runner?**
```sql
SELECT
	runner_id,
    COUNT(order_id) as delivered_orders
FROM silver_runner_orders
WHERE
    cancellation is NULL
GROUP BY
	runner_id
ORDER BY
	delivered_orders DESC;
```

| runner_id | delivered_orders |
| --------- | ---------------- |
| 1         | 4                |
| 2         | 3                |
| 3         | 1                |

**4. How many of each type of pizza was delivered?**
```sql
SELECT
    p.pizza_name,
    count(c.order_id) as num_delivered
FROM silver_customer_orders as c
JOIN week2_pizza_runner.silver_runner_orders AS r
    ON c.order_id = r.order_id
JOIN pizza_names p
    ON c.pizza_id = p.pizza_id
WHERE
    r.cancellation is NULL
GROUP BY
    p.pizza_name
ORDER BY num_delivered DESC;
```

| pizza_name | num_delivered |
| ---------- | ------------- |
| A          | 9             |
| B          | 3             |

**5. How many Vegetarian and Meatlovers were ordered by each customer?**

```sql
SELECT
    c.customer_id,
    -- Creates a column for 'Meatlovers'
    COUNT(CASE WHEN p.pizza_name = 'Meatlovers' THEN c.order_id END) AS meatlovers,
    
    -- Creates a column for 'Vegetarian'
    COUNT(CASE WHEN p.pizza_name = 'Vegetarian' THEN c.order_id END) AS vegetarian
    
FROM silver_customer_orders AS c
JOIN week2_pizza_runner.silver_runner_orders AS r
    ON c.order_id = r.order_id
JOIN pizza_names p
    ON c.pizza_id = p.pizza_id
GROUP BY
    c.customer_id
ORDER BY 
    c.customer_id;
```
<img width="639" height="165" alt="Q5_v2" src="https://github.com/user-attachments/assets/90827f43-45c4-442a-8916-573f1b90137f" />

**6. What was the maximum number of pizzas delivered in a single order?**
```sql
SELECT
    order_id,
    COUNT(pizza_id) OVER(PARTITION BY order_id) AS pizzas_per_order
FROM silver_customer_orders
ORDER BY pizzas_per_order DESC
LIMIT 1;
```

| order_ID | pizzas_per_order |
| -------- | ----------- |
| 4           | 3          |

**7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?**
```sql
SELECT count(order_id) AS order_count
FROM customer_orders;
```

| order_count | total_sales |
| ----------- | ----------- |
| A           | 76          |
| B           | 74          |
| C           | 36          |

**8. How many pizzas were delivered that had both exclusions and extras?**
```sql
SELECT count(order_id) AS order_count
FROM customer_orders;
```

| order_count | total_sales |
| ----------- | ----------- |
| A           | 76          |
| B           | 74          |
| C           | 36          |

**9. What was the total volume of pizzas ordered for each hour of the day?**
```sql
SELECT count(order_id) AS order_count
FROM customer_orders;
```

| order_count | total_sales |
| ----------- | ----------- |
| A           | 76          |
| B           | 74          |
| C           | 36          |

**10. What was the volume of orders for each day of the week?**
```sql
SELECT count(order_id) AS order_count
FROM customer_orders;
```

| order_count | total_sales |
| ----------- | ----------- |
| A           | 76          |
| B           | 74          |
| C           | 36          |

