# Window Functions

---

## 1. Window Functions — Core Concept

**Definition:**  
Window functions perform calculations across rows **without collapsing them**.  
This is the key difference from `GROUP BY`.

They enable:
- Rankings  
- Running totals  
- Moving averages  
- Time‑based comparisons  
- Deduplication  
- “Latest record” logic  
- Percentiles  
- Segmentation  

**Interview‑Ready Explanation:**  
“Window functions let me calculate metrics across rows without losing detail. Using PARTITION BY and ORDER BY, I can build rankings, running totals, moving averages, and time‑based comparisons.”

---

## 2. OVER() — The Window Definition

**Meaning:**  
Defines the set of rows the function can see.

Example:
```sql
SUM(amount) OVER()
```

Interpretation:  
Calculate the SUM across all rows, but keep every row in the result.

---

## 3. PARTITION BY — Grouping Inside the Window

**Purpose:**  
Creates groups inside the window without collapsing rows.

Example:
```sql
SUM(amount) OVER(PARTITION BY customer_id)
```

Interpretation:  
Total per customer, but keep every row.

---

## 4. ORDER BY Inside the Window — Sequencing

**Purpose:**  
Defines the sequence for:
- Rankings  
- Running totals  
- Time‑based logic  

Example:
```sql
SUM(amount) OVER(PARTITION BY customer_id ORDER BY date)
```

---

## 5. ROW_NUMBER — Deduping and Latest Record Logic

**Purpose:**  
Select the latest or best row per entity.

Example:
```sql
ROW_NUMBER() OVER(
    PARTITION BY customer_id
    ORDER BY updated_at DESC
)
```

**Use Cases:**
- Latest customer record  
- Latest product price  
- Latest employee status  
- Deduplication  

**Pattern:**
1. Apply ROW_NUMBER  
2. Filter `WHERE rn = 1`

Example:
```sql
WITH ranked AS (
    SELECT *,
           ROW_NUMBER() OVER(
               PARTITION BY customer_id
               ORDER BY updated_at DESC
           ) AS rn
    FROM customers
)
SELECT *
FROM ranked
WHERE rn = 1;
```

---

## 6. RANK and DENSE_RANK — Ranking Patterns

### RANK()
- Allows ties  
- Skips numbers  
- Pattern: 1, 2, 2, 4  

### DENSE_RANK()
- Allows ties  
- No gaps  
- Pattern: 1, 2, 2, 3  

Example:
```sql
SELECT
    salesperson,
    sales_amount,
    RANK() OVER (ORDER BY sales_amount DESC) AS sales_rank
FROM sales;
```

---

## 7. LAG and LEAD — Time‑Based Comparisons

**Purpose:**  
Access previous or next row values within a partition.

Example:
```sql
LAG(sales, 1) OVER(PARTITION BY store ORDER BY date)
```

**Use Cases:**
- Previous day  
- Previous month  
- Previous transaction  
- Variance / deltas  

Example with delta:
```sql
SELECT
    store,
    date,
    sales,
    LAG(sales, 1) OVER(
        PARTITION BY store
        ORDER BY date
    ) AS prev_day_sales,
    sales - LAG(sales, 1) OVER(
        PARTITION BY store
        ORDER BY date
    ) AS day_change
FROM daily_sales;
```

---

## 8. Running Totals — Cumulative Metrics

**Pattern:**
```sql
SUM(sales) OVER(
    PARTITION BY store
    ORDER BY date
    ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
)
```

**Interpretation:**  
Start at the first row in the partition and accumulate forward.

**Use Cases:**
- Running total per customer  
- Running total per product  
- Running total per region  
- Running total per category  

---

## 9. Moving Averages — Rolling Windows

**Template:**
```sql
<AGG>(metric) OVER (
    PARTITION BY <dimension>
    ORDER BY <date_column>
    ROWS BETWEEN <n> PRECEDING AND CURRENT ROW
)
```

Where:
- `<AGG>` = AVG, SUM, COUNT, MIN, MAX  
- `<dimension>` = store, customer, product, region  
- `<n>` = window size minus 1  

### 7‑Day Rolling Average
```sql
AVG(sales) OVER(
    PARTITION BY store
    ORDER BY date
    ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
)
```

### 30‑Day Rolling Average
```sql
AVG(sales) OVER(
    PARTITION BY store
    ORDER BY date
    ROWS BETWEEN 29 PRECEDING AND CURRENT ROW
)
```

### Rolling Sum
```sql
SUM(sales) OVER(
    PARTITION BY store
    ORDER BY date
    ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
)
```

---

## 10. Window Frame Types

---

### A. ROWS Frame (Most Common)

**Example:**
```
ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
```

Use for:
- Daily data  
- Evenly spaced time series  

---

### B. RANGE Frame

**Example:**
```
RANGE BETWEEN INTERVAL '6' DAY PRECEDING AND CURRENT ROW
```

Use for:
- Time gaps  
- Irregular timestamps  

---

### C. UNBOUNDED Frames

**Example:**
```
ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
```

Use for:
- Running totals  
- Cumulative revenue  
- YTD metrics  

---

## 11. Why Window Functions Matter for BI

**They enable:**
- Time intelligence  
- Ranking logic  
- Deduplication  
- Cumulative metrics  
- Rolling windows  
- Variance vs previous period  
- Replicating DAX behaviour in SQL  

This is the difference between junior SQL and mid‑level BI analytics.

---

## 12. One‑Page Window Functions Cheat Sheet

**Core Idea:**  
Window functions calculate across rows without collapsing them.

**Syntax:**
```
<function>() OVER (
    PARTITION BY ...
    ORDER BY ...
    ROWS BETWEEN ... AND ...
)
```

**Most Important Functions:**
- ROW_NUMBER — dedupe, latest record  
- RANK / DENSE_RANK — ranking  
- LAG / LEAD — previous/next row  
- SUM() OVER — running totals  
- AVG() OVER — moving averages  
- COUNT() OVER — running counts  

---

## 13. Quick Quiz

**Q1:** What does PARTITION BY do?  
**A:** Creates groups inside the window.

**Q2:** What does ORDER BY inside OVER() do?  
**A:** Defines sequence for running totals and rankings.

**Q3:** ROW_NUMBER vs RANK?  
**A:** ROW_NUMBER has no ties; RANK allows ties.

**Q4:** What does LAG return?  
**A:** A value from a previous row.

**Q5:** Best function for “latest record per customer”?  
**A:** ROW_NUMBER() with ORDER BY updated_at DESC.

**Q6:** GROUP BY vs window functions?  
**A:** GROUP BY collapses rows; window functions keep them.

**Q7:** What does UNBOUNDED PRECEDING mean?  
**A:** Start at the first row.

**Q8:** RANK vs DENSE_RANK?  
**A:** DENSE_RANK doesn’t skip numbers.

**Q9:** What is a moving average?  
**A:** AVG() OVER with a sliding frame.

**Q10:** Can ORDER BY exist without PARTITION BY?  
**A:** Yes — it applies to the entire dataset.
