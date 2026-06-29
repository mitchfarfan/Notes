# ✅ **06_python_statistics.md**  

---

# **Python Statistical Functions — Analyst Toolkit**  
*(pandas + numpy)*

Python is the most flexible environment for statistical analysis. Pandas handles dataframes, numpy handles numeric operations, and together they provide everything needed for profiling, anomaly detection, modelling, and exploratory analysis.

This file includes:

- Central tendency  
- Spread  
- Percentiles  
- IQR  
- Z‑scores  
- Outlier detection  
- Analyst notes  

All examples use pandas and numpy — the standard tools for analysts.

---

# **1. Central Tendency**

## **Mean (Average)**

```python
df['col'].mean()
```

- Returns a float  
- Sensitive to outliers  
- Best for symmetrical distributions  

---

## **Median**

```python
df['col'].median()
```

- Robust to skew  
- Stable when outliers exist  
- Best for right‑skewed or heavy‑tailed data  

---

## **Mode**

```python
df['col'].mode()
```

### Notes:
- Returns a **Series** (multiple modes possible)  
- Useful for categorical data  

---

# **2. Measures of Spread**

## **Standard Deviation (Population)**

```python
df['col'].std(ddof=0)
```

- `ddof=0` → population SD  
- Use when you have the full dataset (most business cases)

---

## **Standard Deviation (Sample)**

```python
df['col'].std(ddof=1)
```

- `ddof=1` → sample SD  
- Default in pandas  
- Use for surveys, experiments, statistical sampling  

---

## **Variance (Population)**

```python
df['col'].var(ddof=0)
```

Units are squared (e.g., dollars², minutes²).  
Used internally, rarely interpreted directly.

---

## **Variance (Sample)**

```python
df['col'].var(ddof=1)
```

Sample version (n − 1 denominator).

---

## **Range**

```python
df['col'].max() - df['col'].min()
```

Simple spread measure, sensitive to outliers.

---

# **3. Percentiles & IQR**

## **Percentile (25th, 50th, 75th)**

```python
import numpy as np

np.percentile(df['col'], 25)
np.percentile(df['col'], 50)
np.percentile(df['col'], 75)
```

### Notes:
- Uses numpy  
- Robust to skew  
- Foundation for IQR  

---

## **Interquartile Range (IQR)**

```python
import numpy as np

iqr = np.percentile(df['col'], 75) - np.percentile(df['col'], 25)
```

### Why IQR matters:
- Measures middle 50%  
- Ignores extreme values  
- Best for skewed or heavy‑tailed data  

---

# **4. Z‑Scores in Python**

## **Z‑Score (per row)**

```python
df['z_score'] = (df['col'] - df['col'].mean()) / df['col'].std(ddof=0)
```

### Notes:
- Use `ddof=0` for population SD  
- Use `ddof=1` for sample SD  
- Returns a pandas Series  

---

## **Alternative (SciPy)**  
If SciPy is installed:

```python
from scipy.stats import zscore

df['z_score'] = zscore(df['col'])
```

---

# **5. Outlier Detection (IQR Method)**  
This is the most robust method for skewed or heavy‑tailed business data.

### **Step 1 — Compute percentiles**

```python
import numpy as np

p25 = np.percentile(df['col'], 25)
p75 = np.percentile(df['col'], 75)
```

---

### **Step 2 — Compute IQR**

```python
iqr = p75 - p25
```

---

### **Step 3 — Compute fences**

```python
lower = p25 - 1.5 * iqr
upper = p75 + 1.5 * iqr
```

---

### **Step 4 — Filter outliers**

```python
outliers = df[(df['col'] < lower) | (df['col'] > upper)]
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
- Business datasets → population (`ddof=0`)  
- Surveys/experiments → sample (`ddof=1`)  

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

### **Python Workflow Tips**
- Use pandas for dataframes  
- Use numpy for numeric operations  
- Use SciPy for advanced stats  
- Use seaborn/matplotlib for distribution visualisation  
- Always check distribution shape before modelling  

---