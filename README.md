# Pizza Sales SQL Project

## Project Overview

This project analyzes pizza sales data using SQL to uncover valuable business insights related to customer ordering behavior, product performance, sales trends, and revenue generation.

The analysis demonstrates practical SQL skills including joins, aggregations, Common Table Expressions (CTEs), subqueries, and window functions to solve real-world business problems using transactional sales data.

---

## Database Schema

The project consists of four main tables:

### pizza_types

Stores pizza information including name, category, and ingredients.

| Column        | Description                  |
| ------------- | ---------------------------- |
| pizza_type_id | Unique pizza type identifier |
| name          | Pizza name                   |
| category      | Pizza category               |
| ingredients   | Pizza ingredients            |

### pizzas

Stores pizza size and pricing information.

| Column        | Description             |
| ------------- | ----------------------- |
| pizza_id      | Unique pizza identifier |
| pizza_type_id | References pizza type   |
| size          | Pizza size              |
| price         | Pizza price             |

### orders

Stores customer order information.

| Column     | Description             |
| ---------- | ----------------------- |
| order_id   | Unique order identifier |
| order_date | Order date              |
| order_time | Order time              |

### order_details

Stores details of pizzas ordered.

| Column           | Description                   |
| ---------------- | ----------------------------- |
| order_details_id | Unique transaction identifier |
| order_id         | References order              |
| pizza_id         | References pizza              |
| quantity         | Quantity ordered              |

---

## Key SQL Concepts Covered

✔ Table Creation & Relationships

✔ Primary & Foreign Keys

✔ Joins (INNER JOIN)

✔ Aggregate Functions (SUM, COUNT, AVG)

✔ GROUP BY & ORDER BY

✔ Common Table Expressions (CTEs)

✔ Window Functions

✔ Revenue Analysis

✔ Time-Based Analysis

✔ Business Intelligence Reporting

---

## Solving Real-World Business Problems with SQL

### Problem 1: Retrieve the Total Number of Orders Placed

```sql
SELECT COUNT(*) as total_order FROM orders;
```

---

### Problem 2: Calculate the Total Revenue Generated from Pizza Sales

```sql
select sum(price) as totalrevenue from pizzas
```

---

### Problem 3: Identify the Highest-Priced Pizza

```sql
select price from pizzas
order by price desc
limit 1;
```

---

### Problem 4: Identify the Most Common Pizza Size Ordered

```sql
select size, count(size) as totalcount from pizzas
group by size
order by totalcount desc
limit 1;
```

---

### Problem 5: List the Top 5 Most Ordered Pizza Types Along with Their Quantities

```sql
select pt.name, sum(od.quantity) as totalquantity 
from pizza_types pt
join pizzas p
	on pt.pizza_type_id=p.pizza_type_id
join order_details od
	on p.pizza_id=od.pizza_id

group by pt.name
order by totalquantity desc
limit 5;
```

---

### Problem 6: Find the Total Quantity of Each Pizza Category Ordered

```sql
select pt.category, sum(od.quantity) as totalquantity 
from pizza_types pt
join pizzas p
	on pt.pizza_type_id=p.pizza_type_id
join order_details od
	on p.pizza_id=od.pizza_id

group by pt.category
order by totalquantity desc
```

---

### Problem 7: Determine the Distribution of Orders by Hour of the Day

```sql
select 
extract(hour from order_time) as hour,
count(order_id) as numberoforders
from orders
group by hour
order by hour;
```

---

### Problem 8: Find the Category-Wise Distribution of Pizzas

```sql
select 
pt.category,sum(od.quantity)as totalquantity
from pizza_types pt
join pizzas p
	on pt.pizza_type_id=p.pizza_type_id
join order_details od
	on p.pizza_id=od.pizza_id
group by pt.category
order by totalquantity desc;
```

---

### Problem 9: Calculate the Average Number of Pizzas Ordered Per Day

```sql
with daily_orders as (
select 
o.order_date,sum(od.quantity)as totalpizzas
from pizza_types pt
join pizzas p
	on pt.pizza_type_id=p.pizza_type_id
join order_details od
	on p.pizza_id=od.pizza_id
join orders o
 	on od.order_id=o.order_id
group by o.order_date
order by totalpizzas desc
)
select avg(totalpizzas) as avgpizzasperday
from daily_orders;
```

---

### Problem 10: Determine the Top 3 Most Ordered Pizza Types Based on Revenue

```sql
select 
pt.name,sum(od.quantity) as totalquantity, sum(od.quantity*p.price)as totalrevenue
from pizza_types pt
join pizzas p
	on pt.pizza_type_id=p.pizza_type_id
join order_details od
	on p.pizza_id=od.pizza_id
join orders o
 	on od.order_id=o.order_id
group by pt.name
order by totalrevenue desc
limit 3;
```

---

### Problem 11: Calculate the Percentage Contribution of Each Pizza Type to Total Revenue

```sql
select 
	pt.name, 
	round(sum(od.quantity*p.price),2)as revenue,
	round(sum(od.quantity*p.price)*100/
(
	select 
	sum(od.quantity*p.price)
	from order_details od
	join pizzas p
		on od.pizza_id=p.pizza_id
	),2)as percentage_contribution
from pizza_types pt
join pizzas p
	on pt.pizza_type_id=p.pizza_type_id
join order_details od
	on p.pizza_id=od.pizza_id
group by pt.name
order by percentage_contribution desc;
```

---

### Problem 12: Analyze the Cumulative Revenue Generated Over Time

```sql
with daily_revenue as (
select 
	o.order_date,
	sum(od.quantity*p.price) as revenue
	from orders o
	join order_details od
		on o.order_id=od.order_id
	join pizzas p
		on od.pizza_id=p.pizza_id
		group by o.order_date
	)
select 
order_date,
revenue,
sum(revenue)over (order by order_date)as cumulative_revenue
from daily_revenue;
```

---

### Problem 13: Determine the Top 3 Most Ordered Pizza Types Based on Revenue for Each Pizza Category

```sql
WITH pizza_revenue AS (
select 
pt.category,pt.name,sum(od.quantity*p.price)as revenue,
rank() over(
	partition by pt.category
	order by sum(od.quantity*p.price) desc
	)as rnk
from pizza_types pt
join pizzas p
	on pt.pizza_type_id=p.pizza_type_id
join order_details od
	on p.pizza_id=od.pizza_id
join orders o
 	on od.order_id=o.order_id
group by pt.category,pt.name 
)

SELECT
    category,
    name,
    revenue,
	rnk
FROM pizza_revenue
WHERE rnk <= 3;
```

---

## Key Insights

* Identified the most popular pizza types based on customer orders.
* Analyzed category-wise sales performance.
* Examined customer ordering patterns throughout the day.
* Calculated average daily pizza demand.
* Measured revenue generated by individual pizza types.
* Evaluated each pizza's contribution to overall revenue.
* Tracked cumulative revenue growth over time.
* Ranked top-performing pizzas within each category.

---

## Tools Used

* PostgreSQL
* SQL
* Relational Database Management Systems (RDBMS)
* Data Analysis

---

## Conclusion

This project demonstrates how SQL can be used to transform raw transactional sales data into actionable business insights. Through sales analysis, revenue tracking, and customer behavior exploration, the project highlights practical SQL techniques commonly used by Data Analysts and Business Intelligence professionals.
