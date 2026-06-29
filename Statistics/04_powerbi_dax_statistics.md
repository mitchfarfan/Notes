# ✅ **04_powerbi_dax_statistics.md**  


---

# **Power BI / DAX — Statistical Functions Toolkit**

This file provides a complete reference for statistical calculations in Power BI using DAX. It includes central tendency, spread, percentiles, IQR, z‑scores, and outlier detection — all structured for real‑world analytics.

---

# **1. Central Tendency**

## **Mean (Average)**

```DAX
MeanValue = AVERAGE(Table[Column])
```

- Most common measure  
- Sensitive to outliers  
- Works best for symmetrical distributions  

---

## **Median**

```DAX
MedianValue = MEDIAN(Table[Column])
```

- Robust to skew  
- Stable when outliers exist  
- Best for right‑skewed or heavy‑tailed data  

---

## **Mode (DAX workaround)**  
DAX has **no built‑in MODE()**, so you compute it manually.

```DAX
ModeValue =
VAR Summary =
    ADDCOLUMNS(
        VALUES(Table[Column]),
        "Count", CALCULATE(COUNT(Table[Column]))
    )
RETURN
MAXX(
    TOPN(1, Summary, [Count], DESC),
    Table[Column]
)
```

### Notes:
- Creates a table of unique values  
- Counts occurrences  
- Returns the most frequent value  
- Rarely needed in BI dashboards  

---

# **2. Measures of Spread**

## **Standard Deviation (Population)**

```DAX
SD_Population = STDEV.P(Table[Column])
```

Use when you have the **full dataset** (most business cases).

---

## **Standard Deviation (Sample)**

```DAX
SD_Sample = STDEV.S(Table[Column])
```

Use when working with **samples** (surveys, experiments).

---

## **Variance (Population)**

```DAX
Variance_Pop = VAR.P(Table[Column])
```

Units are squared (e.g., dollars²).  
Used internally, rarely interpreted directly.

---

## **Variance (Sample)**

```DAX
Variance_Sample = VAR.S(Table[Column])
```

Sample version (n − 1 denominator).

---

## **Range**

```DAX
RangeValue = MAX(Table[Column]) - MIN(Table[Column])
```

Simple spread measure, sensitive to outliers.

---

# **3. Percentiles & IQR**

## **Percentile (Inclusive)**

```DAX
P25 = PERCENTILEX.INC(Table, Table[Column], 0.25)
P50 = PERCENTILEX.INC(Table, Table[Column], 0.50)
P75 = PERCENTILEX.INC(Table, Table[Column], 0.75)
```

### Notes:
- Use **INC** (inclusive) — standard for business analytics  
- P50 = median  

---

## **Interquartile Range (IQR)**

```DAX
IQR =
PERCENTILEX.INC(Table, Table[Column], 0.75)
    - PERCENTILEX.INC(Table, Table[Column], 0.25)
```

### Why IQR matters:
- Robust to outliers  
- Best for skewed or heavy‑tailed data  
- Used for outlier detection  

---

# **4. Z‑Scores in DAX**

## **Z‑Score (Measure)**  
Use when you want dynamic z‑scores in visuals.

```DAX
ZScore =
DIVIDE(
    SELECTEDVALUE(Table[Column]) - AVERAGE(Table[Column]),
    STDEV.P(Table[Column])
)
```

---

## **Z‑Score (Calculated Column)**  
Use when you need row‑level z‑scores.

```DAX
ZScoreColumn =
(Table[Column] - CALCULATE(AVERAGE(Table[Column])))
    / CALCULATE(STDEV.P(Table[Column]))
```

### Notes:
- Calculated column = static  
- Measure = dynamic  
- Use population SD unless working with samples  

---

# **5. Outlier Detection (IQR Method)**

This is the most robust method for skewed or heavy‑tailed data.

### **Step 1 — Compute P25, P75, and IQR**

```DAX
P25 = PERCENTILEX.INC(Table, Table[Column], 0.25)
P75 = PERCENTILEX.INC(Table, Table[Column], 0.75)
IQR = [P75] - [P25]
```

---

### **Step 2 — Compute Fences**

```DAX
LowerFence = [P25] - 1.5 * [IQR]
UpperFence = [P75] + 1.5 * [IQR]
```

---

### **Step 3 — Flag Outliers**

```DAX
IsOutlier =
IF(
    Table[Column] < [LowerFence]
        || Table[Column] > [UpperFence],
    1,
    0
)
```

### Notes:
- Works even when data is skewed  
- More reliable than z‑scores for messy business data  
- Use 3×IQR for extreme outliers  

---

# **6. Analyst Notes & Best Practices**

### **Population vs Sample**
- Business datasets = population → use STDEV.P  
- Surveys/experiments = sample → use STDEV.S  

---

### **Mean vs Median**
- Normal → mean  
- Skewed → median  
- Heavy‑tailed → median  

---

### **SD vs IQR**
- Normal → SD  
- Skewed → IQR  
- Heavy‑tailed → IQR  

---

### **Z‑Scores vs IQR**
- Normal → z‑scores  
- Skewed → IQR  
- Heavy‑tailed → IQR  

---

### **DAX Quirks**
- MODE requires a workaround  
- Percentiles require `PERCENTILEX.INC`  
- Z‑scores require careful use of `SELECTEDVALUE`  
- Calculated columns are static — measures are dynamic  

---