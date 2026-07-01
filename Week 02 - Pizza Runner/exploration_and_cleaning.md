# A. Pizza Metrics

## Exploration
After a quick glance, the tables `pizza_names`, `pizza_recipes` and `pizza_toppings` do not oppose a challenge as they are small and not complexe that we can visually explore. Plus they are complete. If we were to be very detailled we would create primary keys and modify data types as serial to ensure unique ids.
However, `customer_orders` and `runner_orders` are more complexe and require to thouroughly check with queries to ensure data integrity. Below, you can find my exploration for data integrity violations. 

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
This table has multiple columns with inconsistencies :
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

## Cleaning
Medallion architecture, I create a pseudo-silver layer for our two problematic tables.

```sql
SET search_path = week2_pizza_runner;

SELECT * FROM customer_orders;

-- Preparation
SELECT
    exclusions AS original_data,
    CASE
        -- 1. If it's already a true SQL NULL, keep it NULL
        WHEN exclusions IS NULL THEN NULL

        -- 2. Turn literal text strings that spell 'NULL' into true SQL NULLs
        WHEN UPPER(TRIM(exclusions)) = 'NULL' THEN NULL

        -- 3. Turn completely blank cells or spaces into true SQL NULLs
        WHEN TRIM(exclusions) = '' THEN NULL

        -- 4. Keep everything else exactly as it is (e.g., '1', '1,2', '4,5,6')
        ELSE TRIM(exclusions)
    END AS cleaned_data
FROM customer_orders;

-- Create silver table
DROP TABLE IF EXISTS silver_customer_orders;
CREATE TABLE IF NOT EXISTS  silver_customer_orders (
serial_id SMALLSERIAL PRIMARY KEY, -- create a new column to be able to have a PRIMARY KEY with unique ids for this table
order_id INTEGER,
customer_id INTEGER,
pizza_id INTEGER,
exclusions VARCHAR(4),
extras VARCHAR(4),
order_time TIMESTAMP
);


INSERT INTO silver_customer_orders (
    order_id, customer_id, pizza_id, exclusions, extras, order_time)
SELECT
    order_id,
    customer_id,
    pizza_id,
    -- clean exclusions col
    CASE
        WHEN exclusions IS NULL THEN NULL
        WHEN UPPER(TRIM(exclusions)) = 'NULL' THEN NULL
        WHEN TRIM(exclusions) = '' THEN NULL
        ELSE TRIM(exclusions)
    END,

    -- clean extras col
    CASE
        WHEN extras IS NULL THEN NULL
        WHEN UPPER(TRIM(extras)) = 'NULL' THEN NULL
        WHEN TRIM(extras) = '' THEN NULL
        ELSE TRIM(extras)
    END,
    order_time
FROM customer_orders;


DROP TABLE IF EXISTS silver_runner_orders;
CREATE TABLE silver_runner_orders(
	order_id smallserial PRIMARY KEY,
	runner_id INTEGER,
	pickup_time TIMESTAMP,
	distance_km FLOAT,
	duration_minutes FLOAT,
	cancellation VARCHAR(50)
);

INSERT INTO silver_runner_orders (
    order_id,
    runner_id,
    pickup_time,
    distance_km,
    duration_minutes,
    cancellation
)
SELECT
    order_id,
    runner_id,
     -- CLEAN PICKUP_TIME (Cast standard strings or handle 'NULL' text strings)
    CASE
        WHEN pickup_time IS NULL THEN NULL
        WHEN UPPER(TRIM(pickup_time)) = 'NULL' THEN NULL
        WHEN TRIM(pickup_time) = '' THEN NULL
        ELSE CAST(pickup_time AS TIMESTAMP)
    END,

    -- CLEAN DISTANCE (Strip 'km' and cast to FLOAT)
    CASE
        WHEN distance IS NULL THEN NULL
        WHEN UPPER(TRIM(distance)) = 'NULL' THEN NULL
        WHEN TRIM(distance) = '' THEN NULL
        -- Extract just the numbers and decimals, ignoring 'km'
        ELSE CAST(REGEXP_SUBSTR(distance, '[0-9.]+') AS FLOAT)
    END,

    -- CLEAN DURATION (Strip 'mins', 'minute', etc. and cast to FLOAT)
    CASE
        WHEN duration IS NULL THEN NULL
        WHEN UPPER(TRIM(duration)) = 'NULL' THEN NULL
        WHEN TRIM(duration) = '' THEN NULL
        -- Extract just the numbers and decimals, ignoring alphabetic text
        ELSE CAST(REGEXP_SUBSTR(duration, '[0-9.]+') AS FLOAT)
    END,

   -- CLEAN CANCELLATION
    CASE
        WHEN cancellation IS NULL THEN NULL
        WHEN UPPER(TRIM(cancellation)) = 'NULL' THEN NULL
        WHEN TRIM(cancellation) = '' THEN NULL
        ELSE TRIM(cancellation)
    END

FROM runner_orders;
```

