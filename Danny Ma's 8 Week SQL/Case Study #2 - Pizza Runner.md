# 🍜 Case Study #2 - Pizza Runner:  
<img src="()" alt="Image" width="500" height="520">

## 📚 Table of Contents
- [Business Task](#business-task)
- [Entity Relationship Diagram](#entity-relationship-diagram)
- [Question and Solution](#question-and-solution)

Please note that all the information regarding the case study has been sourced from the following link: [here](https://8weeksqlchallenge.com/case-study-2/). 

***

## Business Task
Danny is expanding his new Pizza Empire and at the same time, he wants to Uberize it, so Pizza Runner was launched!

Danny started by recruiting “runners” to deliver fresh pizza from Pizza Runner Headquarters (otherwise known as Danny’s house) and also maxed out his credit card to pay freelance developers to build a mobile app to accept orders from customers.

***

## Entity Relationship Diagram

![image]()

***

## Data Cleaning: 
### Table: customer_orders
Looking at the customer_orders table below, we can see that there are:

In the exclusions column, there are missing/ blank spaces ' ' and null values.
In the extras column, there are missing/ blank spaces ' ' and null values.
I am cleaning them using a TEMPORARY Table.

````sql
CREATE TEMPORARY TABLE customer_order_temp (
    order_id INT,
    customer_id INT,
    pizza_id INT,
    exclusions VARCHAR(255),
    extras VARCHAR(255),
    order_time DATETIME
) AS
SELECT
    order_id,
    customer_id,
    pizza_id,
    COALESCE(exclusions, '') as exclusions,
    COALESCE(extras, '') as extras,
    order_time
FROM customer_orders;
````

#### Answer:
![image](https://github.com/user-attachments/assets/1a8f699b-b9e9-4d00-80f5-bd7a9b64b719)


### Table: runner_orders

Looking at the table below, we can see that there are:

In the exclusions column, there are missing/ blank spaces ' ' and null values.
In the extras column, there are missing/ blank spaces ' ' and null values
```` sql
CREATE TEMPORARY TABLE runner_orders_temp AS
SELECT
    order_id,
    runner_id,
    COALESCE(pickup_time, ' ') as pickup_time,
    coalesce(trim(case when distance LIKE '%km' THEN replace(distance, 'km', ' ') else distance END),' ') as distance,
    COALESCE(TRIM(REGEXP_REPLACE(duration, '(minutes|minute|min|mins|s)', ' ')),' ') AS duration,
    COALESCE(cancellation, '') as cancellation
FROM runner_orders;
````
#### Answer:
![image](https://github.com/user-attachments/assets/0e486165-9434-4283-af47-2e4d54d79e9b)

## [Question and Solution](#question-and-solution)

***

### A. Pizza Metrics

**1. How many pizzas were ordered?**

````sql
SELECT COUNT(*) AS pizza_order_count
FROM customer_orders_temp;
````
#### Answer:
![image](https://github.com/user-attachments/assets/db127ebb-2a5a-4204-8ace-b7110a9c7254)



**2. How many unique customer orders were made?**
````sql
SELECT count(distinct order_id) as unique_customer_orders
FROM customer_order_temp;
````
#### Answer: 
![image](https://github.com/user-attachments/assets/e47f378f-9df1-4125-8bab-1bc3c08f9d49)


**3. How many successful orders were delivered by each runner?**
````sql
SELECT r.runner_id, count(r.order_id) as successful_order_count
FROM runner_orders_temp as r
where cancellation NOT in ('Customer Cancellation','Restaurant Cancellation')
group by r.runner_id;
````
#### Answer: 
![image](https://github.com/user-attachments/assets/a8c093fa-3444-4206-87ca-ad4ce96cd8de)


**4. How many of each type of pizza was delivered?**
````sql
select p.pizza_id, count(c.order_id) as pizzas_delivered
from pizza_names as p 
join customer_order_temp as c 
on p.pizza_id = c.pizza_id
join runner_orders_temp as r
on c.order_id = r.order_id
where lower(cancellation) not LIKE '%cancellation%'
group by c.pizza_id;
````
#### Answer:
![image](https://github.com/user-attachments/assets/e91a7ba9-5f54-4efe-a891-00c3dd982076)

**5. How many Vegetarian and Meatlovers were ordered by each customer?**
````sql
select c.customer_id, p.pizza_name, 
sum(case 
when p.pizza_name = 'Meatlovers' THEN 1
when p.pizza_name = 'Vegetarian' THEN 1 
END) as order_count
from pizza_names as p 
join customer_order_temp as c 
on p.pizza_id = c.pizza_id
join runner_orders_temp as r
on c.order_id = r.order_id
group by c.customer_id, p.pizza_name
order by c.customer_id;
````
#### Answer:
![image](https://github.com/user-attachments/assets/ebe9a5a9-6ad0-440f-93a2-29f8bd80645d)

**6. What was the maximum number of pizzas delivered in a single order?**
````sql
WITH ranked_ct_pizzas_delivered as (
select c.order_id, count(p.pizza_name) as pizzas_delivered, rank()over(order by count(p.pizza_name) desc) as rnk
from pizza_names as p 
join customer_order_temp as c 
on p.pizza_id = c.pizza_id
join runner_orders_temp as r
on c.order_id = r.order_id
group by c.order_id)

select order_id, max(pizzas_delivered)
from ranked_ct_pizzas_delivered;
````
#### Answer:
![image](https://github.com/user-attachments/assets/e51b9675-726a-4f0f-8e2f-41b5eb424dc0)

**7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?**
````sql

````
#### Answer:


**8. **
````sql

````
#### Answer:


**9. **
````sql

````
#### Answer:


**10. **

````sql

````
#### Answer:


***

## BONUS QUESTIONS

** **

** **

````sql

````
#### Answer:


** **

** **

````sql

````
#### Answer:

***
