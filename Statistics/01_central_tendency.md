# ✅ **01_central_tendency.md**  

---

# **Central Tendency & Spread — Analyst Toolkit**

Understanding central tendency and spread is the foundation of real‑world analytics. These concepts determine how you summarise data, detect anomalies, build Power BI models, and communicate insights to stakeholders.

---

## **1. Measures of Central Tendency**

### **Mean (Average)**  
- Sensitive to outliers  
- Works best for symmetrical distributions  
- Pulls toward extreme values  

**Formula:**  
\[
\text{Mean} = \frac{\sum x}{n}
\]

**Example:**  
Customer wait times:  
- A few long waits (20+ minutes) will pull the mean upward  
- Median is often more reliable

---

### **Median**  
- Robust when data is skewed  
- Not affected by extreme values  
- Best for right‑skewed (income, sales per customer) or heavy‑tailed data

**Example:**  
House prices:  
- Median price represents the “typical” buyer better than mean  
- Mean is distorted by luxury properties

---

### **Mode**  
- Useful for categorical data  
- Shows the most common value  
- Helps identify dominant groups or behaviours

**Example:**  
Product ratings:  
- Mode = 5 stars  
- Median = 4 stars  
- Mean = 4.2  
Mode reveals customer preference more clearly.

---

## **2. Measures of Spread**

### **Range**  
\[
\text{Range} = \max(x) - \min(x)
\]

- Quick but crude  
- Extremely sensitive to outliers  
- Good for fast profiling, not deep analysis

---

### **Variance**  
Average squared deviation from the mean.

\[
\text{Variance} = \frac{\sum (x - \bar{x})^2}{n}
\]

**Important:**  
- Units become squared (e.g., dollars², minutes²)  
- Hard to interpret directly  
- Used internally for SD and modelling

---

### **Standard Deviation (SD)**  
Square root of variance — brings units back to normal.

\[
\text{SD} = \sqrt{\text{Variance}}
\]

**Why SD matters:**  
- Intuitive  
- Same units as the data  
- Used in dashboards, KPIs, anomaly detection  
- Works well when data is roughly normal

---

## **3. Distribution Shapes & Why They Matter**

Distribution shape determines:

- Whether mean or median is appropriate  
- Whether SD or IQR is meaningful  
- Whether outliers are errors or expected  
- Whether segmentation is required  
- How to visualise the data  
- How to model behaviour in Power BI or SQL  

### **Normal Distribution**  
- Symmetrical  
- Mean ≈ Median  
- Outliers rare  
- SD + z‑scores meaningful  
- Empirical rule applies (68/95/99.7)

### **Right‑Skewed**  
- Long tail to the right  
- Mean > Median  
- Median more reliable  
- Outliers may be normal  
Examples: income, house prices, customer wait times

### **Left‑Skewed**  
- Long tail to the left  
- Mean < Median  
Examples: retirement age, high‑score tests

### **Bimodal**  
- Two peaks  
- Mean/median hide structure  
- Indicates two underlying groups  
Examples: theme park ages (kids + parents)

### **Heavy‑Tailed**  
- Outliers expected  
- SD inflated  
- Median + IQR preferred  
Examples: financial returns, insurance claims, web traffic spikes

---

## **4. Outliers — Practical Analyst Guidance**

### **Why Outliers Matter**
- Reveal rare events  
- Identify data quality issues  
- Show operational bottlenecks  
- Affect KPIs and thresholds  
- Influence modelling decisions  

### **When to Keep Outliers**
- Heavy‑tailed data  
- Fraud detection  
- Website traffic spikes  
- Insurance claims  
- Sales per customer (VIPs)

### **When to Remove Outliers**
- Data entry errors  
- Sensor malfunctions  
- Impossible values (negative wait times)  
- Incorrect units (minutes vs seconds)

### **How to Justify Decisions (Senior‑Friendly)**
- “The distribution is heavy‑tailed; outliers are expected.”  
- “Median is more stable for skewed data.”  
- “Extreme values represent legitimate customer behaviour.”  
- “Outliers removed only when clearly erroneous.”

---

## **5. Analyst‑Ready Framing**

### **Data Profiling**
- Identify skew  
- Spot anomalies  
- Understand spread  
- Choose correct summary statistics

### **Anomaly Detection**
- Use z‑scores for normal data  
- Use IQR for skewed/heavy‑tailed data  
- Use percentiles for thresholds (P90, P95)

### **Stakeholder Communication**
- Explain mean vs median simply  
- Show distribution shape visually  
- Justify outlier treatment clearly  
- Use intuitive language (“typical customer”, “rare event”)

### **Power BI Modelling**
Understanding distribution shape helps you:

- Choose correct DAX measures  
- Build stable KPIs  
- Avoid misleading averages  
- Select correct visuals (histograms, boxplots, percentiles)  
- Detect data quality issues early  

---