## Table 2: customer_orders

`Exclusions` and `extras` columns contain unwanted values of ***NaN*** and ***Null***, as seen below.

````sql
select *
from pizza_runner.customer_orders
````
<img src="https://github.com/andriibaranets/8-Week-SQL-Challenge/blob/main/Case%20Study%20%232%20-%20Pizza%20Runner/Results/Customer_orders_before.png?raw=true" >

Let's replace all of uwanted values with **null** instead, which will be easier to manipulate.
For this, table needs to be **UPDATED** and values for those columns should be **SET** to '' **WHERE** values are ***NaN*** or ***null***. This can be done in 2 statements below.

````sql
update pizza_runner.customer_orders
set exclusions = null
where exclusions = ' ' or exclusions = 'null';

update pizza_runner.customer_orders
set extras = null
where extras = ' ' or extras = 'null';

select * from pizza_runner.customer_orders;

````

<img src="https://github.com/andriibaranets/8-Week-SQL-Challenge/blob/main/Case%20Study%20%232%20-%20Pizza%20Runner/Results/Customer_orders_after.png?raw=true" >

## Table 3: runner_orders

`Pickup_time`, `distance`, `duration` and `cancellation` all contain ***NaN*** and ***null*** values that should be replaced.<br>
Additionally, `distance` and `duration` sometimes contain names of the units, such as ***mins*** or **km**

````sql
select *
from pizza_runner.runner_orders
````
<img src="https://github.com/andriibaranets/8-Week-SQL-Challenge/blob/main/Case%20Study%20%232%20-%20Pizza%20Runner/Results/pizza_runners_before.png?raw=true" >

Issue with ***NaN*** and ***null*** can be resolved the same way as in the previous table, bu updating the rows for columns in question and setting value to ' '.

````sql
update pizza_runner.runner_orders
set pickup_time = null
where pickup_time = ' ' or pickup_time = 'null';

update pizza_runner.runner_orders
set distance = null
where distance = ' ' or distance = 'null';

update pizza_runner.runner_orders
set duration = null
where duration = ' ' or duration = 'null';

update pizza_runner.runner_orders
set cancellation = null
where cancellation = ' ' or cancellation = 'null';
````

In case of measurements only part of the value should be **REPLACED**, so function **regexp_replace** can be used to cover both 'km' and variations of 'min'.

````sql
update pizza_runner.runner_orders
set distance = regexp_replace(distance, 'km', '');


update pizza_runner.runner_orders
set duration = regexp_replace(duration, 'min.*', '');

select *
from pizza_runner.runner_orders
````


<img src="https://github.com/andriibaranets/8-Week-SQL-Challenge/blob/main/Case%20Study%20%232%20-%20Pizza%20Runner/Results/pizza_runners_after.png?raw=true" >