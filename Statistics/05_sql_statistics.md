# ✅ **05_sql_statistics.md**  

---

# **SQL Statistical Functions — Analyst Toolkit**

This file provides a complete reference for statistical calculations in SQL, including central tendency, spread, percentiles, IQR, z‑scores, and outlier detection. All examples use ANSI‑standard syntax where possible and reflect real‑world analytics workflows.

---

# **1. Central Tendency**

## **Mean (Average)**

```sql
SELECT AVG(column_name) AS mean_value
FROM table_name;
```

- Works in all major SQL engines  
- Sensitive to outliers  
- Best for symmetrical distributions  

---

## **Median (PERCENTILE_CONT)**  
ANSI‑standard median using window functions:

```sql
SELECT
    PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY column_name)
        OVER () AS median_value
FROM table_name;
```

### Notes:
- Works in PostgreSQL, SQL Server, Oracle, Snowflake  
- MySQL 8+ supports percentiles but not true MEDIAN  
- Robust to skew and outliers  

---

## **Mode (Frequency Count)**  
No built‑in MODE() in most SQL engines.

```sql
SELECT column_name
FROM table_name
GROUP BY column_name
ORDER BY COUNT(*) DESC
LIMIT 1;
```

### Notes:
- Simple frequency count  
- Returns most common value  
- Useful for categorical data  

---

# **2. Measures of Spread**

## **Standard Deviation (Population)**

```sql
SELECT STDDEV_POP(column_name) AS sd_population
FROM table_name;
```

### Notes:
- Use when you have **all data** (most business cases)  
- SQL Server equivalent: `STDEVP()`  

---

## **Standard Deviation (Sample)**

```sql
SELECT STDDEV_SAMP(column_name) AS sd_sample
FROM table_name;
```

### Notes:
- Use when working with **samples**  
- SQL Server equivalent: `STDEV()`  
- Uses n − 1 denominator (Bessel’s correction)  

---

## **Variance (Population)**

```sql
SELECT VAR_POP(column_name) AS variance_population
FROM table_name;
```

Units are squared (e.g., dollars², minutes²).  
Used internally, rarely interpreted directly.

---

## **Variance (Sample)**

```sql
SELECT VAR_SAMP(column_name) AS variance_sample
FROM table_name;
```

Sample version (n − 1 denominator).

---

## **Range**

```sql
SELECT MAX(column_name) - MIN(column_name) AS range_value
FROM table_name;
```

Simple spread measure, sensitive to outliers.

---

# **3. Percentiles & IQR**

## **Percentiles (P25, P50, P75)**

```sql
SELECT
    PERCENTILE_CONT(0.25) WITHIN GROUP (ORDER BY column_name) OVER () AS p25,
    PERCENTILE_CONT(0.50) WITHIN GROUP (ORDER BY column_name) OVER () AS p50,
    PERCENTILE_CONT(0.75) WITHIN GROUP (ORDER BY column_name) OVER () AS p75
FROM table_name;
```

### Why percentiles matter:
- Robust to skew  
- Useful for thresholds (P90, P95)  
- Foundation for IQR  
- Better than mean for heavy‑tailed data  

---

## **Interquartile Range (IQR)**

```sql
SELECT
    PERCENTILE_CONT(0.75) WITHIN GROUP (ORDER BY column_name) OVER ()
    -
    PERCENTILE_CONT(0.25) WITHIN GROUP (ORDER BY column_name) OVER ()
    AS iqr
FROM table_name;
```

### Why IQR matters:
- Measures middle 50%  
- Ignores extreme values  
- Best for skewed or heavy‑tailed data  

---

# **4. Z‑Scores in SQL**

Full example from your notes:

```sql
SELECT
    value,
    (value - avg_val) / std_val AS z_score
FROM (
    SELECT
        value,
        AVG(value) OVER () AS avg_val,
        STDDEV(value) OVER () AS std_val
    FROM table
) t;
```

### What this computes:
- Mean  
- Standard deviation  
- Z‑score per row  

### Why it’s useful:
- Anomaly detection  
- Standardisation  
- Comparing across scales  

---

# **5. Outlier Detection (IQR Method)**  
This is the most robust method for skewed or heavy‑tailed business data.

### **Step 1 — Compute P25, P75, and IQR**

```sql
WITH stats AS (
    SELECT
        PERCENTILE_CONT(0.25) WITHIN GROUP (ORDER BY column_name) OVER () AS p25,
        PERCENTILE_CONT(0.75) WITHIN GROUP (ORDER BY column_name) OVER () AS p75
    FROM table_name
    LIMIT 1
)
```

---

### **Step 2 — Apply Fences**

```sql
SELECT
    t.*
FROM
    table_name t,
    stats s
WHERE
    t.column_name < s.p25 - 1.5 * (s.p75 - s.p25)
    OR
    t.column_name > s.p75 + 1.5 * (s.p75 - s.p25);
```

### Interpretation:
- Inside fences → normal  
- Outside fences → outlier  
- Far outside (3×IQR) → extreme outlier  

### Why analysts use this:
- Works even when data is skewed  
- Doesn’t assume normality  
- More reliable than z‑scores for messy business data  

---

# **6. Analyst Notes & Best Practices**

### **Population vs Sample**
- Business datasets → population  
- Surveys/experiments → sample  

### **Mean vs Median**
- Normal → mean  
- Skewed → median  
- Heavy‑tailed → median  

### **SD vs IQR**
- Normal → SD  
- Skewed → IQR  
- Heavy‑tailed → IQR  

### **Z‑Scores vs IQR**
- Normal → z‑scores  
- Skewed → IQR  
- Heavy‑tailed → IQR  

### **SQL Engine Differences**
- PostgreSQL, Oracle, Snowflake → full percentile support  
- SQL Server → uses `PERCENTILE_CONT`  
- MySQL → percentile support varies  

---