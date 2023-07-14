# 🍜 Case Study #2 - Pizza Runner - Solution E
<img src="https://8weeksqlchallenge.com/images/case-study-designs/2.png" alt="Image" width="500" height="520">

***
### **1. If Danny wants to expand his range of pizzas - how would this impact the existing data design? Write an INSERT statement to demonstrate what would happen if a new Supreme pizza with all the toppings was added to the Pizza Runner menu?**

#### **Solution**:
````sql
INSERT INTO pizza_runner.pizza_recipes
VALUES (3,'1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12');

INSERT INTO pizza_runner.pizza_names
VALUES (3, 'Supreme');
````

#### **Steps**:
- In order to get new pizza on the men, new lines should be added to `pizza_recipes` adn `pizza_name`. This can be done using **INSERT INTO** and providing values of all toppings and name of the new pizza.

#### **Answer**:
<img src="https://github.com/andriibaranets/8-Week-SQL-Challenge/blob/main/Case%20Study%20%232%20-%20Pizza%20Runner/Results/E.1.png?raw=true">
<img src="https://github.com/andriibaranets/8-Week-SQL-Challenge/blob/main/Case%20Study%20%232%20-%20Pizza%20Runner/Results/E.2.png?raw=true">



***