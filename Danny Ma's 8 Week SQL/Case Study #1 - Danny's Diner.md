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
#### Answer:
![image](https://github.com/user-attachments/assets/0abee626-1c01-4943-b31c-a6d9a73dda4e)


**2. How many days has each customer visited the restaurant?**
````sql
  select s.customer_id, count(distinct order_date)
from sales as s
group by s.customer_id;
````
#### Answer: 
![image](https://github.com/user-attachments/assets/879780b4-d312-47e3-8736-a12ce783ac6c)


**3. What was the first item from the menu purchased by each customer?**
````sql
WITH OrderedPurchase AS (
select s.customer_id, m.product_name, row_number()over(partition by s.customer_id ORDER BY s.order_date) as ordered
from sales as s
INNER JOIN menu as m
on s.product_id = m.product_id
)
Select customer_id, product_name
from OrderedPurchase
where ordered = 1;
````

From my understanding, the first item that was purchased can only be one, however the first order could me multiple items. Hence, I am listing only the first.
#### Answer: 
![image](https://github.com/user-attachments/assets/ca2ad45d-e1a2-4135-a6f6-73a2aaac452c)


**4. What is the most purchased item on the menu and how many times was it purchased by all customers?**
````sql
WITH RankedPurchases as (
select m.product_name, count(m.product_name) as total, rank()over(order by count(m.product_name) desc) as rnk
from sales as s
INNER JOIN menu as m on s.product_id = m.product_id
group by m.product_name
)
select product_name, total
from RankedPurchases
where rnk = 1;
````
#### Answer: 
![image](https://github.com/user-attachments/assets/cbf34635-ce4f-42fb-8cfe-924bc2c2506c)


**5. Which item was the most popular for each customer?**
````sql
WITH PopularItem as
(
select s.customer_id, m.product_name, count(m.product_name) as total, rank()over(partition by s.customer_id order by count(m.product_name) desc) as rnk
from sales as s
INNER JOIN menu as m on s.product_id = m.product_id
group by s.customer_id, m.product_name
)
SELECT customer_id, product_name
from PopularItem
where rnk = 1;
````
#### Answer: 
![image](https://github.com/user-attachments/assets/8c837de3-05ec-4e53-aa72-28d3fe55a85b)

**6. Which item was purchased first by the customer after they became a member?**
````sql
WITH FirstMemberPurchase as (
select s.customer_id, m.product_name, row_number()over(partition by s.customer_id order by order_date) as rnk
from sales as s
INNER JOIN members as me on s.customer_id = me.customer_id and s.order_date>=me.join_date
INNER JOIN menu as m on s.product_id = m.product_id
)
select customer_id, product_name
from FirstMemberPurchase
where rnk = 1;
````
#### Answer: 
![image](https://github.com/user-attachments/assets/4fb09052-0052-4d42-956e-5bd27ca682ef)

**7. Which item was purchased just before the customer became a member?**
````sql
WITH LastPurchase_NonMember as (
select s.customer_id, m.product_name,s.order_date, row_number()over(partition by s.customer_id order by order_date desc) as rnk
from sales as s
INNER JOIN members as me on s.customer_id = me.customer_id and s.order_date<me.join_date
INNER JOIN menu as m on s.product_id = m.product_id
)
Select customer_id, product_name
from LastPurchase_NonMember
where rnk = 1;
````
#### Answer: 
![image](https://github.com/user-attachments/assets/1b10e894-e155-46eb-9cec-964a0fcb1ad3)

**8. What is the total items and amount spent for each member before they became a member?**
````sql
select s.customer_id, sum(m.price) as AmtSpent, count(m.product_name) as TotalItems
from sales as s
INNER JOIN members as me on s.customer_id = me.customer_id and s.order_date<me.join_date
INNER JOIN menu as m on s.product_id = m.product_id
group by s.customer_id
ORDER BY s.customer_id;
````
#### Answer: 
![image](https://github.com/user-attachments/assets/b80ac60d-f8f7-4131-9333-88176d04dac3)


**9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?**
````sql
select s.customer_id, sum(case when m.product_name NOT IN ('sushi') THEN m.price*10 ELSE m.price*20 END) as points
from sales as s
INNER JOIN menu as m on s.product_id = m.product_id
group by s.customer_id;
````
#### Answer: 
![image](https://github.com/user-attachments/assets/11e4f9b2-1eab-4745-9bc3-c28135befa95)
  
**10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?**
````sql
select s.customer_id, sum(m.price*20) as points
from sales as s
INNER JOIN members as me on s.customer_id = me.customer_id and s.order_date >= me.join_date
INNER JOIN menu as m on s.product_id = m.product_id
where extract(month from s.order_date) <2
group by s.customer_id
order by s.customer_id;
````
#### Answer: 
![image](https://github.com/user-attachments/assets/76088362-5f00-460d-9e72-7a735e69efc0)


