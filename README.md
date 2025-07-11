# LeetCode SQL 50 Questions

This repository is a curated collection of 50 SQL questions from LeetCode that I solved as part of my SQL interview preparation.


---

## 🔗 Basic Select

---

### **1757. Recyclable and Low Fat Products**

```sql
SELECT product_id
FROM Products
WHERE low_fats = 'Y' AND recyclable = 'Y';
```

---

### **584. Find Customer Referee**

```sql
SELECT name
FROM Customer
WHERE referee_id != 2 OR referee_id IS NULL;
```

---

### **595. Big Countries**

```sql
SELECT name, population, area
FROM World
WHERE area >= 3000000 OR population >= 25000000;
```

---

### **1148. Article Views I**

```sql
SELECT DISTINCT author_id as id
FROM Views
WHERE viewer_id >= 1
AND author_id = viewer_id
ORDER BY author_id;
```

---

### **1683. Invalid Tweets**

```sql
SELECT tweet_id 
FROM Tweets
WHERE LENGTH(content) > 15;
```

---

## 🔗 Basic Joins

---

### **1378. Replace Employee ID With The Unique Identifier**

```sql
SELECT 
    u.unique_id, 
    e.name
FROM Employees e 
LEFT JOIN EmployeeUNI u
ON e.id = u.id;
```

---

### **1068. Product Sales Analysis I**

```sql
SELECT p.product_name, s.year, s.price
FROM Sales s INNER JOIN Product p
ON s.product_id=p.product_id;
```

---

### **1581. Customer Who Visited but Did Not Make Any Transactions**

```sql
SELECT v.customer_id, COUNT(*) AS count_no_trans 
FROM Visits v LEFT JOIN Transactions t
ON v.visit_id = t.visit_id
WHERE t.transaction_id IS NULL
GROUP BY v.customer_id;
```

---

### **197. Rising Temperature**

```sql
SELECT w1.id
FROM Weather w1 JOIN Weather w2
ON w1.recordDate = w2.recordDate + 1
WHERE w1.temperature > w2.temperature;
```

---

### **1661. Average Time of Process per Machine**

```sql
SELECT machine_id, process_id, 
ROUND(AVG(timestamp - start_time),3) AS processing_time
FROM (
    SELECT machine_id, process_id, timestamp,
           FIRST_VALUE(timestamp) OVER (PARTITION BY machine_id, process_id ORDER BY activity_type DESC) AS start_time
    FROM Activity
) AS sub
WHERE activity_type = 'end'
GROUP BY machine_id, process_id;
```

```sql
SELECT s.machine_id , ROUND(AVG(e.timestamp - s.timestamp), 3) AS processing_time
FROM Activity s JOIN Activity e
ON s.machine_id= e.machine_id
WHERE s.process_id = e.process_id
AND s.activity_type ='start'
AND e.activity_type ='end'
GROUP BY s.machine_id
```
---

### **577. Employee Bonus**

```sql
SELECT e.name, b.bonus
FROM Employee e LEFT JOIN Bonus b
ON e.empId = b.empId
WHERE b.bonus < 1000 OR b.bonus IS NULL;
```

---

### **1280. Students and Examinations**

```sql
SELECT s.student_id, s.student_name, sub.subject_name,
COUNT(e.subject_name) AS attended_exams
FROM Students s
CROSS JOIN Subjects sub
LEFT JOIN Examinations e
ON s.student_id = e.student_id AND sub.subject_name = e.subject_name
GROUP BY s.student_id, s.student_name, sub.subject_name
ORDER BY s.student_id, sub.subject_name;

```

---

### **570. Managers with at Least 5 Direct Reports**

```sql
SELECT name
FROM Employee
WHERE id IN (
    SELECT managerId
    FROM Employee
    GROUP BY managerId
    HAVING COUNT(*) >= 5
);
```

---

### **1934. Confirmation Rate**

```sql
SELECT s.user_id, 
       ROUND(SUM(CASE WHEN c.action='confirmed' THEN 1 ELSE 0 END)/COUNT(*), 2) AS confirmation_rate
FROM Signups s
LEFT JOIN Confirmations c
ON s.user_id = c.user_id
GROUP BY s.user_id;
```

---

## Basic Aggregate Functions

---

### **620. Not Boring Movies**

```sql
SELECT * FROM Cinema 
WHERE description != 'boring' AND id % 2 = 1
ORDER BY rating DESC;
```

---

### **1251. Average Selling Price**

```sql
SELECT p.product_id, 
       ROUND(SUM(price * units) / SUM(units), 2) AS average_price
FROM Prices p 
JOIN UnitsSold u 
ON p.product_id = u.product_id AND u.purchase_date BETWEEN p.start_date AND p.end_date
GROUP BY p.product_id;
```

