# ✅ **03_standard_deviation_zscore.md**  

---

# **Standard Deviation & Z‑Scores — Analyst Toolkit**

Standard deviation and z‑scores are core tools for understanding spread, detecting anomalies, and profiling data. They are essential for mid‑level analytics work across SQL, Power BI, Python, and Excel.

This file covers:

- Standard deviation  
- Variance  
- Empirical rule  
- Z‑scores  
- SQL / DAX / Python implementations  
- When SD and z‑scores fail  
- Analyst interpretation  

---

# **1. Standard Deviation (SD)**

Standard deviation measures how spread out values are around the mean.

### **Formula**
\[
SD = \sqrt{\frac{\sum (x - \bar{x})^2}{n}}
\]

### **Interpretation**
- **Low SD** → values tightly clustered  
- **High SD** → values more variable or unstable  

### **Mental Model**
> If the mean is the centre of gravity, SD tells you how wide the orbit is.

### **Why Analysts Care**
- Detecting volatility (sales, wait times, processing times)  
- Comparing consistency between groups  
- Spotting unusual behaviour before modelling  
- Understanding whether a dataset is “clean” or chaotic  

---

# **2. Variance (for completeness)**

Variance is the average squared deviation from the mean.

### **Formula**
\[
Variance = \frac{\sum (x - \bar{x})^2}{n}
\]

### **Why variance is rarely interpreted**
- Units become squared (dollars², minutes²)  
- Hard to explain to stakeholders  
- Used internally to compute SD  

### **SD is preferred because:**
- Same units as the data  
- Intuitive  
- Works directly with z‑scores  
- Used in dashboards, KPIs, anomaly detection  

---

# **3. Empirical Rule (Normal Distribution Cheat Sheet)**

Applies when data is roughly bell‑shaped.

- **68%** of values fall within **1 SD**  
- **95%** within **2 SD**  
- **99.7%** within **3 SD**

### **Why it matters**
- Fast way to judge whether a value is normal or unusual  
- Helps set thresholds (flag anything beyond 2 SD)  
- Useful for anomaly detection and sanity checks  

### **Interpretation**
> If something sits beyond 2–3 SD, it’s probably worth investigating.

---

# **4. Z‑Scores**

Z‑scores tell you how extreme a value is relative to the mean.

### **Formula**
\[
Z = \frac{x - \bar{x}}{SD}
\]

### **Interpretation**
- **Positive z** → above average  
- **Negative z** → below average  
- \(|z| < 2\) → normal  
- \(|z| ≥ 2\) → unusual  
- \(|z| ≥ 3\) → highly unlikely under normal conditions  

### **Why Analysts Use Z‑Scores**
- Outlier detection  
- Fraud detection  
- Quality control  
- Standardising variables for modelling  
- Comparing different metrics on a common scale  

---

# **5. Z‑Scores in SQL (from your notes)**

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

This computes:

- Mean  
- Standard deviation  
- Z‑score per row  

All in milliseconds.

---

# **6. Z‑Scores in Power BI / DAX**

### **Mean**
```DAX
MeanValue = AVERAGE(Table[Column])
```

### **Standard Deviation**
```DAX
SDValue = STDEV.P(Table[Column])
```

### **Z‑Score (measure)**
```DAX
ZScore =
DIVIDE(
    SELECTEDVALUE(Table[Column]) - [MeanValue],
    [SDValue]
)
```

### **Z‑Score (calculated column)**
Use this when you need row‑level z‑scores:

```DAX
ZScoreColumn =
(Table[Column] - CALCULATE(AVERAGE(Table[Column]))) /
CALCULATE(STDEV.P(Table[Column]))
```

---

# **7. Z‑Scores in Python (pandas + numpy)**

```python
df['z_score'] = (df['col'] - df['col'].mean()) / df['col'].std(ddof=0)
```

### Notes:
- `ddof=0` → population SD  
- `ddof=1` → sample SD (default in pandas)  

---

# **8. When SD & Z‑Scores Fail**

SD and z‑scores assume data is roughly normal.

They **fail** when data is:

### **Skewed**
- Mean distorted  
- SD inflated  
- Z‑scores unreliable  

### **Heavy‑Tailed**
- Outliers expected  
- SD misleading  
- Median + IQR preferred  

### **Bimodal**
- Two peaks  
- Mean/median hide structure  
- SD meaningless  
- Segmentation required  

### **Best Alternatives**
- Median  
- Percentiles (P25, P50, P75)  
- IQR  
- P90/P95 thresholds  
- Boxplots  
- Distribution visualisation  

---

# **9. Practical Analyst Workflow**

### **Step 1 — Check distribution shape**
- Histogram  
- Boxplot  
- Percentiles  

### **Step 2 — Choose correct summary statistic**
- Normal → mean + SD  
- Skewed → median + IQR  
- Heavy‑tailed → median + IQR  
- Bimodal → segment first  

### **Step 3 — Apply z‑scores only when appropriate**
- Use z‑scores for roughly normal data  
- Use IQR for skewed/heavy‑tailed data  

### **Step 4 — Communicate clearly**
- “SD is inflated due to skew.”  
- “Median is more stable for this dataset.”  
- “Outliers are expected in heavy‑tailed data.”  

---