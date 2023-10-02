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



**What was the most purchase product and how many times was it purchased by users?**


SELECT  product_id AS most_purch_product_id, userid, COUNT(product_id) AS purchase_count FROM SALES WHERE product_id IN (SELECT product_id FROM (SELECT product_id, COUNT(product_id) AS product_count, RANK()OVER(ORDER BY COUNT(product_id) DESC) AS rnk FROM SALES GROUP BY product_id) as t where rnk = 1) GROUP BY product_id, userid ORDER BY Product_id;

Approach:

1. We started by counting number of times each product appeared in sales table by count function and ranked them (most sold product was rank 1 least sold was rank 3) using rank function
2. We then created a subquery where our result table was used as inner table and outer query was fetching prodcut_id from inner query
3. We then created one more subquery where we passed the condition of product_id that we got from previous subquery which will give us columns (product_id, userid, count(product_id)) which are realted to most sold product's product_id.


| most_purch_product_id | userid | purchase_count |
| --------------------- | ------ | -------------- |
| 2                     | 1      | 3              |
| 2                     | 2      | 1              |
| 2                     | 3      | 3              |


**Which item was the most popular for each customer?**



SELECT userid, product_id AS most_purchased_product_id FROM (SELECT *, RANK()OVER(PARTITION BY userid ORDER BY prod_count DESC) AS rnk FROM (SELECT userid, product_id, COUNT(product_id) AS prod_count FROM SALES GROUP BY userid, product_id ORDER BY userid) AS T) AS t where rnk = 1;


Approach:

1. First we fetched how many times each user made purchase for each product
2. Then we ranked these purchases for each user
3. We then fetched userid and product_id from rows which were ranked 1

| userid | most_purchased_product_id |
| ------ | ------------------------- |
| 1      | 2                         |
| 2      | 3                         |
| 3      | 2                         | 



**Which item was purchase first by customer after they became member?**

SELECT userid, product_id AS first_product_after_membership FROM (SELECT *, RANK()OVER(PARTITION BY userid ORDER BY created_date) as rnk FROM (SELECT s.*, g.gold_signup_date  FROM sales AS s JOIN goldusers_signup AS g ON s.USERID = g.USERID WHERE created_date > gold_signup_date) AS l) AS m WHERE rnk = 1;


Approach:

1. First we joined sales and goldusers_signup table to only get data of those users who were gold members
2. During joining of table we also gave condition to ensure we only get those transactions of gold users which were on a later date from their purchase of gold membership because the question asks us to list their first transaction after getting membership.
3. We then ranked these transactions for each user based on created date (such that smallest date gets rank 1)
4. At last we only fecthed those rows which were ranked 1


| userid | first_product_after_membership |
| ------ | ------------------------------ |
| 1      | 3                              |
| 3      | 2                              |


**Which item was purchased just before the customer became a member?**


SELECT userid, product_id AS product_purch_justbefore_membership FROM (SELECT *, RANK()OVER(PARTITION BY userid ORDER BY created_date DESC) as rnk FROM (SELECT s.*, g.gold_signup_date  FROM sales AS s JOIN goldusers_signup AS g ON s.USERID = g.USERID WHERE created_date < gold_signup_date) AS l) AS m WHERE rnk = 1;


Approach:

1. First we joined sales and goldusers_signup table to only get data of those users who were gold members
2. During joining of table we also gave condition to ensure we only get those transactions of gold users which were on a prior date from their purchase of gold membership because the question asks us to list their last transaction before getting membership.
3. We then ranked these transactions for each user based on created date (such that most recent date gets rank 1)
4. At last we only fecthed those rows which were ranked 1


| userid | product_purch_justbefore_membership |
| ------ | ----------------------------------- |
| 1      | 2                                   |
| 3      | 2                                   |



**WHAT IS TOTAL ORDERS AND AMOUNT SPENT BY USER BEFORE BECOMING A GOLD USER?**




SELECT userid, COUNT(created_date) AS order_count, SUM(PRICE) AS amount_spent_before_membership 
FROM
(SELECT 
s.userid, s.created_date, g.gold_signup_date, p.price
FROM 
sales AS s 
JOIN 
goldusers_signup AS g
ON s.userid = g.userid AND created_date < gold_signup_date
JOIN
product AS p
ON p.product_id = s.product_id) AS v 
GROUP BY userid ORDER BY userid;



Approach: 

1. We joined sales and golduser_signup tables to get details of gold users and put a condition to only include those transactions which were on a date prior to gold membership dates
2. We then joined the result of earlier join with product table so we can fetch prices from product table
3. At last we did count & sum operation on price column grouped by userid so we can get **a**. Total order count before membership **b**. Sum spent by each user before membership

| userid | order_count_before_membership | amount_spent_before_membership |
| ------ | ----------------------------- | ------------------------------ |
| 1      | 5                             | 4030                           |
| 3      | 3                             | 2720                           |



If buying each product generates Zomato points using given table calculate following
1. Points earned by each user
2. For Which product most points have been given?

| Product_id | Money Spent | Points Given |
| ---------- | ----------- | ------------ |
| 1          | 5Rs         | 1            |
| 2          | 10Rs        | 5            |
| 3          | 5Rs         | 1            |



**Query for points earned by each user**
SELECT userid, SUM(points) AS points_earned FROM 
(SELECT s.userid, s.product_id, p.price, 
IF(s.product_id = 2,FLOOR((price/10)*5), FLOOR(price/5)) AS points
FROM sales AS s 
JOIN product AS p
ON s.product_id = p.product_id) AS r GROUP BY userid;


Approach:
1. Joined sales and product table to get price details listed for each product in corresponding columns 
2. Created a new column that calculates zomato points and it gets its values from price column based on given criteria inside IF function
3. Sum of points column and grouped by user id





| userid | points_earned |
| ------ | ------------- |
| 1      | 1829          |
| 3      | 1697          |
| 2      | 763           |

**Query for maxmimum points giving product**

SELECT product_id, total_points FROM (
SELECT *, RANK()OVER(ORDER BY total_points DESC) AS rnk FROM (SELECT product_id, SUM(points) total_points FROM 
(SELECT s.userid, s.product_id, p.price, 
IF(s.product_id = 2,FLOOR((price/10)*5), FLOOR(price/5)) AS points
FROM sales AS s
JOIN product AS p
ON s.product_id = p.product_id) AS r GROUP BY product_id) AS s) as u WHERE rnk =1;

Approach:
1. Joined sales and product table to get price details listed for each product in corresponding columns
2. Created a new column that calculates zomato points and it gets its values from price column based on given criteria inside IF function
3. Sum of points column and grouped by product_id
4. Ranked the result rows (highest point row gets rank 1)
5. Used select statment for row which has rank 1  


| product_id | total_points |
| ---------- | ------------ |
| 2          | 3045         |

