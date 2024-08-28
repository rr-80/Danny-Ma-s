# 🍜 Case Study #1: Danny's Diner 
<img src="https://user-images.githubusercontent.com/81607668/127727503-9d9e7a25-93cb-4f95-8bd0-20b87cb4b459.png" alt="Image" width="500" height="520">

## 📚 Table of Contents
- [Business Task](#business-task)
- [Entity Relationship Diagram](#entity-relationship-diagram)
- [Question and Solution](#question-and-solution)

Please note that all the information regarding the case study has been sourced from the following link: [here](https://8weeksqlchallenge.com/case-study-1/). 

***

## Business Task
Danny wants to use the data to answer a few simple questions about his customers, especially about their visiting patterns, how much money they’ve spent and also which menu items are their favourite. 

***

## Entity Relationship Diagram

![image](https://user-images.githubusercontent.com/81607668/127271130-dca9aedd-4ca9-4ed8-b6ec-1e1920dca4a8.png)

***

**1. What is the total amount each customer spent at the restaurant?**

````sql
select s.customer_id, sum(price)
from sales as s join menu as m
on s.product_id = m.product_id
group by s.customer_id;
````
####Answer:
![image](https://github.com/user-attachments/assets/28726718-5ef9-433a-b71e-c3ee7bc5e9da)

**2. How many days has each customer visited the restaurant?**
````sql
  select s.customer_id, count(distinct order_date)
from sales as s
group by s.customer_id;
````


**3. What was the first item from the menu purchased by each customer?**
````sql

````

**4. What is the most purchased item on the menu and how many times was it purchased by all customers?**
````sql

````

**5. Which item was the most popular for each customer?**
````sql

````

**6. Which item was purchased first by the customer after they became a member?**
````sql

````

**7. Which item was purchased just before the customer became a member?**
````sql

````

**8. What is the total items and amount spent for each member before they became a member?**
````sql

````

**9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?**
````sql

````

**10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?**
````sql

````

