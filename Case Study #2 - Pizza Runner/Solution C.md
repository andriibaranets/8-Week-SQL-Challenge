# 🍜 Case Study #2 - Pizza Runner - Solution C
<img src="https://8weeksqlchallenge.com/images/case-study-designs/2.png" alt="Image" width="500" height="520">

***
### **1. What are the standard ingredients for each pizza?**

#### **Solution**:
````sql
with recipe as 
(
	select 
		pizza_id,
		trim(unnest(string_to_array(toppings,','))) as toppings
	from pizza_runner.pizza_recipes
)
select 
	pizza_name,
	topping_name
from pizza_runner.pizza_names n
left join recipe r on
	n.pizza_id = r.pizza_id
left join pizza_runner.pizza_toppings t on
	t.topping_id::text = r.toppings
````

#### **Steps**:
- First, let's prepare data in `pizza_recipes` for usage, as currently ingridients are listed in on row, separated with a coma. <br>
For this we'll use **STRING_TO_ARRAY** in combination with **UNNEST** to transform list of items into several rows. <br> **TRIM** will remove any leading/trailing zeros.
- After that it's question of **JOINING** `pizza_names` with `pizza_toppings` through temporary `recipe` that has been just created and showing list of `topping_name` correlating to each `pizza_name`

#### **Answer**:
<img src="https://github.com/andriibaranets/8-Week-SQL-Challenge/blob/main/Case%20Study%20%232%20-%20Pizza%20Runner/Results/C.1.png?raw=true">



***

### **2. What was the most commonly added extra?**

#### **Solution**:
````sql
with extras as
(
	select 
		order_id,
		trim(unnest(string_to_array(extras,','))) as extras
	from pizza_runner.customer_orders
	where extras is not null and extras <> ' '
)
select 
	topping_name as extra_name,
	count(order_id) as order_qty
from extras e
left join pizza_runner.pizza_toppings t on
	t.topping_id::text = e.extras
group by extra_name
order by order_qty DESC
````

#### **Steps**:
- Information on toppings used is located in `customer_orders`, column `extras`, listed as a string of numbers.
- First we'll do the same **STRING_TO_ARRAY** and **UNNEST** combinationj of actions as in previous question. This gives us temporary `extras` where each row is one instance of extra being ordered.
- Next **LEFT JOIN** `extras` with `pizza_toppings` on `topping_id` to get `topping_name` for each extra.
- If we **GROUP_BY** results by `topping_name` and **COUNT** linesm we'll get quantity of orders where extra has been ordered. Bacon is the most popular extra with it eing ordered 4 times.

#### **Answer**:
<img src="https://github.com/andriibaranets/8-Week-SQL-Challenge/blob/main/Case%20Study%20%232%20-%20Pizza%20Runner/Results/C.2.png?raw=true" >



***
***
### **3. What was the most common exclusion?**

#### **Solution**:
````sql
with exclusions as
(
	select 
		order_id,
		trim(unnest(string_to_array(exclusions,','))) as exclusions
	from pizza_runner.customer_orders
	where exclusions is not null and exclusions <> ' '
)
select 
	topping_name as exclusion_name,
	count(order_id) as order_qty
from exclusions e
left join pizza_runner.pizza_toppings t on
	t.topping_id::text = e.exclusions
group by exclusion_name
order by order_qty DESC
````

#### **Steps**:
- As data on exclusions is stored the same way as on extras (`customer_orders`, listed as string of numbers), solution is the same as previous question with column name replacements.
- The most picked exclusion is Cheese with 4 orders.

#### **Answer**:
<img src="https://github.com/andriibaranets/8-Week-SQL-Challenge/blob/main/Case%20Study%20%232%20-%20Pizza%20Runner/Results/C.3.png?raw=true"" >



***
***
### **4. Generate an order item for each record in the customers_orders table in the format of one of the following:**
`Meat Lovers` <br>
`Meat Lovers - Exclude Beef`<br>
`Meat Lovers - Extra Bacon` <br>
`Meat Lovers - Exclude Cheese, Bacon - Extra Mushroom, Peppers`

#### **Solution**:
````sql

````

#### **Steps**:
- Question asks us to generate table where each row is `pizza_name` ordered with names of exclusions and extras, if there were any.

#### **Answer**:
<img src="" >



***
***
### **5. Generate an alphabetically ordered comma separated ingredient list for each pizza order from the customer_orders table and add a 2x in front of any relevant ingredients:**

For example: `Meat Lovers: 2xBacon, Beef, ... , Salami`

#### **Solution**:
````sql

````

#### **Steps**:


#### **Answer**:
<img src="" >



***
***
### **6. What is the total quantity of each ingredient used in all delivered pizzas sorted by most frequent first?**

#### **Solution**:
````sql

````

#### **Steps**:


#### **Answer**:
<img src="" >



***