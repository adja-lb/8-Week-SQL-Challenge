# A. Pizza Metrics

## Exploration
After a quick glance, the tables `pizza_names`, `pizza_recipes` and `pizza_toppings` do not oppose a challenge as they are small and not complexe that we can visually explore. Plus they are complete. If we were to be very detailled we would create primary keys and modify data types as serial to ensure unique ids.
However, `customer_orders` and `runner_orders` are more complexe and require to thouroughly check with queries to ensure data integrity. Below, you can find my exploration for data integrity violations. 

## Cleaning
This case study raw data contains errors notably the tables `customer_orders` and `runner_orders`. Below, you'll find a description of the errors.

### `customer_orders`
_exlusions_ and _extras_ columns had inconsistencies with :
- exclusions (VARCHAR): null, BLANK and <_null_> are present
- extras (VARCHAR): null, BLANK and <_null_> are present

| exclusions | extras   |
| ---------- | -------- |
| null       | 2        |
| null       | <_null_> |
| 1, 2       | <_null_> |
| <_null_>   | null     |

| exclusions_clean | extras_clean |
| ---------------- | ------------ |
| <_null_>         | 2            |
| <_null_>         | <_null_>     |
| 1, 2             | <_null_>     |
| <_null_>         | <_null_>     |

### `runner_orders`
_exlusions_ and _extras_ columns had inconsistencies with :
- pickuptime (TIMESTAMP): null and `TIMESTAMP` ;
- distance (FLOAT): null and strings (e.g. 20km, 23.4km) 
- duration (FLOAT): null and strings (e.g. 40, 25mins, 10minute, 27 minutes)
- cancellation (VARCHAR): null, BLANK, <_null_> and Cancellation

| pickuptime          | distance | duration  | cancellation |
| ----------          | -------- | --------- | ------------ |
| 2020-01-01 18:15:34 | 13.4km   | 20 mins   | null         |
| null                | 20 km    | 10minutes | Cancellation |
| 2020-01-08 21:10:57 | 10       | null      | <_null_>     |
| 2020-01-10 00:15:02 | null     | 15 minute | <_null_>     |

| pickuptime_c        | distance_km_c | duration_min_c | cancellation_c |
| ------------------- | ------------- | -------------- | -------------- |
| 2020-01-01 18:15:34 | 13.4          | 20             | <_null_>       |
| <_null_>            | 20.0          | 10             | Cancellation   |
| 2020-01-08 21:10:57 | 10.0          | <_null_>       | <_null_>       |
| 2020-01-10 00:15:02 | <_null_>      | 15             | <_null_>       |
