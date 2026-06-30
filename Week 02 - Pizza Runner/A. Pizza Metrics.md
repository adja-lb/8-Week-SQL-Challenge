# A. Pizza Metrics
```sql
SET search_path = week2_pizza_runner;
```

How many pizzas were ordered?
```sql
SELECT count(order_id) AS order_count
FROM customer_orders;
```
| order_count |
| ----------- | 
| 14          |


How many unique customer orders were made?
```sql
SELECT count(order_id) AS order_count
FROM customer_orders;
```

| order_count | total_sales |
| ----------- | ----------- |
| A           | 76          |
| B           | 74          |
| C           | 36          |


How many successful orders were delivered by each runner?
```sql
SELECT count(order_id) AS order_count
FROM customer_orders;
```

| order_count | total_sales |
| ----------- | ----------- |
| A           | 76          |
| B           | 74          |
| C           | 36          |

How many of each type of pizza was delivered?
```sql
SELECT count(order_id) AS order_count
FROM customer_orders;
```

| order_count | total_sales |
| ----------- | ----------- |
| A           | 76          |
| B           | 74          |
| C           | 36          |

How many Vegetarian and Meatlovers were ordered by each customer?

```sql
SELECT count(order_id) AS order_count
FROM customer_orders;
```

| order_count | total_sales |
| ----------- | ----------- |
| A           | 76          |
| B           | 74          |
| C           | 36          |

What was the maximum number of pizzas delivered in a single order?
```sql
SELECT count(order_id) AS order_count
FROM customer_orders;
```

| order_count | total_sales |
| ----------- | ----------- |
| A           | 76          |
| B           | 74          |
| C           | 36          |

For each customer, how many delivered pizzas had at least 1 change and how many had no changes?
```sql
SELECT count(order_id) AS order_count
FROM customer_orders;
```

| order_count | total_sales |
| ----------- | ----------- |
| A           | 76          |
| B           | 74          |
| C           | 36          |

How many pizzas were delivered that had both exclusions and extras?
```sql
SELECT count(order_id) AS order_count
FROM customer_orders;
```

| order_count | total_sales |
| ----------- | ----------- |
| A           | 76          |
| B           | 74          |
| C           | 36          |

What was the total volume of pizzas ordered for each hour of the day?
```sql
SELECT count(order_id) AS order_count
FROM customer_orders;
```

| order_count | total_sales |
| ----------- | ----------- |
| A           | 76          |
| B           | 74          |
| C           | 36          |
What was the volume of orders for each day of the week?

