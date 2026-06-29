# ✅ **07_unified_syntax_table.md**  

---

# **Unified Statistical Syntax Table**  
### *(SQL • Power BI / DAX • Python • Excel)*

This file provides a complete cross‑tool reference for statistical calculations. It allows you to translate logic instantly between SQL, Power BI, Python, and Excel — essential for BI, analytics, and data engineering workflows.

All syntax and notes come directly from your consolidated statistics document.

---

# **Unified Syntax Table**

| **Stat Type** | **SQL Syntax** | **Power BI / DAX Syntax** | **Python Syntax** | **Excel Syntax** | **Notes** |
|---------------|----------------|----------------------------|--------------------|-------------------|-----------|
| **Mean (Average)** | `AVG(col)` | `AVERAGE(Table[col])` | `df['col'].mean()` | `=AVERAGE(range)` | Most common measure; sensitive to outliers |
| **Median** | `PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY col) OVER ()` | `MEDIAN(Table[col])` | `df['col'].median()` | `=MEDIAN(range)` | Robust to skew and outliers |
| **Mode** | Frequency count + `LIMIT 1` | DAX workaround using `TOPN` | `df['col'].mode()` | `=MODE.SNGL(range)` | Excel has built‑in MODE; DAX does not |
| **Standard Deviation (Population)** | `STDDEV_POP(col)` | `STDEV.P(Table[col])` | `df['col'].std(ddof=0)` | `=STDEV.P(range)` | Use for full datasets (most business cases) |
| **Standard Deviation (Sample)** | `STDDEV_SAMP(col)` | `STDEV.S(Table[col])` | `df['col'].std(ddof=1)` | `=STDEV.S(range)` | Use for samples (surveys, experiments) |
| **Variance (Population)** | `VAR_POP(col)` | `VAR.P(Table[col])` | `df['col'].var(ddof=0)` | `=VAR.P(range)` | Units squared; rarely interpreted directly |
| **Variance (Sample)** | `VAR_SAMP(col)` | `VAR.S(Table[col])` | `df['col'].var(ddof=1)` | `=VAR.S(range)` | Sample version (n−1 denominator) |
| **Range** | `MAX(col) - MIN(col)` | `MAX - MIN` | `df['col'].max() - df['col'].min()` | `=MAX(range)-MIN(range)` | Sensitive to outliers |
| **Percentile (25th)** | `PERCENTILE_CONT(0.25) ...` | `PERCENTILEX.INC(Table, Table[col], 0.25)` | `np.percentile(df['col'], 25)` | `=PERCENTILE.INC(range,0.25)` | Used for IQR |
| **Percentile (50th)** | Same as median | Same as median | Same as median | `=PERCENTILE.INC(range,0.5)` | Median = 50th percentile |
| **Percentile (75th)** | `PERCENTILE_CONT(0.75) ...` | `PERCENTILEX.INC(Table, Table[col], 0.75)` | `np.percentile(df['col'], 75)` | `=PERCENTILE.INC(range,0.75)` | Used for IQR |
| **IQR (Interquartile Range)** | `P75 - P25` | `P75 - P25` | `np.percentile(...75) - np.percentile(...25)` | `=PERCENTILE.INC(range,0.75) - PERCENTILE.INC(range,0.25)` | Middle 50% spread; robust to outliers |
| **Z‑Score (per row)** | `(col - AVG OVER()) / SD OVER()` | `(col - AVERAGE)/STDEV.P` | `(df['col'] - mean) / sd` | `(A1 - mean) / sd` | Standardised value |
| **Outlier Detection (IQR Method)** | Filter outside fences | `IF outside fences` | Filter df outside fences | `=IF(A1<LowerFence,1,IF(A1>UpperFence,1,0))` | Best for skewed/heavy‑tailed data |

---

# **Additional Notes**

### **Population vs Sample**
- Business datasets → population  
- Surveys/experiments → sample  

### **Percentiles**
- Use inclusive percentiles (`INC`) in DAX and Excel  
- SQL uses `PERCENTILE_CONT` (ANSI standard)  
- Python uses `numpy.percentile`  

### **Z‑Scores**
- Use only when data is roughly normal  
- Use IQR for skewed or heavy‑tailed data  

### **IQR**
- Most robust spread measure  
- Works across all tools  
- Foundation for outlier detection  

---