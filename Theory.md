# SQL Order of Execution

## 🔹 Sabse important baat

SQL hum upar se niche likhte hain,
BUT execute niche se upar hota hai (almost)

---

## 🔹 Actual Execution Order

FROM → JOIN → WHERE → GROUP BY → HAVING → SELECT → DISTINCT → ORDER BY → LIMIT

👉 Shortcut: **F J W G H S D O L**

---

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

