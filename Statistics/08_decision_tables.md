# ✅ **08_decision_tables.md**  

---

# **Decision Tables — Analyst Toolkit**

This file provides practical, mid‑level decision frameworks for choosing the correct statistical method based on data behaviour. These tables help analysts avoid common mistakes and choose the right summary statistic, spread measure, outlier method, and modelling approach.

All content is derived from your consolidated statistics notes.

---

# **1. “When to Use What” — Decision Table**

| **Scenario / Data Behaviour** | **Use This Statistic** | **Why** |
|-------------------------------|-------------------------|---------|
| Data is roughly symmetrical | **Mean + SD** | Mean represents centre well; SD meaningful |
| Data is skewed (left or right) | **Median + IQR** | Median stable; IQR ignores extreme values |
| Data has extreme outliers | **Median + IQR** | Mean/SD get distorted |
| You want to detect outliers | **IQR Method** | Works even when data is skewed |
| You want standardised values | **Z‑Scores** | Converts values to “how many SDs from mean” |
| You want to compare variability | **Standard Deviation** | Intuitive, same units as data |
| You want robust spread measure | **IQR** | Middle 50% only; ignores noise |
| You have full dataset | **Population SD/Variance** | Most business data is population |
| You have a sample | **Sample SD/Variance** | Corrects bias (n−1) |
| You want distribution shape | **Histogram / Percentiles** | Shows skew, tails, modality |
| You want thresholds | **Percentiles (P25/P75/P90)** | Good for KPI bands, SLAs |

---

# **2. Distribution Shape Decision Table**

This table helps you identify distribution shape and choose the correct statistical tools.

| **Distribution Type** | **Visual Pattern** | **Real‑World Examples** | **Mean vs Median** | **Outlier Behaviour** | **Analyst Interpretation** |
|------------------------|--------------------|--------------------------|---------------------|-------------------------|-----------------------------|
| **Normal** | 🔔 Bell curve | Height, test scores | Mean ≈ Median | Rare | SD meaningful; z‑scores valid |
| **Right‑Skewed** | 📈─── Tail to right | Income, house prices, wait times | Mean > Median | Common | Use median; expect high outliers |
| **Left‑Skewed** | ───📉 Tail to left | Retirement age, high test scores | Mean < Median | Common | Use median; low outliers expected |
| **Bimodal** | 🔔 🔔 Two peaks | Kids + adults, commute types | Mean/Median hide structure | Depends | Segmentation required |
| **Heavy‑Tailed** | 📉──────── Long thick tail | Claims, web traffic, fraud | Mean unstable | Frequent | Use IQR; SD misleading |
| **Uniform** | ▬ Flat | Random numbers | Mean ≈ Median | None | No pattern; rare in business |

---

# **3. Outlier Decision Framework**

### **Step 1 — Identify distribution shape**
- Normal → use z‑scores  
- Skewed → use IQR  
- Heavy‑tailed → use IQR  
- Bimodal → segment first  

### **Step 2 — Choose method**
| **Method** | **Best For** | **Avoid When** |
|------------|--------------|----------------|
| **Z‑Scores** | Normal distributions | Skewed or heavy‑tailed data |
| **IQR** | Skewed, heavy‑tailed, messy business data | Perfectly normal data |
| **Percentiles (P90/P95)** | Thresholds, SLAs, KPIs | Detecting subtle anomalies |
| **Visual inspection (boxplot/histogram)** | Mixed distributions, segmentation | Automated detection |

### **Step 3 — Interpret**
- Inside fences → normal  
- Outside fences → outlier  
- Far outside (3×IQR) → extreme outlier  

---

# **4. Summary Statistic Decision Framework**

### **Choosing the Centre**
| **Data Shape** | **Use** |
|----------------|---------|
| Normal | Mean |
| Skewed | Median |
| Heavy‑Tailed | Median |
| Bimodal | Segment first |

### **Choosing the Spread**
| **Data Shape** | **Use** |
|----------------|---------|
| Normal | SD |
| Skewed | IQR |
| Heavy‑Tailed | IQR |
| Bimodal | Segment first |

---

# **5. KPI & Threshold Decision Table**

| **Goal** | **Best Method** | **Why** |
|----------|------------------|---------|
| SLA thresholds | Percentiles (P90/P95) | Robust to skew |
| Quality control | Z‑scores | Works for normal data |
| Customer behaviour | Median + IQR | Stable for skewed data |
| Fraud detection | IQR + tail analysis | Heavy‑tailed behaviour |
| Operational bottlenecks | P75/P90 | Highlights slow cases |

---

# **6. Visualisation Decision Table**

| **Data Behaviour** | **Best Visual** | **Why** |
|---------------------|------------------|---------|
| Skewed | Boxplot | Shows median + IQR clearly |
| Heavy‑Tailed | Histogram + percentiles | Shows tail thickness |
| Bimodal | Histogram | Reveals two peaks |
| Normal | Histogram + SD bands | Shows symmetry |
| Mixed | Density plot | Shows shape smoothly |

---

# **7. Analyst Communication Framework**

### **How to explain choices to stakeholders**
- “Median is more stable due to skew.”  
- “IQR ignores extreme values, giving a clearer picture.”  
- “Outliers are expected in heavy‑tailed data.”  
- “Two peaks indicate two customer groups.”  
- “SD is inflated due to extreme values.”  

### **How to justify outlier treatment**
- Keep outliers when they represent real behaviour  
- Remove only when clearly erroneous  
- Explain distribution shape before removing anything  

---