---

### **1075. Project Employees I**

```sql
SELECT p.project_id, ROUND(AVG(e.experience_years), 2) AS average_years
FROM Project p 
JOIN Employee e 
ON p.employee_id = e.employee_id
GROUP BY p.project_id;
```

---

### **1633. Percentage of Users Attended a Contest**

```sql
SELECT u.user_id, 
       ROUND(COUNT(a.user_id)*100.0 / (SELECT COUNT(*) FROM Users), 2) AS percentage
FROM Users u 
LEFT JOIN Register a 
ON u.user_id = a.user_id
GROUP BY u.user_id;
```
```sql
SELECT r.contest_id, ROUND(COUNT(DISTINCT(r.user_id))*100/(select count(*) from Users),2) AS percentage
FROM Users u JOIN Register r
ON u.user_id=r.user_id
GROUP BY r.contest_id
ORDER BY percentage DESC, r.contest_id ASC;
```
---

### **1211. Queries Quality and Percentage**

```sql
SELECT query_name, 
       ROUND(AVG(rating/position), 2) AS quality, 
       ROUND(SUM(CASE WHEN rating < 3 THEN 1 ELSE 0 END)*100.0 / COUNT(*), 2) AS poor_query_percentage
FROM Queries
WHERE query_name IS NOT NULL
GROUP BY query_name;
```
```sql
SELECT query_name,
ROUND( AVG(rating/position),2) AS quality,
ROUND(SUM(CASE WHEN rating < 3 THEN 1 ELSE 0 END) * 100.0 / COUNT(*), 2) AS poor_query_percentage 
FROM Queries
GROUP BY query_name
```
---

### **1193. Monthly Transactions I**

```sql
SELECT DATE_FORMAT(trans_date, '%Y-%m') AS month, 
       country, 
       COUNT(*) AS trans_count, 
       SUM(CASE WHEN state = 'approved' THEN 1 ELSE 0 END) AS approved_count, 
       SUM(amount) AS trans_total_amount, 
       SUM(CASE WHEN state = 'approved' THEN amount ELSE 0 END) AS approved_amount
FROM Transactions
GROUP BY month, country;
```
```sql
SELECT 
    TO_CHAR(trans_date, 'YYYY-MM') AS month,
    country,
    COUNT(*) AS trans_count,
    COUNT(CASE WHEN state = 'approved' THEN 1 END) AS approved_count,
    SUM(amount) AS trans_total_amount,
    SUM(CASE WHEN state = 'approved' THEN amount ELSE 0 END) AS approved_total_amount
FROM 
    Transactions
GROUP BY 
    TO_CHAR(trans_date, 'YYYY-MM'), country;
```

---

### **1174. Immediate Food Delivery II**

```sql
SELECT ROUND(SUM(order_date = customer_pref_delivery_date) * 100.0 / COUNT(*), 2) AS immediate_percentage
FROM Delivery
WHERE (customer_id, order_date) IN (
    SELECT customer_id, MIN(order_date)
    FROM Delivery
    GROUP BY customer_id
);
```

```sql
SELECT 
    ROUND(
        COUNT(CASE 
                  WHEN d.order_date = d.customer_pref_delivery_date 
                  THEN 1 
             END) * 100.0 / COUNT(*), 
        2
    ) AS immediate_percentage
FROM 
    Delivery d
JOIN (
    SELECT 
        customer_id, 
        MIN(order_date) AS first_order_date
    FROM 
        Delivery
    GROUP BY 
        customer_id
) f
ON d.customer_id = f.customer_id 
   AND d.order_date = f.first_order_date;
```
OR
```sql
WITH first_orders AS (
  SELECT 
    customer_id,
    MIN(order_date) AS first_order_date
  FROM 
    Delivery
  GROUP BY 
    customer_id
)
SELECT 
  ROUND(
    COUNT(CASE 
             WHEN d.order_date = d.customer_pref_delivery_date THEN 1 
          END) * 100.0 / COUNT(*),
    2
  ) AS immediate_percentage
FROM 
  Delivery d
JOIN 
  first_orders f
  ON d.customer_id = f.customer_id 
 AND d.order_date = f.first_order_date;
```
---

### **550. Game Play Analysis IV**

