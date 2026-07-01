# A. Pizza Metrics

## Exploration
After a quick glance, the tables `pizza_names`, `pizza_recipes` and `pizza_toppings` do not oppose a challenge as they are small and not complexe that we can visually explore. Plus they are complete. If we were to be very detailled we would create primary keys and modify data types as serial to ensure unique ids.
However, `customer_orders` and `runner_orders` are more complexe and require to thouroughly check with queries to ensure data integrity. Below, you can find my exploration for data integrity violations. 


### 1. Checking for Missing Data (NULLs or Blanks)


### 2. Hunting for Duplicates

### 3. Investigating Categorical Data & Typos

### 4. Finding Outliers and Out-of-Range Values

### 5. Checking Logical Consistency (Business Rules)

### 6. Pattern and Length Violations


### Conclusion on findings
This case study raw data contains errors notably the tables `customer_orders` and `runner_orders`. Below, you'll find a description of the errors :

`customer_orders`
_exlusions_ and _extras_ columns had inconsistencies with :
- exclusions (VARCHAR): null, BLANK and <_null_> are present
- extras (VARCHAR): null, BLANK and <_null_> are present
| exclusions | extras | exclusions_clean | extras_clean |
| ---------- | ------ / ---------------- | ------------ |
|null        |2       | <_null_>         | 2            |
|            |        | <_null_>         | <_null_>|
|1, 2        |<_null_>|                  | <_null_>    |
|<_null_>    |null    | <_null_>         | <_null_> |


`runner_orders` :
_exlusions_ and _extras_ columns had inconsistencies with :
- pickuptime (TIMESTAMP): null and `TIMESTAMP` ;
- distance (FLOAT): null and strings (e.g. 20km, 23.4km) 
- duration (FLOAT): null and strings (e.g. 40, 25mins, 10minute, 27 minutes)
- cancellation (description): null, BLANK and <_null_> 

## Cleaning
