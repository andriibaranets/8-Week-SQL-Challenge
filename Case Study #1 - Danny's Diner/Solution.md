# 🍜 Case Study #1: Danny's Diner 
<img src="https://user-images.githubusercontent.com/81607668/127727503-9d9e7a25-93cb-4f95-8bd0-20b87cb4b459.png" alt="Image" width="500" height="520">

***
### **1. What is the total amount each customer spent at the restaurant?**

#### **Solution**:
````sql
select customer_id, sum(price) as "Total amount"
from sales s
left join menu m on
s.product_id = m.product_id
group by customer_id
order by customer_id
````

#### **Steps**:
- First define tables involved:
  - `Sales` for list of customers and what has been sold
  - `Menu` for prices of the items
- Column `product_id` should be used to **join** those tables
- **GROUP BY** should be used on `customer_id`, as we want summary by the customer, in combination with **SUM** on `price`
- Adding **ORDER BY** just for better readability

#### **Answer**:
<img src="https://raw.githubusercontent.com/andriibaranets/8-Week-SQL-Challenge/main/Case%20Study%20%231%20-%20Danny's%20Diner/Results/Answer%201.png" >



***
### **2. How many days has each customer visited the restaurant?**

#### **Solution**:
````sql
select customer_id, count(distinct(order_date)) as "Total visits"
from sales
group by customer_id
````

#### **Steps**:
- Translating request into data requirement, we want to see on how many **distinct** dates each customer has made a purchase
- Base table is `sales`, as customer and date info is located there
- **GROUP BY** should be used on `customer_id`, as we want list of customers
- **COUNT** in combination with **DISTINCT** should be used on `order_date` to get list of distinct dates on which purchases have been made


#### **Answer**:
<img src="https://raw.githubusercontent.com/andriibaranets/8-Week-SQL-Challenge/main/Case%20Study%20%231%20-%20Danny's%20Diner/Results/Answer%202.png" >

***
### **3. What was the first item from the menu purchased by each customer?**

#### **Solution**:
````sql
with ranking as 
(
	select customer_id,
	product_name,
	rank() over (partition by customer_id order by order_date) as ranking
	from sales s 
	left join menu m
	on s.product_id = m.product_id
)
select customer_id,
product_name
from ranking
where ranking = 1
group by 1,2
````

#### **Steps**:
- Window function **RANK** with **PARTITION BY** `customer_id` and **ORDER BY** `order date` will provide us with the order in which products have been purchased by each clients
- To make result readable, **JOIN** is done with `menu` to get `product_name`
- All that is left is to select only rank 1 by using **WHERE** and **GROUP BY** `customer_id` and `product_name` to remove repeating purchases of the same product
- As **WHERE** and **GROUP BY** are applied before the window functions, ranking table should be prepared first and then filered and grouped. It is done by using **WITH** and creating remporary table `ranking` 


#### **Answer**:
<img src="https://raw.githubusercontent.com/andriibaranets/8-Week-SQL-Challenge/main/Case%20Study%20%231%20-%20Danny's%20Diner/Results/Answer%203.png" >


***
### **4. What is the most purchased item on the menu and how many times was it purchased by all customers?**

#### **Solution**:
````sql
select product_name, count(*) as "qty of sales"
from sales s
left join menu m on
s.product_id = m.product_id
group by product_name
order by "qty of sales" desc
limit 1
````

#### **Steps:**
- Translating request into data question, we want to see which item has the most lines in `sales` and what is the quantity
- **GROUP BY** should be used on `product_id` from `sales` in combinarion with **COUNT** to get list of product with quantity of sales
- **ORDER BY** `qty of sales` will provide with list ranked by popularity and **LIMIT** 1 will filter out only the leader
- In order to get name of the product **JOIN** should be done with `menu` to get `product_name`


#### **Answer:**
<img src="https://raw.githubusercontent.com/andriibaranets/8-Week-SQL-Challenge/main/Case%20Study%20%231%20-%20Danny's%20Diner/Results/Answer%204.png" >


