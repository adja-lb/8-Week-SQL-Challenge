# 🍜 Case Study #1: Danny's Diner
<img width="500" height="500" alt="illustration_1" src="https://github.com/user-attachments/assets/0a6bd458-cfbb-4c94-b4e4-4c47f4437607" />

## 📚 Table of Contents
- [Business Task](#business-task)
- [Entity Relationship Diagram](#entity-relationship-diagram)
- [Question and Solution](#question-and-solution)

## Business Task
Danny wants to use the data to answer a few simple questions about his customers, especially about their visiting patterns, how much money they’ve spent and also which menu items are their favourite. 

## Entity Relationship Diagram
<img width="383" height="234" alt="diagram" src="https://github.com/user-attachments/assets/2fb9b3d9-203f-4e82-ba83-6ae8edb72901" />

***

## Question and Solution

### What is the total amount each customer spent at the restaurant?
````sql
SELECT 
	s.customer_id, 
	SUM(m.price) AS total_sales
FROM sales AS s 
JOIN menu AS m 
	ON s.product_id = m.product_id
GROUP BY s.customer_id
ORDER BY total_sales DESC;
````
| customer_id | total_sales |
| ----------- | ----------- |
| A           | 76          |
| B           | 74          |
| C           | 36          |


### How many days has each customer visited the restaurant?
````sql
SELECT 
	customer_id,
	COUNT(DISTINCT order_date) AS unique_days_visited
FROM sales
GROUP BY customer_id 
ORDER BY unique_days_visited desc;
````
| customer_id | unique_days_visited |
| ----------- | --------------------|
| B           | 6          			|
| A           | 4          			|
| C           | 2          			|

### What was the first item from the menu purchased by each customer?
````sql
SELECT 
	customer_id, 
    order_date, 
    product_id,
    product_name, 
    price
FROM (
	SELECT 
		s.customer_id, 
        s.order_date, 
        s.product_id,
        m.product_name,
        m.price,
	    row_number() OVER (
	    	PARTITION BY s.customer_id
	    	ORDER BY s.order_date
	    ) AS rn
	FROM sales AS s
	INNER JOIN menu AS m
        ON s.product_id = m.product_id
) AS ranked
WHERE rn = 1;
````
| customer_id | order_date | product_id | product_name | price |
| ----------- | ---------- | ---------- | ------------ | ----- |
|A			  |	2021-01-01 | 2			|curry		   |15	   |
|B 			  |	2021-01-01 | 2			|curry		   |15	   |
|C			  |	2021-01-01 | 3			|ramen		   |12     |

### What is the most purchased item on the menu and how many times was it purchased by all customers?
````sql
SELECT
````

### Which item was the most popular for each customer?
````sql
SELECT
````

### Which item was purchased first by the customer after they became a member?
````sql
SELECT
````

### Which item was purchased just before the customer became a member?
````sql
SELECT
````

### What is the total items and amount spent for each member before they became a member?
````sql
SELECT
````

### If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
````sql
SELECT
````


### In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?
````sql
SELECT
````

