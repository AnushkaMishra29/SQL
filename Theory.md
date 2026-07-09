# SQL Order of Execution

SQL hum upar se niche likhte hain, BUT execute niche se upar hota hai (almost)
## 🔹 Actual Execution Order

FROM → JOIN → WHERE → GROUP BY → HAVING → SELECT → DISTINCT → ORDER BY → LIMIT

👉 Shortcut: **F J W G H S D O L**

## 🔹 Step-by-Step

### 1️⃣ FROM + JOIN

* Tables load hoti hain
* Joins lagte hain
* Ek virtual table banta hai
* FROM users u
JOIN orders o ON u.id = o.user_id

---

### 2️⃣ WHERE

* Row filtering hoti hai
  ❌ Aggregate use nahi kar sakte
WHERE age > 18
---

### 3️⃣ GROUP BY

* Rows ko groups mein convert karna
GROUP BY user_id

---

### 4️⃣ HAVING

* Groups ko filter karta hai (post aggregation)
HAVING COUNT(*) > 5
👉 Difference:

WHERE → rows filter
HAVING → groups filter

---

### 5️⃣ SELECT

Columns choose hote hain
Expressions evaluate hote hain
SELECT user_id, COUNT(*) as total_orders
👉 Isliye aliases yahan bante hain
---

### 6️⃣ DISTINCT

* Duplicate rows remove

---

### 7️⃣ ORDER BY

* Final result sort hota hai
ORDER BY total_orders DESC
👉 Yahan alias use kar sakte ho (kyunki SELECT ho chuka hai)

---

### 8️⃣ LIMIT

* Final output trim
LIMIT 10

---

SELECT user_id, COUNT(*) as orders
FROM orders
WHERE status = 'completed'
GROUP BY user_id
HAVING COUNT(*) > 5
ORDER BY orders DESC
LIMIT 10;

👉 Execution actually:

FROM orders
WHERE status filter
GROUP BY user_id
HAVING filter
SELECT columns
ORDER BY
LIMIT

New functions:
julianday for dates--date nikalne ke liye floating point mein
substr(date,startno, end no) year month wagera nikalne ke liye

# Do we have to use a group by if we use aggregate function in select?
Yes — in SQL, whenever you use an aggregate function like:

MAX()
MIN()
COUNT()
SUM()
AVG()

along with normal columns in SELECT, you usually need GROUP BY.

# WINDOW FUNCTIONS:
FORMAT- WINDOW_FUNC() OVER (PARTITION BY COL ORDER BY COL)
FOR RUNNING TOTALS USE FORMAT: WINDOW_FUNC OVER(PARTITION BY COL ORDER BY COL RANGE BETWEEN UNBOUNDED PRECEDENT CURRENT ROW)

DIFF WINDOW FUNC: AGGREGATE AND WINDOW FUNCTION
AGGREGATE: SUN, COUNT, AVG ETC
WINDOW FUNC- RANK() DENSE_RANK() NTILE(N) ROW_NUMBER() 

# WINDOW FUNCTIONS THAT DO AND DO NOT TAKE PARAMETERS:
Ranking functions mostly do not take parameters:
ROW_NUMBER(), RANK(), DENSE_RANK()

Behavior comparison functions take parameters:
LAG(column, offset, default)
LEAD(column, offset, default)

Value-picking functions take parameters:
FIRST_VALUE(column)
LAST_VALUE(column)
NTH_VALUE(column, n)

Segmentation function takes parameter:
NTILE(n)

Aggregate window functions take column parameters:
SUM(column), AVG(column), COUNT(column), MIN(column), MAX(column)


# Diff between ROWS AND RANGE IN WINDOW FUNCTION:

ROWS = counts physical rows.

RANGE = counts based on ORDER BY value.
Rows with same ORDER BY value are included together.

If ORDER BY values are unique, ROWS and RANGE may look same.
If ORDER BY values are duplicated, results can differ.

For analyst interviews, prefer ROWS for running totals and moving averages because it is more predictable.
Eg: 

SUM(revenue) OVER (
    ORDER BY order_date
    ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
) AS running_revenue

OUTPUT: <img width="912" height="276" alt="image" src="https://github.com/user-attachments/assets/21108083-cf4c-4dfe-ba4f-ab7695bc7d72" />

SUM(revenue) OVER (
    ORDER BY order_date
    RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
) AS running_revenue

OUTPUT: <img width="872" height="272" alt="image" src="https://github.com/user-attachments/assets/c9e910d1-1c78-4715-a941-e33478bb3e0f" />

<img width="1007" height="375" alt="image" src="https://github.com/user-attachments/assets/e0782439-c466-4fb7-b6aa-b54ea8acd87f" />

<img width="712" height="165" alt="image" src="https://github.com/user-attachments/assets/970e34aa-e3cd-4033-b117-fe6d30c2bd1b" />


 



