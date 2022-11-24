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
<img src="https://github.com/andriibaranets/8-Week-SQL-Challenge/blob/main/Case%20Study%20%232%20-%20Pizza%20Runner/Results/C.3.png?raw=true" >



***
***
### **4. Generate an order item for each record in the customers_orders table in the format of one of the following:**
`Meat Lovers` <br>
`Meat Lovers - Exclude Beef`<br>
`Meat Lovers - Extra Bacon` <br>
`Meat Lovers - Exclude Cheese, Bacon - Extra Mushroom, Peppers`

#### **Solution**:
````sql
ALTER TABLE pizza_runner.customer_orders ADD id serial primary key;

with 
exclusions as
(
	select 
		id,
		order_id,
		trim(unnest(string_to_array(exclusions,','))) as exclusions
	from pizza_runner.customer_orders
	where exclusions is not null and exclusions <> ' '
),
exclusions_name as
(
	select 
		id,
		order_id,
		trim(unnest(string_to_array(exclusions,','))) as exclusions,
		topping_name
	from exclusions
	left join pizza_runner.pizza_toppings t on
	t.topping_id::text = exclusions
),
extras  as
(
	select 
		id,
		order_id,
		trim(unnest(string_to_array(extras ,','))) as extras 
	from pizza_runner.customer_orders
	where extras  is not null and extras  <> ' '
),
extras_name as
(
	select 
		id,
		order_id,
		trim(unnest(string_to_array(extras,','))) as extras ,
		topping_name
	from extras 
	left join pizza_runner.pizza_toppings t on
	t.topping_id::text = extras 
),

adding_exclusions as 
(
	select 
		o.id,
		o.order_id,
		CONCAT(
			n.pizza_name,
			case 
				when e.id is NULL then ''
				else CONCAT(' - Exclude ', string_agg(e.topping_name, ', ') )
			END
		) as record
	from pizza_runner.customer_orders o
	left join pizza_runner.pizza_names n on o.pizza_id = n.pizza_id
	left join exclusions_name e on o.id = e.id
	group by o.id,o.order_id,n.pizza_name,e.id 
),

adding_extras as

(
	select 
		a.id,
		a.order_id,
		CONCAT(
			a.record,
			case 
				when e.id is NULL then ''
				else CONCAT(' - Extras ', string_agg(e.topping_name, ', ') )
			END
		) as record
	from adding_exclusions a
	left join extras_name e on a.id = e.id
	GROUP BY a.id, a.order_id,a.record, e.id
)

select * from adding_extras
````

#### **Steps**:
- We need to generate table where each row is `pizza_name` ordered with names of exclusions and extras, if there were any.
- First, let's add ID column to `customer_orders`, as we'll need to keep track which extras and inclusions are needed for each pizza. It can be done by **ALTER TABLE** and adding a new column (with type `serial` in case for PostgreSQL)
- This question will require use of CTE, as we'll need to transfor and re-combine data before presenting it
- First, using the same logic as in previous 2 questions, we create `extras_name` and `exclusions_name` temporary tables with list of extras and exclusions tied to unique ID from `customer_orders`
- Then we start with list of `ID` and `order_id` and add `exclusions_name` to it. Using **STRING_AGG**, we convert column `topping_name` into string in needed format.
- Same is repeated in `adding_extras`, but for extras
- As a result we get table with `order_id` and corresponding `record` that has been requested

#### **Answer**:
<img src="https://github.com/andriibaranets/8-Week-SQL-Challenge/blob/main/Case%20Study%20%232%20-%20Pizza%20Runner/Results/C.4.png?raw=true">



***
***
### **5. Generate an alphabetically ordered comma separated ingredient list for each pizza order from the customer_orders table and add a 2x in front of any relevant ingredients:**

For example: `Meat Lovers: 2xBacon, Beef, ... , Salami`

#### **Solution**:
````sql
 with extras
as
(
	select 
		id,
		trim(unnest(string_to_array(extras ,','))) as extras 
	from pizza_runner.customer_orders
	where extras is not null and extras  <> ' '
),

exclusions
as
(
	select 
		id,
		trim(unnest(string_to_array(exclusions ,','))) as exclusion 
	from pizza_runner.customer_orders
	where exclusions is not null and extras  <> ' '
),

recipes_expanded
as
(
	select
		pizza_id,
		trim(unnest(string_to_array(toppings ,','))) as component
	from pizza_runner.pizza_recipes
),

order_recipes
as
(
	select
		c.id,
		r.component
	from pizza_runner.customer_orders c
	left join recipes_expanded r on c.pizza_id = r.pizza_id
),

removing_exclusions
as
(
	select
		o.id,
		o.component
	from order_recipes o
	left join exclusions e on o.id = e.id and o.component = e.exclusion
	where e.id is NULL
),