```sql
SELECT player_id, MIN(event_date) AS first_login
FROM (
    SELECT player_id, event_date, 
           RANK() OVER (PARTITION BY player_id ORDER BY event_date) AS rk
    FROM Activity
) AS t
WHERE rk = 1
GROUP BY player_id;
```
```sql
SELECT 
    ROUND(
        COUNT(DISTINCT CASE 
            WHEN a.event_date = f.first_login_date + 1 THEN a.player_id 
        END) * 1.0 
        / COUNT(DISTINCT a.player_id), 2
    ) AS fraction
FROM Activity a
JOIN (
    SELECT 
        player_id, 
        MIN(event_date) AS first_login_date
    FROM Activity
    GROUP BY player_id
) f ON a.player_id = f.player_id;
```
---

### 🔗 Sorting and Grouping

---

#### 2356. Number of Unique Subjects Taught by Each Teacher

```sql
SELECT teacher_id, COUNT(DISTINCT subject_id) AS cnt
FROM Teacher
GROUP BY teacher_id;
```

---

#### 1141. User Activity for the Past 30 Days I

* Use `DATEDIFF()` to filter last 30 days.
* Use `DISTINCT` to count unique users per day.

```sql
SELECT activity_date as day,
       COUNT(DISTINCT user_id) as active_users
FROM Activity
WHERE activity_date BETWEEN DATE_SUB('2019-07-27', INTERVAL 29 DAY) AND '2019-07-27'
GROUP BY activity_date
```
```sql
SELECT 
    TO_CHAR(activity_date, 'YYYY-MM-DD') AS day,
    COUNT(DISTINCT user_id) AS active_users
FROM Activity
WHERE activity_date BETWEEN DATE '2019-06-28' AND DATE '2019-07-27'
GROUP BY activity_date;
```
---

#### 1070. Product Sales Analysis III

```sql
SELECT  s.product_id, s.year AS first_year, s.quantity, s.price
FROM Sales s
JOIN
(SELECT MIN(year) AS first_year ,product_id FROM Sales group by product_id) y
on y.product_id=s.product_id
and s.year=y.first_year
```

---

#### 596. Classes With at Least 5 Students

```sql
SELECT class
FROM Courses
GROUP BY class
HAVING COUNT(student) >= 5;
```
---

#### 1729. Find Followers Count

**Thought Process:**

* Group by `user_id` and count how many times they appear as `follower_id`.


```sql
SELECT user_id, COUNT(follower_id) AS followers_count
FROM Followers
GROUP BY user_id;
```

---

#### 28. 619. Biggest Single Number

```sql
SELECT MAX(num) AS num
FROM MyNumbers
GROUP BY num
HAVING COUNT(*) = 1;
```

---

#### 29. 1045. Customers Who Bought All Products

```sql
SELECT customer_id
FROM Customer
GROUP BY customer_id
HAVING COUNT(DISTINCT product_key) = ( SELECT COUNT(DISTINCT product_key) FROM Product)
```

---

### 📘 Advanced Select and Joins

---

#### 1731. The Number of Employees Which Report to Each Employee

```sql
SELECT e1.employee_id, e1.name, COUNT(*) AS reports_count, ROUND(AVG(e2.age)) AS average_age
FROM Employees e1 JOIN Employees e2
ON e1.employee_id=e2.reports_to
GROUP BY e1.employee_id,e1.name
ORDER BY e1.employee_id
```

---

#### 1789. Primary Department for Each Employee

```sql
SELECT employee_id, department_id
FROM Employee
WHERE primary_flag='Y' 
UNION
SELECT employee_id, department_id
FROM Employee
WHERE employee_id in (SELECT employee_id
FROM Employee GROUP BY employee_id
HAVING count(*)=1)
```

---

#### 610. Triangle Judgement

```sql
SELECT x, y, z,
       CASE
           WHEN x + y > z AND x + z > y AND y + z > x THEN 'Yes'
           ELSE 'No'
       END AS triangle
FROM Triangle
```
---

#### 180. Consecutive Numbers

```sql
WITH Lagged AS (
    SELECT id,
        num,
        LAG(num,1) OVER(ORDER BY id) AS prev_num,
        LEAD(num,1) OVER(ORDER BY id) AS next_num
    FROM Logs

) SELECT DISTINCT(num) AS ConsecutiveNums 
FROM Lagged where num=prev_num AND num= next_num
```

---

####  1164. Product Price at a Given Date

```sql
WITH RankedPrices AS (
  SELECT
    product_id,
    new_price,
    ROW_NUMBER() OVER (
      PARTITION BY product_id
      ORDER BY change_date DESC
    ) AS rn
  FROM Products
  WHERE change_date <= '2019-08-16'
)

SELECT
  p.product_id,
  COALESCE(r.new_price, 10) AS price
FROM (
    SELECT DISTINCT product_id FROM Products
) p
LEFT JOIN RankedPrices r
  ON p.product_id = r.product_id AND r.rn = 1;

)
```

