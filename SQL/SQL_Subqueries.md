# Subqueries

---

## 1. What a Subquery Is

**Definition:**  
A subquery is a query nested inside another query. It can appear in:
- SELECT  
- WHERE  
- FROM  
- HAVING  

Subqueries may return:
- A single value (scalar)  
- One column, many rows (multi‑row)  
- Multiple columns (table subquery)  
- Row‑dependent results (correlated)  

---

## 2. Types of Subqueries

---

### A. Scalar Subqueries

**Description:**  
Returns exactly one row and one column. Commonly used for comparisons against aggregated values.

**Example:**
```sql
SELECT *
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);
```

**Use Cases:**  
- Comparisons against aggregates  
- KPIs  
- Threshold logic  

---

### B. Multi‑Row Subqueries

**Description:**  
Returns one column with multiple rows. Requires operators like `IN`, `ANY`, or `ALL`.

**Example:**
```sql
SELECT *
FROM employees
WHERE department_id IN (
    SELECT department_id
    FROM departments
    WHERE location = 'New York'
);
```

**Use Cases:**  
- Dynamic filtering  
- Matching IDs  

---

### C. Multi‑Column Subqueries

**Description:**  
Returns multiple columns. Often used with `IN`, `EXISTS`, or as a derived table.

**Example:**
```sql
SELECT *
FROM orders
WHERE (customer_id, order_date) IN (
    SELECT customer_id, MAX(order_date)
    FROM orders
    GROUP BY customer_id
);
```

**Use Cases:**  
- Latest record per ID  
- Composite key matching  

---

### D. Correlated Subqueries

**Description:**  
References the outer query and evaluates once per row. More powerful but slower.

**Example:**
```sql
SELECT e.name, e.salary
FROM employees e
WHERE e.salary > (
    SELECT AVG(salary)
    FROM employees
    WHERE department_id = e.department_id
);
```

**Use Cases:**  
- Group‑level comparisons  
- “Above department average” logic  
- Row‑specific filtering  

---

## 3. Where Subqueries Can Appear

---

### A. Subqueries in WHERE (Filtering)

```sql
SELECT *
FROM sales_agents
WHERE agency_fee > (
    SELECT AVG(agency_fee)
    FROM sales_agents
);
```

---

### B. Subqueries in SELECT (Inline Calculations)

```sql
SELECT
    employee_name,
    salary,
    (SELECT AVG(salary) FROM employees) AS avg_salary
FROM employees;
```

---

### C. Subqueries in FROM (Derived Tables)

```sql
SELECT department, avg_salary
FROM (
    SELECT department, AVG(salary) AS avg_salary
    FROM employees
    GROUP BY department
);
```

---

### D. Subqueries in HAVING (Aggregated Filtering)

```sql
SELECT department_id, COUNT(*)
FROM employees
GROUP BY department_id
HAVING COUNT(*) >
(
    SELECT AVG(cnt)
    FROM (
        SELECT COUNT(*) AS cnt
        FROM employees
        GROUP BY department_id
    )
);
```

---

## 4. Subqueries vs Joins

---

**Use a Subquery When:**
- You need a single value (scalar)  
- You need row‑specific logic (correlated)  
- You want cleaner, more readable logic  
- You want to avoid unnecessary joins  

**Use a Join When:**
- You need multiple columns  
- You need better performance on large datasets  
- You want to avoid row‑by‑row correlated execution  

---

## 5. BI‑Relevant Subquery Patterns

---

### A. Latest Record per ID

```sql
SELECT *
FROM customers c
WHERE updated_at = (
    SELECT MAX(updated_at)
    FROM customers
    WHERE customer_id = c.customer_id
);
```

---

### B. Customers With No Transactions (Anti‑Join)

```sql
SELECT *
FROM customers c
WHERE NOT EXISTS (
    SELECT 1
    FROM transactions t
    WHERE t.customer_id = c.customer_id
);
```

---

### C. Above Average in Group

```sql
SELECT *
FROM sales s
WHERE amount > (
    SELECT AVG(amount)
    FROM sales
    WHERE region = s.region
);
```

---

## 6. Subqueries — One‑Page Cheat Sheet

---

**Types**
- Scalar → returns 1 value  
- Multi‑row → returns 1 column, many rows  
- Multi‑column → returns table‑like output  
- Correlated → depends on outer query  

**Where They Appear**
- WHERE → filtering  
- SELECT → inline metrics  
- FROM → derived tables  
- HAVING → aggregated filtering  

**Operators**
- IN → multi‑row match  
- EXISTS / NOT EXISTS → presence/absence  
- ANY / SOME → compare to any value  
- ALL → compare to all values  

**When to Use**
- Need a dynamic list → IN  
- Need row‑specific logic → correlated  
- Need a single metric → scalar  
- Need a temporary table → FROM subquery  

**BI Patterns**
- Latest record per ID  
- Anti‑join (no transactions)  
- Above‑average in group  
- Composite key matching  

---

# ✅ Subqueries.md COMPLETE

Reply **“Next”** and I’ll send **FILE 3 — Window Functions.md** in the same formatting.
