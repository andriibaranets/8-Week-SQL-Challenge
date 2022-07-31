# 🍜 Case Study #2 - Pizza Runner - Solution A
<img src="https://8weeksqlchallenge.com/images/case-study-designs/2.png" alt="Image" width="500" height="520">

***
### **1. How many pizzas were ordered?**

#### **Solution**:
````sql
select count(*) 
from pizza_runner.customer_orders
````

#### **Steps**:
Each line of `customer_orders` represents 1 pizza ordered, so total quantity of rows will show how many pizzas were ordered.

#### **Answer**:
<img src="" >

***

### **2. How many unique customer orders were made?**

#### **Solution**:
````sql
select count(distinct(order_id)) 
from pizza_runner.customer_orders
````

#### **Steps**:
`order_id` from `customer_orders` contains numbers of all of the orders that have been made, so we need **COUNT** **DISTINCT** order ids.

#### **Answer**:
<img src="" >

***

### **3. How many successful orders were delivered by each runner?**

#### **Solution**:
````sql
select runner_ID, count(*) as orders
from pizza_runner.runner_orders
where cancellation = ' '
group by 1
````

#### **Steps**:
- Each line of `runner_orders` represents separate order, so we need to **COUNT** lines and **GROUP BY** `runner_id` to show qty for each runner.
- As per question, we're interested only in successfull orders, so ones **WHERE** `cancellation` is empty.

#### **Answer**:
<img src="" >

***

### **4. How many of each type of pizza was delivered?**

#### **Solution**:
````sql
select pizza_name, count(*) as pizzas_ordered
from pizza_runner.customer_orders a
left join pizza_runner.pizza_names b on
a.pizza_id = b.pizza_id
group by pizza_name
````

#### **Steps**:
- `pizza_id` from `customer_orders` shows types of pizza that have been delivered and each line represents 1 pizza delivered, so we just need to **COUNT** lines and **GROUP BY** `pizza_id`
- For better readability, we can do a **LEFT JOIN** to `pizza_names` and get `pizza_name` to **GROUP BY** instead.

#### **Answer**:
<img src="" >

***

### **5. How many Vegetarian and Meatlovers were ordered by each customer?**

#### **Solution**:
````sql
select customer_id, pizza_name, count(*) as pizzas_ordered
from pizza_runner.customer_orders a
left join pizza_runner.pizza_names b on
a.pizza_id = b.pizza_id
group by customer_id,pizza_name
````

#### **Steps**:
- This question wans us to split quantities from previous question further down by `customer_id`. We can do it by adding column to the selection and to **GROUP BY**.

#### **Answer**:
<img src="" >

***

### **6. What was the maximum number of pizzas delivered in a single order?**

#### **Solution**:
````sql
select order_id,count(*) as pizzas_ordered
from pizza_runner.customer_orders a
group by order_id
order by pizzas_ordered desc
limit 1
````

#### **Steps**:
- First, let's find out how many pizzas have been delivered within each order. It can be done by **COUNTING** lines from `customer_orders` and **GROUP BY** `order_id`.
- In order to get order with maximum quantity we need to **ORDER BY** `pizzas_ordered` **DESC** and **LIMIT** result only to the first one.

#### **Answer**:
<img src="" >

***
### **7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?**

#### **Solution**:
````sql
select customer_id,count(*) as pizzas_delivered
from pizza_runner.customer_orders a
left join pizza_runner.runner_orders b
on a. order_id = b.order_id
where extras = ' ' and exclusions = ' '
and cancellation = ' '
group by customer_id;


select customer_id,count(*) as pizzas_ordered
from pizza_runner.customer_orders a
left join pizza_runner.runner_orders b
on a. order_id = b.order_id
where NOT(extras = ' ' and exclusions = ' ')
and cancellation = ' '
group by customer_id;
````

#### **Steps**:
- Change in this case would mean value either in `extras` or in `exclusions`.
- Qty of pizzas would be represented by **COUNT** of lines from `customer_orders`, **GROUP BY** `customer_id`.
- In order to get only pizzas with no changes, **WHERE** should be applied with condinition that both `extras` **AND** `exclusions` are emnpty.
- In order to get pizzas with at least 1 change, exactly the reverse condition shoul be applied, using **NOT**
- Additionally, only pizzas delivered to the customer should be counted, meaning that `cancellation` from `runner_orders` for this `order_id` is not empty

#### **Answer**:
<img src="" >
<img src="" >

***

### **8. How many pizzas were delivered that had both exclusions and extras?**

#### **Solution**:
````sql
select count(*) as pizzas_ordered
from pizza_runner.customer_orders a
left join pizza_runner.runner_orders b
on a. order_id = b.order_id
where NOT(extras = ' ' or exclusions = ' ')
and cancellation = ' ';
````

#### **Steps**:
- Request for 'pizza delivered' means that orders hasnn't been cancelled (`cancellation` = ' ')
- If pizza had both exclusions and extras, it means that neither of the `extras` or `exclusions` were empty
- In this case no aggregations are asked for, so **COUNT** for lines in `customer_orders` should suffice

#### **Answer**:
<img src="" >

***

### **9. What was the total volume of pizzas ordered for each hour of the day?**

#### **Solution**:
````sql
select date_part('hour', order_time) as hour, count(*) as pizzas_ordered
from pizza_runner.customer_orders
group by hour
````

#### **Steps**:
- `order_time` from `customer_orders` provides the time of the order, from which **HOUR** can be extracted, using **DATE_PART**
- By **COUNTING** each line of the `customer_orders` and using **GROUP BY** on hour number, we'll get the requested information

#### **Answer**:
<img src="" >

***

### **10. What was the volume of orders for each day of the week?**

#### **Solution**:
````sql
select date_part('DOW', order_time) as DOW, count(*) as pizzas_ordered
from pizza_runner.customer_orders
group by DOW
````

#### **Steps**:
- Day of the week can be extracted by using the same **DATE_PART** function as in the previous question, from 0 for Sunday to 6 for Saturday

#### **Answer**:
<img src="" >

***