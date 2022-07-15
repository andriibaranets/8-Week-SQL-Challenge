# 🍜 Case Study #1: Danny's Diner 
<img src="https://user-images.githubusercontent.com/81607668/127727503-9d9e7a25-93cb-4f95-8bd0-20b87cb4b459.png" alt="Image" width="500" height="520">

***
### 1. What is the total amount each customer spent at the restaurant?

````sql
select customer_id, sum(price)
from sales s
left join menu m on
s.product_id = m.product_id
group by customer_id
order by customer_id
````

#### Steps:
- First define tables involved:
  - `Sales` for list of customers and what has been sold
  - `Menu` for prices of the items
- Column `product_id` should be used to **join** those tables
- **GROUP BY** should be used on `customer_id`, as we want summary by the customer, in combination with **SUM** on `price`

#### Answer:
<img src="https://raw.githubusercontent.com/andriibaranets/8-Week-SQL-Challenge/main/Case%20Study%20%231%20-%20Danny's%20Diner/Results/Answer%201.png" >



***
### 2. How many days has each customer visited the restaurant?

````sql

````

#### Steps:



#### Answer:


***

***
### 3. What was the first item from the menu purchased by each customer?

````sql

````

#### Steps:



#### Answer:


***

***
### 4. What is the most purchased item on the menu and how many times was it purchased by all customers?

````sql

````

#### Steps:



#### Answer:


***

***
### 5. Which item was the most popular for each customer?

````sql

````

#### Steps:



#### Answer:


***

***
### 5. Which item was the most popular for each customer?

````sql

````

#### Steps:



#### Answer:


***

***
### 6. Which item was purchased first by the customer after they became a member?

````sql

````

#### Steps:



#### Answer:


***

***
### 7. Which item was purchased just before the customer became a member?

````sql

````

#### Steps:



#### Answer:


***

***
### 8. What is the total items and amount spent for each member before they became a member?

````sql

````

#### Steps:



#### Answer:


***

***
### 9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?

````sql

````

#### Steps:



#### Answer:


***

***
### 10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?

````sql

````

#### Steps:



#### Answer:


***