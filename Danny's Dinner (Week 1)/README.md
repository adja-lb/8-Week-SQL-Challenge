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
	COUNT(DISTINCT order_date) AS unique_day_visit
FROM sales
GROUP BY customer_id 
ORDER BY unique_day_visit desc;
````
| customer_id | unique_day_visit |
| ----------- | -----------------|
| B           | 6          	     |
| A           | 4          		 |
| C           | 2          		 |

### What was the first item from the menu purchased by each customer?
````sql
SELECT 
    customer_id, 
    product_name
FROM (
    SELECT 
        s.customer_id, 
        m.product_name,
        row_number() OVER (
            PARTITION BY s.customer_id
            ORDER BY s.order_date) AS ranking
    FROM sales AS s
    INNER JOIN dannys_diner.menu AS m
        ON s.product_id = m.product_id
) AS ranked
WHERE rn = 1;
````
| customer_id | product_name |
| ----------- | -------------|
| B           | curry        |
| A           | curry  		 |
| C           | ramen        |

/!\ Les données `order_date` n'ont pas de timestamp ni une colonne avec un ordre serial. Cela rend la réponse à la question ambigüe et à la discrétion de chacun. J'ai choisi de faire une requête sur seulement le premier item rank comme dans jeu de données plus complexe qui comporterait au moins un moyen discriminant l'odre de commande des produits entre eux. 

### What is the most purchased item on the menu and how many times was it purchased by all customers?
````sql
SELECT 
    m.product_name, 
    COUNT(s.product_id) AS total_orders
FROM sales AS s
JOIN menu AS m 
    ON s.product_id = m.product_id 
GROUP BY m.product_name 
ORDER BY total_orders DESC
LIMIT 1;
````
| product_name | total_orders  |
| ------------ | ------------- |
| ramen        | 8             |

### Which item was the most popular for each customer?
````sql
WITH count_total AS (
    SELECT
        s.customer_id,
        s.product_id,
        m.product_name, 
        COUNT(s.product_id) AS num_orders,
        DENSE_RANK() OVER (
            PARTITION BY s.customer_id 
            ORDER BY COUNT(s.product_id) DESC)
			AS ranking
    FROM
        sales AS s
    JOIN menu AS m
        ON m.product_id = s.product_id
    GROUP BY
        s.customer_id,
        s.product_id,
        m.product_name
)
SELECT 
    customer_id,
    product_name,
    num_orders,
    ranking
FROM count_total
WHERE
    ranking = 1
ORDER BY customer_id;
````
| customer_id | product_name | num_orders |
| ----------- | -------------| ---------- |
| A           | ramen  		 | 3          |
| B           | sushi        | 2          |
| B           | ramen        | 2          |
| B           | curry        | 2          |
| C           | ramen        | 3          |

### Which item was purchased first by the customer after they became a member?
**Note méthodologique**: 
En France, la praxis veut que lorsqu'un client souscrit à un programme de fidélité lors de son passage en caisse, le repas qu'il vient de consommer soit inclus dans le calcul de ses avantages.

Dans une optique de standardisation et de cohérence entre le parcours client réel et le modèle de données analytique, j'ai fait le choix d'inclure la commande passée au moment même de la souscription plutôt que de démarrer strictement après celle-ci.

Néanmoins, la gestion de cette "première commande" reste sujette à des variations culturelles ou à des décisions politiques propres à chaque entreprise. N'ayant pas d'indications contraires sur la politique spécifique de Danny's Diner, ce choix a été retenu pour refléter la convention commerciale la plus naturelle, mais le modèle reste facilement adaptable si une règle stricte d'exclusion devait être appliquée. 

````sql
WITH count_total AS (
    SELECT
        s.customer_id,
        s.product_id,
        m.product_name, 
        COUNT(s.product_id) AS num_orders,
        DENSE_RANK() OVER (
            PARTITION BY s.customer_id 
            ORDER BY COUNT(s.product_id) DESC) 
            AS ranking
    FROM
        sales AS s
    JOIN menu AS m
        ON m.product_id = s.product_id
    GROUP BY
        s.customer_id,
        s.product_id,
        m.product_name
)
SELECT 
    customer_id,
    product_name,
    num_orders
FROM count_total
WHERE
    ranking = 1
ORDER BY customer_id;
````
| customer_id | product_name  |
| ----------- | ------------- |
| A	          | curry         |
| B           | sushi         |

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

