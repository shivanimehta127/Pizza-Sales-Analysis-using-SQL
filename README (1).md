# Pizza Sales Analysis Using SQL

## Overview
This project explores the dynamic world of pizza sales through data analysis using SQL. By harnessing the power of structured queries, we aim to uncover valuable insights into sales patterns, customer preferences, and operational efficiencies in the pizza industry.

## Objective
The main goal is to analyze pizza sales data to extract actionable insights, optimize business processes, and improve decision-making. This includes exploring trends in revenue, customer preferences, and pizza performance across various dimensions.
## Feature
**Basic Analysis:**

⒈ Retrieve the total number of orders placed.

⒉ Calculate total revenue from pizza sales.

⒊ Identify the highest-priced pizza.

⒋ Determine the most commonly ordered pizza size.

⒌ List the top 5 most ordered pizza types along with their quantities.

**Intermediate Analysis:**

⒈ Perform table joins to determine the total quantity ordered for each pizza category.

⒉ Analyze order distribution across different hours of the day.

⒊ Discover category-wise pizza distribution.

⒋ Group data by date to calculate the daily average number of pizzas ordered.

⒌ Identify the top 3 pizza types based on revenue.

**Advanced Analysis:**

⒈ Calculate the percentage contribution of each pizza type to total revenue.

⒉ Examine cumulative revenue over time.

⒊ Identify the top 3 pizza types contributing to revenue within each category.

## 
**Project Title**: Pizza Sales Analysis

**Level**: Beginner  

**Database**: `pizzahut`

## 
This project is designed to demonstrate SQL skills and techniques typically used by data analysts to explore, clean, and analyze pizza sales data. The project involves setting up a pizza sales database, performing exploratory data analysis (EDA), and answering specific business questions through SQL queries. This project is ideal for those who are starting their journey in data analysis and want to build a solid foundation in SQL.


## Project Structure

### 1. Database Setup

- **Database Creation**: The project starts by creating a database named `pizzahut`.
- **Table Creation**: A table named `order_details`` is created to store the sales data. The table structure includes columns for order_details_id, order_id, pizza_id, quantity.
 
```sql
CREATE DATABASE pizzahut;

USE pizzahut;

CREATE TABLE order_details (
    order_details_id INT NOT NULL,
    order_id INT NOT NULL,
    pizza_id TEXT NOT NULL,
    quantity INT NOT NULL,
    PRIMARY KEY (order_details_id)
);
```


### 2. Data Analysis & Findings

The following SQL queries were developed to answer specific business questions:

1. **Write a SQL query to retrieve the total number of orders placed.**:
```sql
SELECT 
    COUNT(order_id) AS total_order
FROM
    orders;
```

2. **Write a SQL qurey calculate the total revenue generated from pizza sales.**:
```sql
SELECT 
    ROUND(SUM(order_details.quantity * pizzas.price),
            2) AS total_sales
FROM
    order_details
        JOIN
    pizzas ON pizzas.pizza_id = order_details.pizza_id;
```

3. **Write a SQL query to identify the highest-priced pizza.**:
```sql
SELECT 
    pizza_types.name, pizzas.price
FROM
    pizza_types
        JOIN
    pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
ORDER BY pizzas.price DESC
LIMIT 1;
```

4. **Write a SQL query to identify the most common pizza size ordered.**:
```sql
SELECT 
    pizzas.size,
    COUNT(order_details.order_details_id) AS order_count
FROM
    pizzas
        JOIN
    order_details ON pizzas.pizza_id = order_details.pizza_id
GROUP BY pizzas.size
ORDER BY order_count DESC;
```

5. **Write a SQL query to list the top 5 most ordered pizza types along with their quantities.**:
```sql
SELECT 
    pizza_types.name, SUM(order_details.quantity) AS quantity
FROM
    pizza_types
        JOIN
    pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
        JOIN
    order_details ON order_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.name
ORDER BY quantity DESC
LIMIT 5;
```

6. **Write a SQL query to join the necessary tables to find the total quantity of each pizza category ordered.**:
```sql
SELECT 
    pizza_types.category,
    SUM(order_details.quantity) AS quantity
FROM
    pizza_types
        JOIN
    pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
        JOIN
    order_details ON order_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.category
