# SQL Cleaning: Intermediate / Advanced Notes

Realistic messy tables → what’s wrong → how to clean → SQL pattern.

---

## 1. TRIM / LTRIM / RTRIM

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

---

## 2. LEFT / RIGHT / SUBSTRING

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

## 3. REPLACE / REGEXP_REPLACE

### Messy table
| address |
|---------|
| 12 Main St. |
| 12 Main Street |
| 12 Main Str |

### Problem
- Inconsistent abbreviations  
- Need standardised “Street”  

### Cleaning
```sql
REPLACE(REPLACE(REPLACE(address, 'St.', 'Street'), 'Str', 'Street'), 'St', 'Street') AS address_clean
```

**Advanced:**
```sql
REGEXP_REPLACE(address, 'St\\.?|Str', 'Street') AS address_clean
```

---

## 4. UPPER / LOWER / PROPER (INITCAP)

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

## 5. CONCAT / CONCAT_WS

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

## 6. LPAD / RPAD (Padding)

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

## 7. CAST / CONVERT

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

---

## 8. COALESCE (NULL handling)

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

---

## 9. CASE WHEN (Category cleaning)

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
END AS status_clean
```

---

## 10. REGEXP functions (Advanced cleaning)

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

# Bonus: Real‑World Cleaning Scenarios

---

## Scenario A: Cleaning transaction IDs

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

## Scenario B: Cleaning dates stored as text

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

## Scenario C: Cleaning currency fields

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
