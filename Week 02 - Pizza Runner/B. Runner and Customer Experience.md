# B. Runner and Customer Experience

# B. Runner and Customer Experience

**1. How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)**
```sql
SELECT
	TO_CHAR(registration_date, 'W') as week,
	COUNT(runner_id) as n_signups
FROM runners
GROUP BY week
ORDER BY week;
```

| week | n_signups |
| ---- | --------- |
| 1    | 2         |
| 2    | 1         |
| 3    | 1         |

**2. What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?**
```sql
SELECT
    r.runner_id,
    -- Subtract timestamps to get an interval, extract total seconds, and divide by 60
    AVG(EXTRACT(EPOCH FROM (r.pickup_time::TIMESTAMP - c.order_time::TIMESTAMP)) / 60) AS avg_pickuptime_min
FROM silver_runner_orders AS r
JOIN silver_customer_orders AS c
    ON r.order_id = c.order_id
WHERE r.cancellation IS NULL 
GROUP BY r.runner_id
ORDER BY avg_pickuptime_min DESC;
```
| runner_id | avg_pickuptime_min |
| --------- | ------------------ |
| 2         | 23.72              |
| 1         | 15.67              |
| 3         | 10.47              |

**3. Is there any relationship between the number of pizzas and how long the order takes to prepare?**
```sql
WITH order_metrics AS (
    SELECT
        c.order_id,
        COUNT(c.pizza_id) AS pizzas_in_order,
        EXTRACT(EPOCH FROM (r.pickup_time::TIMESTAMP - c.order_time::TIMESTAMP)) / 60 AS prep_minutes
    FROM silver_customer_orders AS c
    JOIN silver_runner_orders AS r ON c.order_id = r.order_id
    GROUP BY c.order_id, r.pickup_time, c.order_time
)
SELECT
    pizzas_in_order,
    AVG(prep_minutes) AS avg_prep_minutes
FROM order_prepped_sizes
GROUP BY pizzas_in_order
ORDER BY pizzas_in_order DESC;
```
| pizzas_in_order | avg_prep_minutes |
| --------------- | ---------------- |
| 3               | 29.28            |
| 2               | 18.375           |
| 1               | 12.36            |


With the same `order_metrics` CTE we can now find the 
```sql
SELECT
    ROUND(CORR(prep_minutes, pizzas_in_order)::NUMERIC, 4) AS corr_coef,
    ROUND(REGR_SLOPE(prep_minutes, pizzas_in_order)::NUMERIC, 2) AS minutes_added_per_pizza,
    ROUND(REGR_INTERCEPT(prep_minutes, pizzas_in_order)::NUMERIC, 2) AS base_prep_overhead_minutes
FROM order_metrics;
```
| corr_coef | minutes_added_per_pizza | base_prep_overhead_minutes |
| --------- | ----------------------- | -------------------------- |
| 0.8357    | 7.85                    | 4.2                        |

Output interpretation:
- `corr_coef`: There's a strong relationship between the number of pizzas in an order and average duration to prep the order
- Slope: The marginal cost of adding one more pizza to the base order of 1 pizza
- Intercept: The fixed operational overhead (e.g., boxing, routing the order, initial kitchen latency) before any pizzas are actually thrown into the oven.

```
**4. What was the average distance travelled for each customer?**
```sql
SELECT
    c.customer_id,
    AVG(r.distance_km) AS average_distance_traveled
FROM silver_customer_orders as c
JOIN week2_pizza_runner.silver_runner_orders as r
    ON c.order_id = r.order_id
WHERE r.cancellation IS NULL
GROUP BY c.customer_id
ORDER BY average_distance_traveled DESC;
```
<img width="583" height="163" alt="B_Q4" src="https://github.com/user-attachments/assets/018a159e-5a24-4b31-8fb2-5c4e8ea4b46f" />


**5. What was the difference between the longest and shortest delivery times for all orders?**
```sql
SELECT
    MAX(duration_minutes) - MIN(duration_minutes) AS diff
FROM silver_runner_orders
WHERE cancellation IS NULL;
```
| diff | 
| ---- |
| 30   | 


**6. What was the average speed for each runner for each delivery and do you notice any trend for these values?**
```sql
SELECT
    runner_id,
    order_id,
    ROUND((distance_km / (duration_minutes / 60.0))::NUMERIC, 2) AS average_speed_kmh
FROM silver_runner_orders
WHERE cancellation IS NULL
ORDER BY runner_id;
```
<img width="626" height="244" alt="B_Q6" src="https://github.com/user-attachments/assets/274c75a6-6ed4-46a2-9da8-b378faaa614f" />

- Urban Baseline: Average speeds consistently scale under 50 km/h which is completely normal for urban scooter/car courier patterns.
- Data Flag: Isolated an extreme outlier of 93.6 km/h.
- Root Cause Assessment: Unlikely to be legitimate driving behavior given city constraints. 

**7. What is the successful delivery percentage for each runner?**
```sql
SELECT
	runner_id,
	COUNT(CASE WHEN cancellation IS NULL THEN 1 END) * 100 / COUNT(*) as success_rate
FROM silver_runner_orders
GROUP BY runner_id
ORDER BY success_rate DESC;
```
| runner_id | success_rate |
| --------- | ------------ |
| 1         | 100          |
| 2         | 75           |
| 3         | 50           |