ORDER BY quantity DESC;
```

7. **Write a SQL query to determine the distribution of orders by hour of the day.**:
```sql
SELECT 
    HOUR(order_time) AS hour, COUNT(order_id) AS order_count
FROM
    orders
GROUP BY HOUR(order_time);
```

8. **Write a SQL query to join relevant tables to find the category-wise distribution of pizzas.**:
```sql
SELECT 
    category, COUNT(name)
FROM
    pizza_types
GROUP BY category;
```

9. **Write a SQL query to Group the orders by date and calculate the average number of pizzas ordered per day.**:
```sql
SELECT 
    ROUND(AVG(quantity), 0) AS avg_pizza_ordered_per_day
FROM
    (SELECT 
        orders.order_date, SUM(order_details.quantity) AS quantity
    FROM
        orders
    JOIN order_details ON orders.order_id = order_details.order_id
    GROUP BY orders.order_date) AS order_quantity;
```

10. **Write a SQL query to determine the top 3 most ordered pizza types based on revenue.
**:
```sql
SELECT 
    pizza_types.name,
    SUM(order_details.quantity * pizzas.price) AS revenue
FROM
    pizza_types
        JOIN
    pizzas ON pizzas.pizza_type_id = pizza_types.pizza_type_id
        JOIN
    order_details ON order_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.name
ORDER BY revenue DESC
LIMIT 3;
```

11. **Write a SQL query to calculate the percentage contribution of each pizza type to total revenue.
**:
```sql
SELECT 
    pizza_types.category,
    ROUND(SUM(order_details.quantity * pizzas.price) / (SELECT 
                    ROUND(SUM(order_details.quantity * pizzas.price),
                                2) AS total_sales
                FROM
                    order_details
                        JOIN
                    pizzas ON pizzas.pizza_id = order_details.pizza_id) * 100,
            2) AS revenue
FROM
    pizza_types
        JOIN
    pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
        JOIN
    order_details ON order_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.category
ORDER BY revenue DESC;
```

12. **Write a SQL query to analyze the cumulative revenue generated over time.
**:
```sql
select order_date,
sum(revenue) over(order by order_date) as cum_revenue 
from 
(select orders.order_date,
sum(order_details.quantity * pizzas.price) as revenue 
from order_details join pizzas 
on order_details.pizza_id = pizzas.pizza_id
join orders
on orders.order_id = order_details.order_id
group by orders.order_date) as sales;
```

13. **Write a SQL query to determine the top 3 most ordered pizza types based on revenue for each pizza category.
**:
```sql
select name, revenue from 
(select category, name, revenue,
rank() over(partition by category order by revenue desc) as rn
from
(select pizza_types.category, pizza_types.name,
sum((order_details.quantity) * pizzas.price) as revenue 
from pizza_types join pizzas
on pizza_types.pizza_type_id = pizzas.pizza_type_id
join order_details
on order_details.pizza_id = pizzas.pizza_id
group by pizza_types.category, pizza_types.name) as a) as b
where rn <= 3;
```

## SQL Techniques Used
**Aggregate Functions:** SUM, AVG, COUNT, etc., for deriving totals and averages.

**Joins:** To combine data from multiple tables and create meaningful relationships.

**Group By:** For segmenting data by time, categories, and other dimensions.

**Window Functions:** For cumulative calculations and ranking.

**Filtering and Sorting:** Using WHERE, HAVING, and ORDER BY clauses for precise querying.

## Key Outcomes

**This project enables businesses to:**
⦁ Gain deeper insight into customer preferences and purchasing behaviors.

⦁ Optimize inventory and resource management.

⦁ Improve overall operational efficiency and profitability.

## Technology Stack
⦁ **Database Management System (DBMS):** SQL-based relational database.

⦁ **Query Language:** SQL (Structured Query Language).

## Future Enhancements
⦁ Integration with visualization tools like Tableau or Power BI for graphical insights.

⦁ Automation of queries for real-time reporting.

⦁ Extension to include forecasting models using SQL and machine learning integration.

## 
This project serves as a comprehensive introduction to SQL for data analysts, covering database setup, data cleaning, exploratory data analysis, and business-driven SQL queries. The findings from this project can help drive business decisions by understanding sales patterns, customer behavior, and product performance.

![Pizza Sales Analysis Project 🥗🍕 - visual selection](https://github.com/user-attachments/assets/8e0c92c9-afe9-4df1-88aa-f4dbaf91372a)