---

#### 1204. Last Person to Fit in the Bus

```sql
WITH WeightTracking AS (
    SELECT 
        person_name,
        weight,
        turn,
        SUM(weight) OVER (ORDER BY turn) AS cum_weight
    FROM Queue
), 
Filtered AS (
    SELECT * 
    FROM WeightTracking
    WHERE cum_weight <= 1000
), 
Ranked AS (
    SELECT Filtered.*,
           ROW_NUMBER() OVER (ORDER BY cum_weight DESC) AS rn
    FROM Filtered
)
SELECT person_name
FROM Ranked 
WHERE rn = 1;

```
---

#### 1907. Count Salary Categories

```sql
SELECT 'Low Salary' AS category, SUM(CASE WHEN income < 20000 THEN 1 ELSE 0 END) AS accounts_count 
FROM Accounts
UNION ALL
SELECT 'Average Salary' AS category, SUM(CASE WHEN income BETWEEN 20000 AND 50000 THEN 1 ELSE 0 END) 
FROM Accounts
UNION ALL
SELECT 'High Salary' AS category, SUM(CASE WHEN income > 50000 THEN 1 ELSE 0 END) 
FROM Accounts;

-----
-- Step 1: Group actual data into categories and count
WITH SalaryCategory AS (
    SELECT 
        CASE 
            WHEN income < 20000 THEN 'Low Salary'
            WHEN income BETWEEN 20000 AND 50000 THEN 'Average Salary'
            ELSE 'High Salary'
        END AS category
    FROM Accounts
),
CategoryCounts AS (
    SELECT category, COUNT(*) AS accounts_count
    FROM SalaryCategory
    GROUP BY category
),
-- Step 2: Ensure all 3 categories are returned
AllCategories AS (
    SELECT 'Low Salary' AS category
    UNION ALL
    SELECT 'Average Salary'
    UNION ALL
    SELECT 'High Salary'
)
-- Final step: Left join to fill 0s
SELECT 
    a.category,
    COALESCE(c.accounts_count, 0) AS accounts_count
FROM AllCategories a
LEFT JOIN CategoryCounts c
ON a.category = c.category;

```
---

### 📘 Subqueries

---

#### 1978. Employees Whose Manager Left the Company

```sql
SELECT employee_id
FROM Employees
WHERE salary < 30000
  AND manager_id IS NOT NULL
  AND manager_id NOT IN (SELECT employee_id FROM Employees)
ORDER BY employee_id;
```

---

#### 626. Exchange Seats

```sql
SELECT 
    CASE 
        WHEN MOD(id, 2) = 1 AND id + 1 <= (SELECT MAX(id) FROM Seat) THEN id + 1
        WHEN MOD(id, 2) = 0 THEN id - 1
        ELSE id
    END AS id,
    student
FROM Seat
ORDER BY id
```

---

#### 1341. Movie Rating

```sql
WITH MovieRatingCount AS (
    SELECT u.name AS name, COUNT(*) AS rating_count
    FROM Users u 
    JOIN MovieRating m ON u.user_id = m.user_id
    GROUP BY u.name
),
TopUser AS (
    SELECT name
    FROM (
        SELECT name, rating_count
        FROM MovieRatingCount
        ORDER BY rating_count DESC, name
    )
    WHERE ROWNUM = 1
),
FebAvgRating AS (
    SELECT mo.title AS movie_title, AVG(mr.rating) AS avg_rating
    FROM Movies mo 
    JOIN MovieRating mr ON mo.movie_id = mr.movie_id
    WHERE TO_CHAR(mr.created_at, 'YYYY-MM') = '2020-02'
    GROUP BY mo.title
),
TopMovie AS (
    SELECT movie_title
    FROM (
        SELECT movie_title, avg_rating
        FROM FebAvgRating
        ORDER BY avg_rating DESC, movie_title
    )
    WHERE ROWNUM = 1
)
SELECT name AS results FROM TopUser
UNION ALL
SELECT movie_title AS results FROM TopMovie;

```

---

#### 1321. Restaurant Growth

```sql
WITH DailyTotals AS (
  SELECT
    visited_on,
    SUM(amount) AS amount
  FROM Customer
  GROUP BY visited_on
),
Rolling7Days AS (
  SELECT
    d1.visited_on,
    SUM(d2.amount) AS amount,
    ROUND(AVG(d2.amount), 2) AS average_amount
  FROM DailyTotals d1
  JOIN DailyTotals d2
    ON d2.visited_on BETWEEN d1.visited_on - 6 AND d1.visited_on
  GROUP BY d1.visited_on
)
SELECT 
  TO_CHAR(visited_on, 'YYYY-MM-DD') AS visited_on,
  amount,
  average_amount
FROM Rolling7Days
WHERE visited_on >= (SELECT MIN(visited_on) + 6 FROM DailyTotals)
ORDER BY visited_on;


```