adding_extras
as
(
	select 
		id,
		component
	from removing_exclusions
	UNION ALL
	select 
		id,
		extras as component
	from extras
	order by 1
),

counting_components
as
(
	select
		id,
		component,
		count(*) as qty
	from adding_extras
	group by 1,2
	order by 1,2
),

adding_names
as
(
	select
		c.id,
		case
			when c.qty > 1 then CONCAT(qty,'x ', p.topping_name)
			else p.topping_name
		end as component_name
	from counting_components c
	left join pizza_runner.pizza_toppings p on c.component::INT = p.topping_id
	order by id, topping_name
),

orders_with_pizza_names
as
(
	select 
		c.id,
		c.order_id,
		p.pizza_name
	from pizza_runner.customer_orders c
	left join pizza_runner.pizza_names p on c.pizza_id = p.pizza_id
),

result
as
(
	select
		o.id,
		o.order_id,
		CONCAT(o.pizza_name,': ', string_agg(a.component_name, ', '))
	from orders_with_pizza_names o
	left join adding_names a on o.id = a.id
	group by o.id, o.pizza_name, o.order_id
	
)
select * from result order by 1
````

#### **Steps**:
- First, let's unpack strings into columns for 3 cases - `extras`, `exclusions` and `recipes_expanded`. Last one is connected by **LEFT JOIN** with `customer_orders` to create `order_recipes` which will contan list of toppings for base versions of the pizza orders.
- Next step is to remove exclusions. This is done with **LEFT JOIN** of `recipes_expanded` with `exclusions` and removing any lines that are present in `exclusions`.
- Resulting `removing_exclusions` is then connected by **UNION ALL** with `extras`. This provides us `adding_extras`, which describes all of the components ordered on each order, each line describing an instance of component being ordered.
- This should be **GROUP BY** `id` and `component` to provide us with qty of components ordered for each original line of the order.
- After that, names of the components are taken from `pizza_toppings` and we prepare `component_name` where we add qty of component ordered if it's more than 1. This is where we **ORDER BY** `id` and `topping_name` to ensure alphabeticall order of the ingridients.
- Final preparation step is to create `orders_with_pizza_names` where each line is name of the pizza ordered with `id` and `order_id`
- After that `orders_with_pizza_names` and `adding_names` are joined and needed string is provided by using **STRING_AGG** on `component_name` and **CONCAT** to add `pizza_name` to it and format to the requirements.

#### **Answer**:
<img src="https://github.com/andriibaranets/8-Week-SQL-Challenge/blob/main/Case%20Study%20%232%20-%20Pizza%20Runner/Results/C.5.png?raw=true">



***
***
### **6. What is the total quantity of each ingredient used in all delivered pizzas sorted by most frequent first?**

#### **Solution**:
````sql
with extras
as
(
	select 
		id,
		trim(unnest(string_to_array(extras ,','))) as extras 
	from pizza_runner.customer_orders
	where extras is not null and extras  <> ' '
),

exclusions
as
(
	select 
		id,
		trim(unnest(string_to_array(exclusions ,','))) as exclusion 
	from pizza_runner.customer_orders
	where exclusions is not null and extras  <> ' '
),

recipes_expanded
as
(
	select
		pizza_id,
		trim(unnest(string_to_array(toppings ,','))) as component
	from pizza_runner.pizza_recipes
),

order_recipes
as
(
	select
		c.id,
		r.component
	from pizza_runner.customer_orders c
	left join recipes_expanded r on c.pizza_id = r.pizza_id
),

removing_exclusions
as
(
	select
		o.id,
		o.component
	from order_recipes o
	left join exclusions e on o.id = e.id and o.component = e.exclusion
	where e.id is NULL
),

adding_extras
as
(
	select 
		id,
		component
	from removing_exclusions
	UNION ALL
	select 
		id,
		extras as component
	from extras
	order by 1
),

counting_components
as
(
	select
		component,
		count(*) as qty
	from adding_extras
	group by 1
	order by 1
),

adding_names
as
(
	select
		p.topping_name as ingredient_name,
		c.qty
	from counting_components c
	left join pizza_runner.pizza_toppings p on c.component::INT = p.topping_id
	order by c.qty DESC
)
select * from adding_names
````

#### **Steps**:
- For this question, we need to get list of used ingredient for each order, with exclusions and extras taken into account. This has been already done in the previous question by `counting_components` step.
- We repeat the same steps, this time doing **GROUP BY** only by `component`, as we're interested in total qty, not for just one line.
- In the next step we're `adding_names` from `pizza_runner.pizza_toppings` and **ORDER BY** `qty` **DESC** to provide us with a requested list.

#### **Answer**:
<img src="https://github.com/andriibaranets/8-Week-SQL-Challenge/blob/main/Case%20Study%20%232%20-%20Pizza%20Runner/Results/C.6.png?raw=true">



***