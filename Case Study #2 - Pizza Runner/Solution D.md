# 🍜 Case Study #2 - Pizza Runner - Solution D
<img src="https://8weeksqlchallenge.com/images/case-study-designs/2.png" alt="Image" width="500" height="520">

***
### **1. If a Meat Lovers pizza costs $12 and Vegetarian costs $10 and there were no charges for changes - how much money has Pizza Runner made so far if there are no delivery fees?**

#### **Solution**:
````sql
select
	sum(
		case when pizza_id = 1 then 12
		else 10
		end
	)as profit
from pizza_runner.customer_orders
````

#### **Steps**:
- `customer_orders` is a table that contains all of the ordered pizzas
- Using **CASE** `pizza_id` can be replaced with proposed costs
- After that we can get total profit using **SUM** - $160

#### **Answer**:
<img src="https://github.com/andriibaranets/8-Week-SQL-Challenge/blob/main/Case%20Study%20%232%20-%20Pizza%20Runner/Results/D.1.png?raw=true">



***
***
### **2. What if there was an additional $1 charge for any pizza extras? Add cheese is $1 extra**

#### **Solution**:
````sql
select
	sum(
		case when pizza_id = 1 then 12
		else 10
		end + length(extras) - length(regexp_replace(extras, '[0-9]', '', 'g'))
	) as profit
	
from pizza_runner.customer_orders
````

#### **Steps**:
- This answers asks us to build on previous price formula
- In order to get quantity of extras we'll use **LENGTH** function and compare length of `extras` with length of `extras` without any numerics. **regexp_replace** can be used to replace numerics with empty space
- This will give us 166$ in total

#### **Answer**:
<img src="https://github.com/andriibaranets/8-Week-SQL-Challenge/blob/main/Case%20Study%20%232%20-%20Pizza%20Runner/Results/D.2.png?raw=true">



***
***
### **3. The Pizza Runner team now wants to add an additional ratings system that allows customers to rate their runner, how would you design an additional table for this new dataset - generate a schema for this new table and insert your own data for ratings for each successful customer order between 1 to 5.**

#### **Solution**:
````sql
CREATE TABLE pizza_runner.order_ratings (
  "order_id" INTEGER,
  "rating" INTEGER
);

INSERT INTO pizza_runner.order_ratings 
select order_id, cancellation
from pizza_runner.runner_orders
where cancellation = '';

UPDATE 
SET rating = RANDOM()*5;

````

#### **Steps**:
- First, let's define list of columns for this new table. `order_id` and `rating` to reflect the ratings should be enough
- **CREATE TABLE** will give us empty table `order_ratings` with needed columns
- To get list of successfull orders, we'll use `order_id` from `runner_orders` **WHERE** `cancellation` is empty. This list can be **INSERT INTO** `order_ratings`
- Using **UPDATE** and **SET** we can add values to empty ratings. Values can be generated using **RANDOM** function.


#### **Answer**:
<img src="https://github.com/andriibaranets/8-Week-SQL-Challenge/blob/main/Case%20Study%20%232%20-%20Pizza%20Runner/Results/D.3.png?raw=true">



***
***
### **4. Using your newly generated table - can you join all of the information together to form a table which has the following information for successful deliveries?**
- `customer_id`
- `order_id`
- `runner_id`
- `rating`
- `order_time`
- `pickup_time`
- Time between order and pickup
- Delivery duration
- Average speed
- Total number of pizzas


#### **Solution**:
````sql
select 
		min(c.customer_id) as customer_id,
		o.order_id,
		r.runner_id,
		o.rating,
		min(c.order_time) as order_time,
		r.pickup_time,
		(r.pickup_time::timestamp - min(c.order_time)) as "Time between order and pickup",
		round(r.duration::decimal/60,1) as "Delivery duration (h)",
		round(r.distance::decimal/round(r.duration::decimal/60,1),1) as "Average speed (km/h)",
		count(c.id) as "Total numbers of pizza"
 		from pizza_runner.order_ratings o
	left join pizza_runner.runner_orders r on o.order_id = r.order_id
	left join pizza_runner.customer_orders c on o.order_id = c.order_id
	group by o.order_id,
			o.rating,
			r.runner_id,
			r.pickup_time,
			r.duration,
			r.distance
````

#### **Steps**:
- For this question tables we'll need to use columns from `order_ratings`, `runner_orders` and `customer_orders`.
- We'll need only 1 line per successful delivery in the target table, so we can use `order_ratings` as a base and add additional columns to it, using **LEFT JOIN**
- Formulas for the requested measures:
    - 'Time between order and pickup' = `runner_orders`.`pickup_time` - `customer_orders`.`order_time`
    - 'Delivery duration' = (`runner_orders`.`duration`/60)
    - 'Average speed' = `runner_orders`.`distance`/(`runner_orders`.`duration`/60)
    - 'Total number of pizzas' = count(`customer_orders`.`id`)
- As `customer_orders` contains multiple lines per order, we'll need to **GROUP BY** all of the columns from the rest of the tables. Column `order_time` has the same value for all of the lines of the same order, so **MIN** can be used to summarize it

#### **Answer**:
<img src="https://github.com/andriibaranets/8-Week-SQL-Challenge/blob/main/Case%20Study%20%232%20-%20Pizza%20Runner/Results/D.4.png?raw=true">



***
***
### **5. If a Meat Lovers pizza was $12 and Vegetarian $10 fixed prices with no cost for extras and each runner is paid $0.30 per kilometre traveled - how much money does Pizza Runner have left over after these deliveries?**

#### **Solution**:
````sql
with revenue_per_order as 
(
	select
		order_id,
		sum(
			case when pizza_id = 1 then 12
			else 10
			end
		)as revenue
	from pizza_runner.customer_orders
	group by order_id
)
select
	sum(p.revenue - r.distance::decimal*0.3) as profit
from revenue_per_order p
left join pizza_runner.runner_orders r on p.order_id = r.order_id
````

#### **Steps**:
- As distance, and as result, costs are calculated per `order_id`, we'll need to find out `revenue_per_order` first. This can be done as in the first question, together with **GROUP BY** `order_id`
- After that, `runner_orders` can be **JOINED**, where we need `distance` multiplied by $0.30, representing transport costs. These costs are substracted from each order's revenue and by summing it up we get profit for th whole operation $94.44

#### **Answer**:
<img src="https://github.com/andriibaranets/8-Week-SQL-Challenge/blob/main/Case%20Study%20%232%20-%20Pizza%20Runner/Results/D.5.png?raw=true">



***