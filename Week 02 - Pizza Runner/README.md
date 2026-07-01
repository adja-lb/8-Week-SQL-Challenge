# 🍕 Case Study #2: Pizza Runner
<img width="40%" height="40%" alt="illustration_2" src="https://github.com/user-attachments/assets/0890cd2e-08f6-4e9e-b5f6-c31f3a02a983" />

STAR:
- Situation:
- Task:
- Actions:
- Results:


***
## Table creation
SQL script for [table creation](https://github.com/adja-lb/8-Week-SQL-Challenge/blob/main/Week%2002%20-%20Pizza%20Runner/table_creation.md)

```sql
SET search_path = week2_pizza_runner;
```

## Data Prep
This case study raw data contains errors, notably the tables `customer_orders` and `runner_orders`. Below, you'll find a description of the errors.

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

Before beginning any sort of queries I transformed [data cleaning](https://github.com/adja-lb/8-Week-SQL-Challenge/blob/main/Week%2002%20-%20Pizza%20Runner/exploration_and_cleaning.md)


## Question and Solution
This case study has LOTS of questions - they are broken up by area of focus including:
- [Pizza Metrics](https://github.com/adja-lb/8-Week-SQL-Challenge/blob/main/Week%2002%20-%20Pizza%20Runner/A.%20Pizza%20Metrics.md)
- [Runner and Customer Experience](https://github.com/adja-lb/8-Week-SQL-Challenge/blob/main/Week%2002%20-%20Pizza%20Runner/B.%20Runner%20and%20Customer%20Experience.md)
- [Ingredient Optimisation](https://github.com/adja-lb/8-Week-SQL-Challenge/blob/main/Week%2002%20-%20Pizza%20Runner/C.%20Ingredient%20Optimisation.md)
- [Pricing and Ratings](https://github.com/adja-lb/8-Week-SQL-Challenge/blob/main/Week%2002%20-%20Pizza%20Runner/D.%20Pricing%20and%20Ratings.md)


## What I learnt
- **Data cleaning and prep** : handling formatting errors
- 
