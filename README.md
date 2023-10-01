# Analytics

**Zomato App Data Analysis**

This project is made using MYSQL that answers several business related questions by runnning analysis on given data. 
These answers will help business take informed decisions backed by data. 
With each question listed below it is also mentioned why and how the answer of that question is helping business.

This Project dataset has four tables
1. Product Table: Has list of products sold
2. Users Table: Has users information
3. Gold_signup Table: Has information of those users having gold membership
4. Sales Table: Has sales details listed

**Product Table**
| product_id | product_name | price |
| ---------- | ------------ | ----- |
| 1          | p1           | 980   |
| 2          | p2           | 870   |
| 3          | p3           | 330   |

**Users Table**
| userid | signup_date  |
| ------ | ------------ |
| 1      | 09-02-2014   |
| 2      | 01-15-2015   |
| 3      | 04-11-2014   |

**Gold_Signup Table**
| userid | gold_signup_date |
| ------ | ---------------- |
| 1      | 09-22-2017       |
| 3      | 04-21-2017       |

**Sales Table**
| userid | created_date | product_id |
| ------ | ------------ | ---------- |
| 1      | 04-19-2017   | 2          |
| 3      | 12-18-2019   | 1          |
| 2      | 07-20-2020   | 3          |
| 1      | 10-23-2019   | 2          |
| 1      | 03-19-2018   | 3          |
| 3      | 12-20-2016   | 2          |
| 1      | 11-09-2016   | 1          |
| 1      | 05-20-2016   | 3          |
| 2      | 09-24-2017   | 1          |
| 1      | 03-11-2017   | 2          |
| 1      | 03-11-2016   | 1          |
| 3      | 11-10-2016   | 1          |
| 3      | 12-07-2017   | 2          |
| 3      | 12-15-2016   | 2          |
| 2      | 11-08-2017   | 2          |
| 2      | 09-10-2018   | 3          |


What is the total amount spent by each customer?

Use: This information can help company segregate the customers based on their spending to plan separately for each segment.

Query:

SELECT s.userid, SUM(price) FROM (SELECT s.userid, p.price FROM sales as s INNER JOIN  product as p ON s.product_id = p.product_id) as t GROUP BY userid;

Approach: 

1. Joined sales and product table. Sales table shows transaction for each user and product table shows product price hence by joining both these tables we listed price of product purchased in each row in its corresponding column.
2. Once we have joined table we use SUM aggregate function to sum price column and group them by userid so we can see sum of price spent by each user.


