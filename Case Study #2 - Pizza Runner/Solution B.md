# 🍜 Case Study #2 - Pizza Runner - Solution B
<img src="https://8weeksqlchallenge.com/images/case-study-designs/2.png" alt="Image" width="500" height="520">

***
### **1. How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)**

#### **Solution**:
````sql
select 
	date_part('week', registration_date) as week_number,
	count(runner_id) as runner_qty
from pizza_runner.runners
group by 1

````

#### **Steps**:
- Each row in `runners` represent 1 runner that have signed up, so we just need to **COUNT** the rows and **GROUP BY** week number that can be extracted from `registration_date` by using **DATE_PART**

#### **Answer**:
<img src="" >



***

### **2. What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?**

#### **Solution**:
````sql
with pickup as
(
	select 
	runner_id,
	pickup_time,
	pickup_time::timestamp - max(order_time) as pickup_interval
	from pizza_runner.runner_orders a
	left join pizza_runner.customer_orders b on
	a.order_id = b.order_id
	where pickup_time is not NULL
	group by 1,2
)
select 
	runner_id,
	(extract(EPOCH from avg(pickup_interval))/60)::int as average_time
	from pickup
	group by 1
````

#### **Steps**:
- Time between order being created and pickup can be calculated by finding difference between `order_time` from `customer_orders` and `pickup_time` from `runner_orders`
- First let's calculate time in minutes for each order that has been picked up (`pickup_time` is not NULL) - this can be done by **JOINING** `customer_orders` with `runner_orders` on `order_id` and adding aggregation on `order_time` (**AVG**, **MAX** or **MIN**), as there are several lines with the same `order_time` in `customer_orders`, one for each position on the order.
- Then we aggregate results by using **GROUP BY** `runner_id` and finding **AVG** on that interval
- In order to get minutes from interval, first we can **EXTRACT** **EPOCH** from the average `pickup_interval`, that will give us duration of the period in seconds. After that to get duration in minutes, we divide seconds by 60 and **CAST** number into **INTEGER**.
#### **Answer**:
<img src="" >



***
### **3. Is there any relationship between the number of pizzas and how long the order takes to prepare?**

#### **Solution**:
````sql

````

#### **Steps**:
- Structure of the query will be simlar to the previous question, with 1 assumption and 1 change
- As we don't have `ready_time` or similar, we would need to use again difference between `order_time` and `pickup_time` to measure time of preparation, as pizza should be ready by the moment of pickup. As PostgreSQL doesn't have **DATEDIFF**, we still use the calculation from previous question
- To get quantity of pizzas within the order we need to **COUNT** lines from `customer_orders` and **GROUP BY** `order_id`
- Using CTE **WITH** we then **AVG** the interval, but this time **GROUP BY** to `pizza_count`

#### **Answer**:
<img src="" >

- We can see that more pizzas in the order means more time between ordering and pickup

***
### **4. What was the average distance travelled for each customer?**

#### **Solution**:
````sql
with customer_list as
(
	select
	distinct customer_id, order_id
	
from pizza_runner.customer_orders 
),

full_distance as
(
	select
		customer_id, 
		a.order_id,
		distance
	from customer_list a
	left join pizza_runner.runner_orders b on
	a.order_id = b.order_id
	where distance <> ' '
)

select 
	customer_id,
	AVG(distance::double precision) as Average_Distance
from full_distance
group by 1
````

#### **Steps**:
- Unique list of customers and their orders can be taken from `customer_orders`, by getting **DISTINCT** combination of `customer_id` and `order_id`
- In order to add distance, we need to **LEFT JOIN** `customer_orders` to `runner_orders` on `order_id` to get `distance` from orders where delivery happened
- After that we can find **AVG** of `distance` and **GROUP BY** `customer_id`
- All of the steps are split using CTE **WITH**

#### **Answer**:
<img src="" >



***
### **5. What was the difference between the longest and shortest delivery times for all orders?**

#### **Solution**:
````sql
select MAX(duration::double precision)-MIN(duration::double precision) as difference
from pizza_runner.runner_orders
where duration <> ' '
````

#### **Steps**:
- All of the delivery times are located within `runner_orders`, so question is about finding the smallest and the biggest value of `duration` and then finding the difference between
- THis can be done bu using **MAX** and **MIN** and then finding difference betwen them

#### **Answer**:
<img src="" >




***
### **6. What was the average speed for each runner for each delivery and do you notice any trend for these values?**

#### **Solution**:
````sql
select 
	runner_id,
	order_id,
	distance::double precision/(duration::double precision/60) as Average_speed
from pizza_runner.runner_orders
where distance <> ' '
order by runner_ID, order_ID
````

#### **Steps**:
- Speed equals `distance` divided by `duration`
- Also we would want speed in km/hour, so `duration` should be divided by 60

#### **Answer**:
<img src="" >

- We can see that speed increases for runners with each new orders, although speed of increase differs from runner to runner.
- All of the runner start from similar speed on their first order - around 35-40 km/hour
***
### **7. What is the successful delivery percentage for each runner?**

#### **Solution**:
````sql
select 
	runner_id,
	sum(
	case when distance <> ' ' then 1
		else 0
	end)::float/count(*)::float*100 as sucessfull_percentage
from pizza_runner.runner_orders
group by 1
order by runner_ID

````

#### **Steps**:
- Percentage of successfull deliveries by dividing quantity of lines from `runner_orders` where `distance` is not empty (by using **CASE**) by total **COUNT** of lines for each runner
- Additional caveat is that **COUNT** returns ***bigint*** type in PostgreSQL, and division of ***bigint*** gets transformed into ***int***, so we need to first cast it into ***float*** to get percentages from the division

#### **Answer**:
<img src="" >