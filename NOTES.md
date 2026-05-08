<div align="center">

# 🧠 SQL Interactive Quick Notes

> Extracted from all 11 PDFs — your go-to SQL reference!

![SQL Notes](https://img.shields.io/badge/SQL-Interactive%20Notes-blue?style=for-the-badge&logo=postgresql)
![Status](https://img.shields.io/badge/Status-Always%20Growing-brightgreen?style=for-the-badge)

</div>

---

## 📌 Table of Contents

- [⚡ SQL Categories Overview](#-sql-categories-overview)
- [🟢 Beginner — Core SQL](#-beginner--core-sql)
- [🟡 Intermediate — Joins & Aggregations](#-intermediate--joins--aggregations)
- [🔴 Advanced — Window Functions & CTEs](#-advanced--window-functions--ctes)
- [🏆 Interview Must-Know](#-interview-must-know)
- [🐛 Common Mistakes & Gotchas](#-common-mistakes--gotchas)
- [⚡ Performance Tips](#-performance-tips)
- [📋 SQL Syntax Cheat Reference](#-sql-syntax-cheat-reference)

---

## ⚡ SQL Categories Overview

| Category | Commands | Purpose |
|----------|----------|---------|
| **DDL** — Data Definition Language | `CREATE`, `ALTER`, `DROP`, `TRUNCATE` | Define/modify structure |
| **DML** — Data Manipulation Language | `SELECT`, `INSERT`, `UPDATE`, `DELETE` | Manipulate data |
| **DCL** — Data Control Language | `GRANT`, `REVOKE` | Control access |
| **TCL** — Transaction Control Language | `COMMIT`, `ROLLBACK`, `SAVEPOINT` | Manage transactions |

---

## 🟢 Beginner — Core SQL

<details>
<summary><b>📌 SELECT Statement — The Foundation</b></summary>

```sql
-- Basic SELECT
SELECT column1, column2 FROM table_name;

-- Select all columns
SELECT * FROM employees;

-- Select with alias
SELECT first_name AS "First Name", salary * 12 AS annual_salary
FROM employees;

-- Select distinct values (removes duplicates)
SELECT DISTINCT department FROM employees;
```

> 💡 **Tip:** Always specify column names instead of `SELECT *` in production — it's faster and future-proof.

</details>

---

<details>
<summary><b>📌 WHERE Clause — Filter Your Data</b></summary>

```sql
-- Basic filtering
SELECT * FROM employees WHERE salary > 50000;

-- Multiple conditions
SELECT * FROM employees
WHERE department = 'IT' AND salary > 60000;

-- OR condition
SELECT * FROM employees
WHERE department = 'HR' OR department = 'Finance';

-- NOT condition
SELECT * FROM employees WHERE NOT department = 'IT';

-- BETWEEN (inclusive on both ends)
SELECT * FROM employees WHERE salary BETWEEN 40000 AND 80000;

-- IN operator (multiple values)
SELECT * FROM employees
WHERE department IN ('IT', 'HR', 'Finance');

-- LIKE (pattern matching)
SELECT * FROM employees WHERE first_name LIKE 'A%';   -- starts with A
SELECT * FROM employees WHERE email LIKE '%@gmail%';  -- contains @gmail
SELECT * FROM employees WHERE phone LIKE '___-____';  -- exact pattern

-- IS NULL / IS NOT NULL
SELECT * FROM employees WHERE manager_id IS NULL;
SELECT * FROM employees WHERE email IS NOT NULL;
```

> 💡 **Tip:** `%` matches any sequence of characters. `_` matches exactly one character in `LIKE`.

</details>

---

<details>
<summary><b>📌 ORDER BY — Sort Results</b></summary>

```sql
-- Ascending (default)
SELECT * FROM employees ORDER BY last_name ASC;

-- Descending
SELECT * FROM employees ORDER BY salary DESC;

-- Multiple columns
SELECT * FROM employees ORDER BY department ASC, salary DESC;

-- Order by column position
SELECT first_name, last_name, salary FROM employees ORDER BY 3 DESC;
```

</details>

---

<details>
<summary><b>📌 LIMIT & OFFSET — Pagination</b></summary>

```sql
-- Get top 5 highest paid
SELECT * FROM employees ORDER BY salary DESC LIMIT 5;

-- Skip first 10, get next 5 (pagination)
SELECT * FROM employees ORDER BY employee_id LIMIT 5 OFFSET 10;

-- SQL Server equivalent
SELECT TOP 5 * FROM employees ORDER BY salary DESC;
```

> 💡 **Tip:** Use `LIMIT` + `OFFSET` for building paginated APIs or reports.

</details>

---

<details>
<summary><b>📌 INSERT, UPDATE, DELETE — CRUD Operations</b></summary>

```sql
-- INSERT single row
INSERT INTO employees (first_name, last_name, salary, department)
VALUES ('John', 'Doe', 75000, 'IT');

-- INSERT multiple rows
INSERT INTO employees (first_name, salary)
VALUES ('Alice', 60000), ('Bob', 55000), ('Charlie', 70000);

-- UPDATE
UPDATE employees SET salary = 80000 WHERE employee_id = 101;

-- UPDATE multiple columns
UPDATE employees
SET salary = salary * 1.10, department = 'Senior IT'
WHERE department = 'IT' AND years_experience > 5;

-- DELETE specific rows
DELETE FROM employees WHERE employee_id = 101;

-- DELETE all rows (keeps structure) — USE CAREFULLY!
DELETE FROM employees;

-- TRUNCATE (faster than DELETE, resets auto-increment)
TRUNCATE TABLE employees;
```

> ⚠️ **Warning:** Always use `WHERE` with `UPDATE` and `DELETE`! Without it, you'll modify/delete ALL rows.

</details>

---

## 🟡 Intermediate — Joins & Aggregations

<details>
<summary><b>📌 JOINs — Combining Tables</b></summary>

```sql
-- INNER JOIN (only matching rows from both tables)
SELECT e.first_name, d.department_name
FROM employees e
INNER JOIN departments d ON e.department_id = d.department_id;

-- LEFT JOIN (all rows from left table + matching from right)
SELECT e.first_name, d.department_name
FROM employees e
LEFT JOIN departments d ON e.department_id = d.department_id;
-- Returns NULL for department_name if no match

-- RIGHT JOIN (all rows from right table + matching from left)
SELECT e.first_name, d.department_name
FROM employees e
RIGHT JOIN departments d ON e.department_id = d.department_id;

-- FULL OUTER JOIN (all rows from both tables)
SELECT e.first_name, d.department_name
FROM employees e
FULL OUTER JOIN departments d ON e.department_id = d.department_id;

-- SELF JOIN (join table with itself)
SELECT e1.first_name AS employee, e2.first_name AS manager
FROM employees e1
INNER JOIN employees e2 ON e1.manager_id = e2.employee_id;

-- CROSS JOIN (cartesian product — all combinations)
SELECT e.first_name, p.project_name
FROM employees e CROSS JOIN projects p;
```

### Join Visualized:

```
INNER JOIN        LEFT JOIN         RIGHT JOIN        FULL JOIN
  ┌──┬──┐           ┌──┬──┐           ┌──┬──┐           ┌──┬──┐
  │  │██│           │██│██│           │  │██│           │██│██│
  │  │██│           │██│██│           │  │██│           │██│██│
  └──┴──┘           └──┴──┘           └──┴──┘           └──┴──┘
  Only overlap    All Left + overlap  All Right+overlap  Everything
```

> 💡 **Tip:** Use `LEFT JOIN` when you want to find rows that DON'T have a match (check for `NULL`).

</details>

---

<details>
<summary><b>📌 Aggregate Functions</b></summary>

```sql
-- COUNT
SELECT COUNT(*) FROM employees;                    -- count all rows
SELECT COUNT(DISTINCT department) FROM employees;  -- count unique depts

-- SUM, AVG, MIN, MAX
SELECT
    SUM(salary)         AS total_salary,
    AVG(salary)         AS avg_salary,
    MIN(salary)         AS min_salary,
    MAX(salary)         AS max_salary,
    COUNT(employee_id)  AS total_employees
FROM employees;

-- GROUP BY — aggregate by group
SELECT department, COUNT(*) AS headcount, AVG(salary) AS avg_sal
FROM employees
GROUP BY department;

-- HAVING — filter groups (like WHERE but for aggregated data)
SELECT department, COUNT(*) AS headcount
FROM employees
GROUP BY department
HAVING COUNT(*) > 5;

-- GROUP BY with ORDER BY
SELECT department, SUM(salary) AS total_spend
FROM employees
GROUP BY department
ORDER BY total_spend DESC;
```

> 💡 **Key Rule:** `WHERE` filters rows BEFORE grouping. `HAVING` filters groups AFTER grouping.

</details>

---

<details>
<summary><b>📌 Subqueries</b></summary>

```sql
-- Subquery in WHERE
SELECT * FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);

-- Subquery in FROM (derived table)
SELECT dept_avg.department, dept_avg.avg_salary
FROM (
    SELECT department, AVG(salary) AS avg_salary
    FROM employees
    GROUP BY department
) AS dept_avg
WHERE avg_salary > 60000;

-- Correlated subquery (references outer query)
SELECT e.first_name, e.salary
FROM employees e
WHERE e.salary > (
    SELECT AVG(salary) FROM employees
    WHERE department = e.department   -- references outer query
);

-- EXISTS (returns true if subquery has rows)
SELECT * FROM customers c
WHERE EXISTS (
    SELECT 1 FROM orders o WHERE o.customer_id = c.customer_id
);

-- NOT EXISTS (find customers with no orders)
SELECT * FROM customers c
WHERE NOT EXISTS (
    SELECT 1 FROM orders o WHERE o.customer_id = c.customer_id
);
```

</details>

---

<details>
<summary><b>📌 String Functions</b></summary>

```sql
-- Concatenation
SELECT CONCAT(first_name, ' ', last_name) AS full_name FROM employees;

-- Length
SELECT first_name, LENGTH(first_name) AS name_length FROM employees;

-- Upper/Lower case
SELECT UPPER(first_name), LOWER(last_name) FROM employees;

-- Substring
SELECT SUBSTRING(email, 1, 5) AS short_email FROM employees;

-- Trim whitespace
SELECT TRIM('  hello  ');           -- 'hello'
SELECT LTRIM('  hello');            -- 'hello'
SELECT RTRIM('hello  ');            -- 'hello'

-- Replace
SELECT REPLACE(phone, '-', '') AS clean_phone FROM employees;

-- LIKE patterns
SELECT * FROM employees WHERE first_name LIKE 'J%';   -- starts with J
SELECT * FROM employees WHERE email LIKE '%gmail%';   -- contains gmail
```

</details>

---

<details>
<summary><b>📌 Date Functions</b></summary>

```sql
-- Current date/time
SELECT NOW();             -- 2024-01-15 14:30:00
SELECT CURDATE();         -- 2024-01-15
SELECT CURTIME();         -- 14:30:00

-- Extract parts
SELECT YEAR(hire_date), MONTH(hire_date), DAY(hire_date) FROM employees;
SELECT DAYNAME(hire_date) FROM employees;   -- 'Monday'
SELECT MONTHNAME(hire_date) FROM employees; -- 'January'

-- Date arithmetic
SELECT DATEDIFF(NOW(), hire_date) AS days_employed FROM employees;
SELECT DATE_ADD(hire_date, INTERVAL 1 YEAR) AS anniversary FROM employees;

-- Format dates
SELECT DATE_FORMAT(hire_date, '%d-%m-%Y') AS formatted_date FROM employees;
```

</details>

---

## 🔴 Advanced — Window Functions & CTEs

<details>
<summary><b>📌 Window Functions — Analytics Without GROUP BY</b></summary>

```sql
-- ROW_NUMBER — unique row number per partition
SELECT
    first_name,
    department,
    salary,
    ROW_NUMBER() OVER (PARTITION BY department ORDER BY salary DESC) AS rank_in_dept
FROM employees;

-- RANK — same rank for ties, skips next number
SELECT
    first_name,
    salary,
    RANK() OVER (ORDER BY salary DESC) AS salary_rank
FROM employees;

-- DENSE_RANK — same rank for ties, NO skipping
SELECT
    first_name,
    salary,
    DENSE_RANK() OVER (ORDER BY salary DESC) AS salary_rank
FROM employees;

-- LEAD & LAG — access next/previous row
SELECT
    first_name,
    salary,
    LAG(salary)  OVER (ORDER BY hire_date) AS prev_salary,
    LEAD(salary) OVER (ORDER BY hire_date) AS next_salary
FROM employees;

-- Running total with SUM
SELECT
    order_date,
    amount,
    SUM(amount) OVER (ORDER BY order_date) AS running_total
FROM orders;

-- Moving average
SELECT
    order_date,
    amount,
    AVG(amount) OVER (ORDER BY order_date ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) AS moving_avg
FROM orders;
```

> 💡 **Key:** Window functions do NOT collapse rows like GROUP BY. Each row keeps its result!

</details>

---

<details>
<summary><b>📌 CTEs — Common Table Expressions</b></summary>

```sql
-- Basic CTE
WITH dept_avg AS (
    SELECT department, AVG(salary) AS avg_salary
    FROM employees
    GROUP BY department
)
SELECT e.first_name, e.salary, d.avg_salary
FROM employees e
JOIN dept_avg d ON e.department = d.department
WHERE e.salary > d.avg_salary;

-- Multiple CTEs
WITH
high_earners AS (
    SELECT * FROM employees WHERE salary > 80000
),
it_dept AS (
    SELECT * FROM employees WHERE department = 'IT'
)
SELECT h.first_name
FROM high_earners h
JOIN it_dept i ON h.employee_id = i.employee_id;

-- Recursive CTE (hierarchical data — org chart)
WITH RECURSIVE org_chart AS (
    -- Anchor: Start with CEO (no manager)
    SELECT employee_id, first_name, manager_id, 0 AS level
    FROM employees WHERE manager_id IS NULL

    UNION ALL

    -- Recursive: Get their reports
    SELECT e.employee_id, e.first_name, e.manager_id, oc.level + 1
    FROM employees e
    JOIN org_chart oc ON e.manager_id = oc.employee_id
)
SELECT * FROM org_chart ORDER BY level;
```

</details>

---

<details>
<summary><b>📌 Views — Virtual Tables</b></summary>

```sql
-- Create a view
CREATE VIEW active_employees AS
SELECT employee_id, first_name, last_name, department, salary
FROM employees
WHERE status = 'active';

-- Use the view like a table
SELECT * FROM active_employees WHERE department = 'IT';

-- Update a view
CREATE OR REPLACE VIEW active_employees AS
SELECT employee_id, first_name, department
FROM employees WHERE status = 'active';

-- Drop a view
DROP VIEW active_employees;
```

> 💡 **Use Views for:** Security (hide sensitive columns), reusable complex queries, simplifying reporting.

</details>

---

<details>
<summary><b>📌 Indexes — Speed Up Queries</b></summary>

```sql
-- Create index (speeds up WHERE, JOIN on indexed columns)
CREATE INDEX idx_last_name ON employees(last_name);

-- Unique index
CREATE UNIQUE INDEX idx_email ON employees(email);

-- Composite index (for queries filtering on both columns together)
CREATE INDEX idx_dept_salary ON employees(department, salary);

-- Drop index
DROP INDEX idx_last_name ON employees;

-- Show indexes
SHOW INDEX FROM employees;
```

### When to Index:
| ✅ Good for Indexes | ❌ Avoid Indexing |
|--------------------|-----------------|
| Columns in WHERE clause | Very small tables |
| Columns in JOIN conditions | Columns rarely used in queries |
| Columns in ORDER BY | Tables with frequent bulk inserts |
| High cardinality columns | Boolean/low-cardinality columns |

</details>

---

<details>
<summary><b>📌 Stored Procedures</b></summary>

```sql
-- Create stored procedure
DELIMITER //
CREATE PROCEDURE GetDeptEmployees(IN dept_name VARCHAR(50))
BEGIN
    SELECT first_name, last_name, salary
    FROM employees
    WHERE department = dept_name
    ORDER BY salary DESC;
END //
DELIMITER ;

-- Call the procedure
CALL GetDeptEmployees('IT');

-- Procedure with OUT parameter
DELIMITER //
CREATE PROCEDURE GetAvgSalary(IN dept VARCHAR(50), OUT avg_sal DECIMAL(10,2))
BEGIN
    SELECT AVG(salary) INTO avg_sal
    FROM employees WHERE department = dept;
END //
DELIMITER ;

-- Use it
CALL GetAvgSalary('IT', @result);
SELECT @result AS it_avg_salary;
```

</details>

---

## 🏆 Interview Must-Know

<details>
<summary><b>💼 Top 20 SQL Interview Questions & Answers</b></summary>

### Q1. Difference between DELETE, TRUNCATE, DROP?

| Feature | DELETE | TRUNCATE | DROP |
|---------|--------|----------|------|
| Removes | Specific rows | All rows | Entire table |
| WHERE clause | ✅ Yes | ❌ No | ❌ No |
| Rollback | ✅ Yes | ❌ No (DDL) | ❌ No |
| Resets auto-increment | ❌ No | ✅ Yes | N/A |
| Speed | Slower | Faster | Fastest |

---

### Q2. Difference between WHERE and HAVING?

```sql
-- WHERE filters ROWS before grouping
SELECT department, AVG(salary)
FROM employees
WHERE salary > 30000        -- filters individual rows first
GROUP BY department;

-- HAVING filters GROUPS after grouping
SELECT department, AVG(salary)
FROM employees
GROUP BY department
HAVING AVG(salary) > 60000; -- filters the grouped result
```

---

### Q3. What is a Primary Key vs Foreign Key?

```sql
-- Primary Key: unique identifier for each row, cannot be NULL
CREATE TABLE departments (
    dept_id INT PRIMARY KEY,
    dept_name VARCHAR(50) NOT NULL
);

-- Foreign Key: references Primary Key in another table
CREATE TABLE employees (
    emp_id INT PRIMARY KEY,
    dept_id INT,
    FOREIGN KEY (dept_id) REFERENCES departments(dept_id)
);
```

---

### Q4. Find the 2nd Highest Salary?

```sql
-- Method 1: LIMIT with OFFSET
SELECT DISTINCT salary FROM employees
ORDER BY salary DESC
LIMIT 1 OFFSET 1;

-- Method 2: Subquery
SELECT MAX(salary) FROM employees
WHERE salary < (SELECT MAX(salary) FROM employees);

-- Method 3: DENSE_RANK (scalable for Nth highest)
SELECT salary FROM (
    SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
    FROM employees
) ranked WHERE rnk = 2;
```

---

### Q5. Find duplicate records?

```sql
-- Find duplicates
SELECT email, COUNT(*) AS count
FROM employees
GROUP BY email
HAVING COUNT(*) > 1;

-- Delete duplicates (keep one)
DELETE FROM employees
WHERE employee_id NOT IN (
    SELECT MIN(employee_id)
    FROM employees
    GROUP BY email
);
```

---

### Q6. UNION vs UNION ALL?

```sql
-- UNION: removes duplicates (slower)
SELECT city FROM customers
UNION
SELECT city FROM suppliers;

-- UNION ALL: keeps all rows including duplicates (faster)
SELECT city FROM customers
UNION ALL
SELECT city FROM suppliers;
```

---

### Q7. What are ACID properties?

| Property | Meaning |
|----------|---------|
| **A**tomicity | All or nothing — entire transaction succeeds or fails |
| **C**onsistency | DB remains in valid state before and after transaction |
| **I**solation | Concurrent transactions don't interfere with each other |
| **D**urability | Committed transactions are permanently saved |

---

### Q8. Self Join — Find employees with same salary?

```sql
SELECT e1.first_name, e2.first_name, e1.salary
FROM employees e1
JOIN employees e2 ON e1.salary = e2.salary
AND e1.employee_id < e2.employee_id;  -- avoid duplicate pairs
```

---

### Q9. What is Normalization?

| Normal Form | Rule |
|------------|------|
| **1NF** | No repeating groups, atomic values |
| **2NF** | 1NF + no partial dependency on composite key |
| **3NF** | 2NF + no transitive dependency |
| **BCNF** | Stronger version of 3NF |

---

### Q10. Find employees who earn more than their manager?

```sql
SELECT e.first_name AS employee, e.salary,
       m.first_name AS manager, m.salary AS manager_salary
FROM employees e
JOIN employees m ON e.manager_id = m.employee_id
WHERE e.salary > m.salary;
```

</details>

---

## 🐛 Common Mistakes & Gotchas

<details>
<summary><b>⚠️ Mistakes Every Beginner Makes</b></summary>

### ❌ Mistake 1: Forgetting WHERE in UPDATE/DELETE
```sql
-- DANGER! Updates ALL rows
UPDATE employees SET salary = 100000;

-- CORRECT: Always use WHERE
UPDATE employees SET salary = 100000 WHERE employee_id = 101;
```

### ❌ Mistake 2: Using = for NULL comparisons
```sql
-- WRONG — NULL = NULL is always false in SQL!
SELECT * FROM employees WHERE manager_id = NULL;

-- CORRECT
SELECT * FROM employees WHERE manager_id IS NULL;
```

### ❌ Mistake 3: COUNT(*) vs COUNT(column)
```sql
COUNT(*)         -- counts ALL rows including NULLs
COUNT(column)    -- counts only NON-NULL values in that column
COUNT(DISTINCT column)  -- counts unique non-null values
```

### ❌ Mistake 4: Wrong JOIN type
```sql
-- If you want ALL customers even without orders → LEFT JOIN
-- INNER JOIN will hide customers with no orders!
SELECT c.name, o.total FROM customers c
LEFT JOIN orders o ON c.id = o.customer_id;
```

### ❌ Mistake 5: WHERE vs HAVING confusion
```sql
-- ❌ WRONG
SELECT dept, COUNT(*) FROM employees
HAVING salary > 50000   -- Cannot use non-aggregated column in HAVING

-- ✅ CORRECT
SELECT dept, COUNT(*) FROM employees
WHERE salary > 50000    -- Filter rows with WHERE
GROUP BY dept
HAVING COUNT(*) > 3;    -- Filter groups with HAVING
```

</details>

---

## ⚡ Performance Tips

<details>
<summary><b>🚀 SQL Query Optimization Best Practices</b></summary>

| Tip | Why |
|-----|-----|
| Use `SELECT column_names` not `SELECT *` | Avoids fetching unnecessary data |
| Add indexes on JOIN and WHERE columns | Dramatically speeds up lookups |
| Use `EXISTS` instead of `IN` for subqueries | `EXISTS` stops at first match |
| Avoid functions on indexed columns in WHERE | Prevents index usage |
| Use `LIMIT` for large result sets | Reduces data transfer |
| Avoid `SELECT DISTINCT` unless needed | Adds sorting overhead |
| Use JOINs instead of subqueries when possible | Usually faster |
| Write sargable queries | Enable index range scans |

### Sargable vs Non-Sargable:
```sql
-- ❌ Non-sargable (function on column — can't use index)
WHERE YEAR(hire_date) = 2023

-- ✅ Sargable (index can be used)
WHERE hire_date BETWEEN '2023-01-01' AND '2023-12-31'
```

### EXPLAIN Plan:
```sql
-- Check how MySQL executes your query
EXPLAIN SELECT * FROM employees WHERE department = 'IT';

-- Look for: type=ALL (full scan) → bad. type=ref/const → good!
```

</details>

---

## 📋 SQL Syntax Cheat Reference

```sql
/*━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  SQL COMPLETE QUERY ORDER OF EXECUTION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━*/

SELECT column_list                  -- 5. Select columns
FROM table_name                     -- 1. Get the table
JOIN another_table ON condition     -- 2. Join tables
WHERE filter_condition              -- 3. Filter rows
GROUP BY column                     -- 4. Group rows
HAVING group_filter                 -- 5. Filter groups
ORDER BY column ASC|DESC            -- 6. Sort results
LIMIT n OFFSET m;                   -- 7. Limit results

-- SQL execution order: FROM → JOIN → WHERE → GROUP BY → HAVING → SELECT → ORDER BY → LIMIT
```

---

<div align="center">

**📚 Back to [Main README](./README.md)**

*Keep practicing — SQL mastery comes with repetition!* 💪

[![Star this repo](https://img.shields.io/github/stars/vicky-tec/SQL-Mastery-Hub?style=social)](https://github.com/vicky-tec/SQL-Mastery-Hub)

</div>
