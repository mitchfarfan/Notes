# SQL Data Cleaning Toolkit — Intermediate to Advanced

A complete reference for real‑world SQL cleaning patterns used in BI, analytics, and data modelling.  
Includes messy examples → problems → cleaning patterns → advanced notes.

---

# 1. TRIM / LTRIM / RTRIM — Whitespace Cleaning

### Messy table
| customer_name |
|---------------|
| ' John Smith ' |
| ' Mary ' |
| ' David' |

### Problem
- Leading/trailing spaces  
- Inconsistent whitespace  

### Cleaning
```sql
TRIM(customer_name) AS clean_name
```

### Result
| clean_name |
|------------|
| John Smith |
| Mary |
| David |

### Additional Notes
- Combine with LOWER/UPPER for standardisation:
```sql
LOWER(TRIM(city)) AS city_clean
```

---

# 2. LEFT / RIGHT / SUBSTRING — Extracting Parts of Text

### Messy table
| phone |
|-------|
| -1029 |
| 0412-555-999 |
| 412555777 |

### Problem
- Extra characters  
- Need last 9 digits only  

### Cleaning
```sql
RIGHT(REGEXP_REPLACE(phone, '[^0-9]', ''), 9) AS phone_clean
```

### Result
| phone_clean |
|-------------|
| 412555123 |
| 412555999 |
| 412555777 |

---

# 3. REPLACE / REGEXP_REPLACE — Standardising Text

### Messy table
| address |
|---------|
| 12 Main St. |
| 12 Main Street |
| 12 Main Str |

### Problem
- Inconsistent abbreviations  
- Need standardised “Street”  

### Cleaning (simple)
```sql
REPLACE(REPLACE(REPLACE(address, 'St.', 'Street'), 'Str', 'Street'), 'St', 'Street') AS address_clean
```

### Cleaning (advanced)
```sql
REGEXP_REPLACE(address, 'St\\.?|Str', 'Street') AS address_clean
```

---

# 4. UPPER / LOWER / INITCAP — Casing Standardisation

### Messy table
| email |
|--------|
| JOHN.SMITH@GMAIL.COM |
| mary.jones@outlook.com |
| DaViD@Yahoo.Com |

### Problem
- Inconsistent casing  
- Emails should be lowercase  

### Cleaning
```sql
LOWER(email) AS email_clean
```

---

# 5. CONCAT / CONCAT_WS — Combining Fields Safely

### Messy table
| first_name | last_name |
|------------|-----------|
| John | Smith |
| Mary | NULL |
| David | Lee |

### Problem
- Need full name  
- NULL breaks normal CONCAT  

### Cleaning
```sql
CONCAT_WS(' ', first_name, last_name) AS full_name
```

---

# 6. LPAD / RPAD — Padding Values

### Messy table
| product_code |
|--------------|
| 1 |
| 22 |
| 333 |

### Problem
- Codes must be 5 digits  
- Left‑pad with zeros  

### Cleaning
```sql
LPAD(product_code, 5, '0') AS product_code_clean
```

### Result
| product_code_clean |
|--------------------|
| 00001 |
| 00022 |
| 00333 |

---

# 7. CAST / CONVERT — Fixing Data Types

### Messy table
| amount |
|--------|
| '12.50' |
| '9' |
| '15.00' |

### Problem
- Stored as text  
- Need numeric for calculations  

### Cleaning
```sql
CAST(amount AS DECIMAL(10,2)) AS amount_clean
```

### Additional Patterns
Convert text to integer:
```sql
CAST(age AS INT)
```

Convert text to date:
```sql
CAST(order_date AS DATE)
```

Convert numeric text with symbols:
```sql
CAST(REGEXP_REPLACE(amount_text, '[^0-9.]', '') AS DECIMAL(10,2))
```

---

# 8. COALESCE / NULLIF — NULL Handling

### Messy table
| phone |
|--------|
| NULL |
| 412555123 |
| NULL |

### Problem
- Missing values  
- Need placeholder  

### Cleaning
```sql
COALESCE(phone, 'Missing') AS phone_clean
```

### Additional Patterns
Turn placeholder into NULL:
```sql
NULLIF(age, 0) AS age_clean
```

---

# 9. CASE WHEN — Category Cleaning & Business Rules

### Messy table
| status |
|--------|
| C |
| Closed |
| closed |
| O |

### Problem
- Mixed categories  
- Need standardised values  

### Cleaning
```sql
CASE 
    WHEN status IN ('C', 'Closed', 'closed') THEN 'Closed'
    WHEN status IN ('O', 'Open') THEN 'Open'
    ELSE 'Unknown'
END AS status_clean
```

### Additional Examples
Categorising revenue bands:
```sql
CASE
    WHEN revenue < 1000 THEN 'Low'
    WHEN revenue < 5000 THEN 'Medium'
    ELSE 'High'
END AS revenue_band
```

---

# 10. REGEXP Functions — Advanced Cleaning

### Messy table
| name |
|------|
| John123 |
| Mary!@# |
| David_Lee |

### Problem
- Remove non‑letters  
- Keep spaces  

### Cleaning
```sql
REGEXP_REPLACE(name, '[^A-Za-z ]', '') AS name_alpha_only
```

---

# 11. Deduplication with ROW_NUMBER()

### Identify duplicates
```sql
ROW_NUMBER() OVER (
    PARTITION BY customer_id
    ORDER BY updated_at DESC
) AS rn
```

### Keep only latest record
```sql
WHERE rn = 1
```

---

# 12. Pivot / Unpivot — Structural Cleaning (Concept)

### Pivot (rows → columns)
```sql
SELECT *
FROM sales
PIVOT (
    SUM(amount) FOR quarter IN ('Q1','Q2','Q3','Q4')
);
```

### Unpivot (columns → rows)
```sql
SELECT *
FROM sales_unpivoted
UNPIVOT (
    amount FOR quarter IN (q1, q2, q3, q4)
);
```

---

# Bonus: Real‑World Cleaning Scenarios

---

## Scenario A — Cleaning Transaction IDs

### Messy table
| transaction_id |
|----------------|
| TX-00123 |
| tx00124 |
| TX_00125 |

### Goal
- Remove prefixes  
- Keep numeric part  
- Standardise to 5 digits  

### Solution
```sql
LPAD(REGEXP_REPLACE(transaction_id, '[^0-9]', ''), 5, '0') AS txn_clean
```

---

## Scenario B — Cleaning Dates Stored as Text

### Messy table
| date_text |
|-----------|
| 5/01/2024 |
| 5/01/2024 |
| Jan 5 2024 |

### Goal
- Convert to proper DATE  

### Solution
```sql
CAST(date_text AS DATE)
```

Or:
```sql
TO_DATE(date_text, 'DD-MM-YYYY')
```

---

## Scenario C — Cleaning Currency Fields

### Messy table
| price |
|--------|
| $12.50 |
| AUD 9 |
| 15 |

### Goal
- Remove symbols  
- Convert to numeric  

### Solution
```sql
CAST(REGEXP_REPLACE(price, '[^0-9\\.]', '') AS DECIMAL(10,2)) AS price_clean
```

---

# End of SQL Cleaning Toolkit