***
### **5. Which item was the most popular for each customer?**
#### **Solution**:
````sql
with qty_sales as
(
	select customer_id, 
		product_name, 
		count(*) as "qty of sales",
		dense_rank() over (partition by customer_id order by count(*) DESC) as rank 
	from sales s
	left join menu m on
	s.product_id = m.product_id
	group by customer_id, product_name
	order by "qty of sales" desc
)
select customer_id, product_name, "qty of sales"
from qty_sales
where rank = 1
````

#### **Steps:**
- First let's prepare table with ranks of the food for each customer.
- **DENSE_RANK** window function in combination with **PARTITION BY** `customer_id` will provide rank of each product for each customer **DENSE RANK** should be used, as there can be a customer that bought several products the same amount
- **LEFT JOIN** with `menu` provides the `product_name`
- **WITH** CTE needs to be used to prepare temporary `qty_sales` ranking talbe first, as **WHERE** cannot be applied to the window function


#### **Answer:**
<img src="https://raw.githubusercontent.com/andriibaranets/8-Week-SQL-Challenge/main/Case%20Study%20%231%20-%20Danny's%20Diner/Results/Answer%205.png" >


***

### 6. Which item was purchased first by the customer after they became a member?
#### **Solution**:
````sql
with ranking as (
	select s.customer_id, product_name, order_date, dense_rank() over(partition by s.customer_id order by order_date asc) as rank
	from sales s
	left join menu m on s.product_id = m.product_id
	left join members mem on s.customer_id = mem.customer_id
	where order_date >= join_date
)
select customer_id, product_name, order_date
from ranking
where rank = 1
````

#### Steps:
- **DENSE_RANK** can be used to rank every purchase individually for each customer by date, by using **PARTITION BY** `customer_id` and **ORDER BY** `order_date`
- Only purchases made after the getting the membership are important, so `sales` should be **JOINED** with `members` to get `join_date` and filter out purchases made before that date by using **WHERE**
- Additional **JOIN** is done with `menu` to get `product_name` in order to provide better readability of results
- Finally, ranking table is wrapped up in **WITH** in order to filter out only items with `rank` = 1


#### Answer:
<img src="https://raw.githubusercontent.com/andriibaranets/8-Week-SQL-Challenge/main/Case%20Study%20%231%20-%20Danny's%20Diner/Results/Answer%206.png" >

***
### 7. Which item was purchased just before the customer became a member?
#### **Solution**:
````sql
with ranking as (
	select s.customer_id, product_name, order_date, dense_rank() over(partition by s.customer_id order by order_date desc) as rank
	from sales s
	left join menu m on s.product_id = m.product_id
	left join members mem on s.customer_id = mem.customer_id
	where order_date < join_date
)
select customer_id, product_name, order_date
from ranking
where rank = 1
````

#### Steps:
- Translating question into data requirements, we want to see the last purchase customer has made before they became a member
- Solution is similar to the previous question, with 2 key diffrences:
    - The *latest*, not the *earliest* date is needed, so **ORDER BY** in **DENSE_RANK** should be reversed to **DESC**
    - Purchase should be from *before* the `join_date`, not *after*, so **WHERE** condition is reversed to '<'
- After `ranking` temporary table is prepared, the next step is the same - to filter out only `rank` = 1


#### Answer:
<img src="https://raw.githubusercontent.com/andriibaranets/8-Week-SQL-Challenge/main/Case%20Study%20%231%20-%20Danny's%20Diner/Results/Answer%207.png" >

***
### 8. What is the total items and amount spent for each member before they became a member?
#### **Solution**:
````sql

````

#### Steps:



#### Answer:


***
### 9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
#### **Solution**:
````sql

````

#### Steps:



#### Answer:



***
### 10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?
#### **Solution**:
````sql

````

#### Steps:



#### Answer:


***