---

#### 602. Friend Requests II: Who Has the Most Friends

```sql
WITH AllConnections AS (
    SELECT requester_id AS user_id, accepter_id AS friend_id FROM RequestAccepted
    UNION ALL
    SELECT accepter_id AS user_id, requester_id AS friend_id FROM RequestAccepted
),
FriendCounts AS (
    SELECT user_id AS id, COUNT(DISTINCT friend_id) AS num
    FROM AllConnections
    GROUP BY user_id
),
MaxCount AS (
    SELECT MAX(num) AS max_num FROM FriendCounts
)
SELECT id, num
FROM FriendCounts
WHERE num = (SELECT max_num FROM MaxCount);


```

---

#### 585. Investments in 2016

```sql
WITH duplicateTIV15 AS(
SELECT tiv_2015
FROM Insurance
GROUP BY tiv_2015
HAVING count(*)>1
),
Uniqueloc AS (
    SELECT lat,lon
    FROM Insurance
    Group by lat,lon
    Having Count(*)=1

) SELECT ROUND(SUM(tiv_2016),2) AS tiv_2016
FROM Insurance
WHERE tiv_2015 IN (SELECT tiv_2015 From duplicateTIV15)
AND (lat,lon) IN (SELECT lat,lon FROM Uniqueloc)


```

---

#### 185. Department Top Three Salaries

```sql
WITH highestSalary AS(
    SELECT d.name AS department,
    e.name AS Employee,
    e.salary AS Salary,
    DENSE_RANK() OVER (PARTITION BY e.departmentId ORDER BY e.salary DESC) AS rank_in_dept   
    FROM Employee e JOIN Department d
    ON e.departmentId = d.id     
)
SELECT Department,Employee,Salary
FROM highestSalary
WHERE rank_in_dept <=3
```

---

### 📘 Advanced String Functions / Regex / Clause

---

#### 1667. Fix Names in a Table

```sql
SELECT user_id,
        UPPER(SUBSTR(name,1,1))||LOWER(SUBSTR(name,2)) AS name
FROM Users
ORDER BY user_id;

```

---

#### 1527. Patients With a Condition

```sql
SELECT patient_id, patient_name, conditions
FROM Patients
WHERE REGEXP_LIKE(conditions, '(^| )DIAB1')
ORDER BY patient_id;'
```

---

#### 196. Delete Duplicate Emails

```sql
DELETE FROM Person
WHERE id NOT IN (
    SELECT MIN(id)
    FROM Person
    GROUP BY email
)
```

---

#### 176. Second Highest Salary

```sql
SELECT MAX(salary) AS SecondHighestSalary
FROM Employee
WHERE salary < (SELECT MAX(salary) FROM Employee);

-----
WITH RankedSalaries AS (
    SELECT DISTINCT salary,
           ROW_NUMBER() OVER (ORDER BY salary DESC) AS rn
    FROM Employee
)
SELECT salary AS SecondHighestSalary
FROM RankedSalaries
WHERE rn = 2;

```

---

#### 1484. Group Sold Products By The Date

```sql
WITH DistinctSales AS (
    SELECT DISTINCT sell_date, product
    FROM Activities
)
SELECT 
    TO_CHAR(sell_date, 'YYYY-MM-DD') AS sell_date,
    COUNT(product) AS num_sold,
    LISTAGG(product, ',') WITHIN GROUP (ORDER BY product) AS products
FROM DistinctSales
GROUP BY sell_date
ORDER BY sell_date;

```

---

#### 1327. List the Products Ordered in a Period

```sql
SELECT 
    p.product_name, 
    SUM(o.unit) AS unit
FROM 
    Products p
JOIN 
    Orders o ON p.product_id = o.product_id
WHERE 
    o.order_date BETWEEN TO_DATE('2020-02-01', 'YYYY-MM-DD') 
                    AND TO_DATE('2020-02-29', 'YYYY-MM-DD')
GROUP BY 
    p.product_name
HAVING 
    SUM(o.unit) >= 100
ORDER BY 
    p.product_name;

```

---

#### 1517. Find Users With Valid E-Mails

```sql
SELECT user_id, name, mail
FROM Users
WHERE mail REGEXP '^[a-zA-Z][a-zA-Z0-9_.-]*@leetcode[.]com$'
```

---




