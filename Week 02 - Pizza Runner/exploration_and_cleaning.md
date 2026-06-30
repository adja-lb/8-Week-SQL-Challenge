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
- exclusions (description): `null` as varchar; blank; 
- extras (description): `null` as varchar;  

`runner_orders` :
_exlusions_ and _extras_ columns had inconsistencies with :
- pickuptime (description): `null` as varchar;
- distance (description): `null` as varchar;
- duration (description): `null` as varchar;
- cancellation (description): `null` as varchar;

## Cleaning
