## Table 2: customer_orders

`Exclusions` and `extras` columns contain unwanted values of ***NaN*** and ***Null***, as seen below.

````sql
select *
from pizza_runner.customer_orders
````
<img src="" >

Let's replace all of uwanted values with ' ' instead, which will be easier to manipulate.
For this, table needs to be **UPDATED** and values for those columns should be **SET** to ' ' **WHERE** values are ***NaN*** or ***null***. This can be done in 2 statements below.

````sql
update pizza_runner.customer_orders
set exclusions = ' '
where exclusions is null or exclusions = '' or exclusions = 'null'

update pizza_runner.customer_orders
set extras = ' '
where extras is null or extras = '' or extras = 'null'

select * from pizza_runner.customer_orders

````

<img src="" >

## Table 3: runner_orders

`Pickup_time`, `distance`, `duration` and `cancellation` all contain ***NaN*** and ***null*** values that should be replaced.<br>
Additionally, `distance` and `duration` sometimes contain names of the units, such as ***mins*** or **km**

````sql
select *
from pizza_runner.runner_orders
````
<img src="" >
