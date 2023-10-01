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


**What is the total amount spent by each customer?**

Use: This information can help company segregate the customers based on their spending to plan separately for each segment.

Query:

SELECT s.userid, SUM(p.price) FROM sales as s INNER JOIN  product as p ON s.product_id = p.product_id GROUP BY userid Order by SUM(p.price) DESC;

Approach: 

1. Sales table shows each transaction of each user and product table shows product price for each product hence by joining (INNER JOIN) both these tables we listed price of product purchased in each row of sales table in its corresponding column.
2. Once we have joined table we use SUM aggregate function to sum price column and group them by userid so we can see sum of price spent by each user. At last we order result by highest to lowest total spent.

Result
| userid | total_spent |
| ------ | ----------- |
| 1      | 5230        |
| 3      | 4570        |
| 2      | 2510        |

Answer: 


**How many days has each cutomer visited?**
Use: This information is helpful to understand who are the most frequent users of the application.

Query:

SELECT userid, COUNT(DISTINCT created_date) AS visits FROM sales GROUP BY userid ORDER BY userid;

Approach:

1. We need the count of rows based on userid but we also need to ensure if a user is visiting more than once in one day we only consider it once hence we applied distinct on created_date column while counting dates and then grouped the result by user id and also ordered by user id.

Result
| userid | visits |
| ------ | ------ |
| 1      | 7      |
| 2      | 4      |
| 3      | 5      |



**What was the first product that was purchased by user?**
Use: We can see if there is any specific product that is attracting users to make their first purchase. If so we can try to push this product to newly targetted customers and it will have higher chances of conversion.



SELECT userid, product_id AS first_product_purchase FROM (
SELECT userid, product_id, RANK()OVER(PARTITION BY userid ORDER BY created_date) AS rnk FROM sales) AS t WHERE rnk = 1;

Approach: 
1. We ranked all purchases made by each user ascendingly ordered by created_date using RANK function
2. Once we got this ranked we created a subquery and fetched rows from inner table that had rank 1


| userid | first_product_purchase |
| ------ | ---------------------- |
| 1      | 1                      |
| 2      | 1                      |
| 3      | 1                      |


Conclusion: Product_id 1 is the only product that every new user buys. Company should focus on advertising this product to newly targetted users.
