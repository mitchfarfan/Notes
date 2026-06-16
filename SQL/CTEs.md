# CTEs

---

## 1. What a CTE Is

**Definition:**  
A Common Table Expression (CTE) is a named, temporary result set defined with `WITH`. It improves readability, supports stepwise logic, and allows reuse within a query.

**Key Rule:**  
`ORDER BY` is not allowed inside a CTE unless paired with:
- LIMIT  
- TOP  
- OFFSET  

CTEs behave like logical tables — and tables do not have inherent order.

**Correct Example:**
```sql
WITH top_sales AS (
    SELECT *
    FROM sales
    ORDER BY amount DESC
    LIMIT 10
)
SELECT * FROM top_sales;
```

**Incorrect Example:**
```sql
WITH bad_cte AS (
    SELECT *
    FROM sales
    ORDER BY amount DESC -- ❌ Not allowed without LIMIT/TOP
)
SELECT * FROM bad_cte;
```

---

## 2. The Two Structural Types

---

### A. Non‑Recursive CTE

**Used for:**
- Pre‑filtering  
- Pre‑aggregating  
- Cleaning data  
- Simplifying joins  
- Reusing logic  

---

### B. Recursive CTE

**Used for:**
- Hierarchies  
- Parent/child trees  
- Sequences  

**Structure:**
1. Anchor query  
2. Recursive query  
3. Implicit termination  

**Example:**
```sql
WITH RECURSIVE numbers AS (
    SELECT 1 AS n
    UNION ALL
    SELECT n + 1
    FROM numbers
    WHERE n < 10
)
SELECT * FROM numbers;
```

---

## 3. Usage Patterns

---

### A. Stand‑Alone CTE

One CTE → one final SELECT.

---

### B. Multi‑Layer / Chained CTEs

A linear pipeline:

clean → filter → aggregate → final

**Example:**
```sql
WITH step1 AS (...),
     step2 AS (SELECT ... FROM step1),
     step3 AS (SELECT ... FROM step2)
SELECT * FROM step3;
```

---

## 4. When to Use a CTE vs Subquery

---

| Scenario | Use CTE | Use Subquery |
|---------|---------|--------------|
| Multi‑step logic | ✔ | |
| Reuse logic | ✔ | |
| Improve readability | ✔ | |
| One‑off inline filter | | ✔ |
| Performance critical | Sometimes | ✔ |

---

## 5. Common Interview Patterns

---

### A. Pre‑Aggregation

```sql
WITH totals AS (
    SELECT customer_id, SUM(amount) AS total_spend
    FROM sales
    GROUP BY customer_id
)
SELECT *
FROM totals
WHERE total_spend > 1000;
```

---

### B. Deduplication (Latest Record)

```sql
WITH ranked AS (
    SELECT *,
           ROW_NUMBER() OVER (PARTITION BY id ORDER BY updated_at DESC) AS rn
    FROM customers
)
SELECT *
FROM ranked
WHERE rn = 1;
```

---

### C. Hierarchy (Recursive)

```sql
WITH RECURSIVE org AS (
    SELECT id, manager_id, name, 0 AS level
    FROM employees
    WHERE manager_id IS NULL

    UNION ALL

    SELECT e.id, e.manager_id, e.name, o.level + 1
    FROM employees e
    JOIN org o ON e.manager_id = o.id
)
SELECT * FROM org;
```

---

## 6. Multiple Chained CTEs — What Interviewers Want

---

**Key Points:**
- Each CTE is evaluated once  
- Each step has a single purpose  
- CTEs replace deeply nested subqueries  
- Avoid over‑chaining (5+ steps when 2–3 would do)

---

## 7. Senior‑Level Best Practices

---

- Name CTEs by purpose (`cleaned_sales`, not `cte1`)  
- One transformation per CTE  
- Avoid unnecessary recursion  
- CTEs improve readability, not performance  
- Reuse logic instead of repeating it  

---

## 8. Rapid‑Fire Drills

---

### A. Multiple Chained CTEs

Think: clean → filter → aggregate → final.

---

### B. Correlated Subqueries

“A correlated subquery references the outer query and evaluates per row.”

---

### C. ORDER BY in CTEs

Invalid unless paired with LIMIT/TOP/OFFSET.

---

### D. Recursive CTEs

“Anchor row → recursive step → repeat until empty.”

---

## 9. One‑Page Summary

---

- CTEs break complex logic into readable steps  
- Correlated subqueries evaluate per row  
- ORDER BY inside CTE only allowed with LIMIT/TOP/OFFSET  
- Recursive CTEs = anchor + recursive member  

---

## 10. Mini Practice Questions

---

**Q1:** Why use multiple chained CTEs?  
**A:** To break logic into clear, single‑purpose steps.

**Q2:** What makes a subquery correlated?  
**A:** It references the outer query.

**Q3:** Why can’t you use ORDER BY inside a CTE?  
**A:** CTEs have no inherent order.

**Q4:** What are the two components of a recursive CTE?  
**A:** Anchor and recursive member.

---

