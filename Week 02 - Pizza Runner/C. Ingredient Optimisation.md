# C. Ingredient Optimisation

**1. What are the standard ingredients for each pizza?**
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

**2. What was the most commonly added extra?**
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

**3. What was the most common exclusion?**
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

**4. Generate an order item for each record in the customers_orders table in the format of one of the following:**
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

| week | n_signups |
| ---- | --------- |
| 1    | 2         |
| 2    | 1         |
| 3    | 1         |

**5. Generate an alphabetically ordered comma separated ingredient list for each pizza order from the customer_orders table and add a 2x in front of any relevant ingredients**
- For example: "Meat Lovers: 2xBacon, Beef, ... , Salami"

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

**6. What is the total quantity of each ingredient used in all delivered pizzas sorted by most frequent first?**

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
