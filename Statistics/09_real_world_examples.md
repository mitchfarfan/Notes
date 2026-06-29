# ✅ **09_real_world_examples.md**  

---

# **Real‑World Statistical Examples — Analyst Toolkit**

This file provides practical examples of how distribution shape affects the choice of summary statistics, outlier detection, KPIs, and modelling. These examples come directly from real business datasets and reflect the patterns analysts encounter daily.

---

# **1. Customer Wait Times**

### **Typical Shape:**  
Right‑skewed (long tail to the right)

### **Best Statistics to Use:**  
- Median  
- IQR  
- P75 / P90 for SLAs  

### **Why:**  
Most customers wait a short time, but a few wait much longer.  
These long waits pull the mean upward and inflate SD.

### **Analyst Interpretation:**  
- Median shows the “typical” customer experience  
- IQR shows stable middle behaviour  
- P90 is ideal for operational thresholds  

---

# **2. Sales per Customer**

### **Typical Shape:**  
Heavy‑tailed

### **Best Statistics to Use:**  
- Median  
- P75 / P95  
- IQR  

### **Why:**  
A few customers spend a lot (VIPs), creating a thick right tail.  
Mean becomes misleading because it’s dominated by high spenders.

### **Analyst Interpretation:**  
- Median shows typical spend  
- P95 shows high‑value customers  
- Outliers are expected, not errors  

---

# **3. Employee Salaries**

### **Typical Shape:**  
Right‑skewed

### **Best Statistics to Use:**  
- Median  
- IQR  

### **Why:**  
High earners distort the mean.  
Median salary is the standard metric used in HR and compensation analytics.

### **Analyst Interpretation:**  
- Median salary represents the workforce better  
- IQR shows salary spread without distortion  

---

# **4. Daily Website Visits**

### **Typical Shape:**  
Heavy‑tailed

### **Best Statistics to Use:**  
- IQR  
- Percentiles (P75, P90, P95)  

### **Why:**  
Traffic spikes are common — viral posts, promotions, events.  
SD becomes inflated and misleading.

### **Analyst Interpretation:**  
- IQR shows stable daily behaviour  
- P95 highlights peak traffic  
- Outliers are expected  

---

# **5. Order Processing Times**

### **Typical Shape:**  
Right‑skewed

### **Best Statistics to Use:**  
- Median  
- P90  

### **Why:**  
Most orders process quickly, but delays create long right tails.  
Mean exaggerates typical processing time.

### **Analyst Interpretation:**  
- Median shows typical processing  
- P90 identifies slow cases  
- Useful for operational SLAs  

---

# **6. Product Ratings**

### **Typical Shape:**  
Left‑skewed (most ratings high)

### **Best Statistics to Use:**  
- Mode  
- Median  

### **Why:**  
Most customers give high ratings (4–5 stars).  
A few low ratings create a left tail.

### **Analyst Interpretation:**  
- Mode shows most common rating  
- Median shows typical rating  
- Mean can be misleading if a few customers rate very low  

---

# **7. Customer Age**

### **Typical Shape:**  
Often bimodal

### **Best Statistics to Use:**  
- Segmentation  
- Separate medians per group  

### **Why:**  
Many datasets contain two groups:  
- Kids  
- Adults  

Mean and median hide this structure.

### **Analyst Interpretation:**  
- Segment first  
- Analyse each group separately  
- Bimodality indicates mixed populations  

---

# **8. Transaction Amounts**

### **Typical Shape:**  
Heavy‑tailed

### **Best Statistics to Use:**  
- Median  
- P95  
- IQR  

### **Why:**  
Most transactions are small, but a few are very large.  
Mean becomes useless because it’s dominated by extreme values.

### **Analyst Interpretation:**  
- Median shows typical transaction  
- P95 highlights high‑value purchases  
- Outliers are normal behaviour  

---

# **9. Fraudulent Transactions**

### **Typical Shape:**  
Heavy‑tailed with extreme outliers

### **Best Statistics to Use:**  
- IQR  
- Tail analysis  
- P99  

### **Why:**  
Fraud often appears as extreme values far beyond normal ranges.

### **Analyst Interpretation:**  
- Outliers are meaningful  
- SD is misleading  
- Percentile thresholds detect anomalies better  

---

# **10. Insurance Claims**

### **Typical Shape:**  
Heavy‑tailed

### **Best Statistics to Use:**  
- Median  
- IQR  
- P95  

### **Why:**  
Most claims are small, but catastrophic events create huge values.

### **Analyst Interpretation:**  
- Median shows typical claim  
- P95 shows risk exposure  
- Outliers represent real events, not errors  

---

# **11. Social Media Engagement**

### **Typical Shape:**  
Heavy‑tailed

### **Best Statistics to Use:**  
- Median  
- P75 / P95  
- IQR  

### **Why:**  
Most posts get little engagement; a few go viral.

### **Analyst Interpretation:**  
- Median shows typical engagement  
- P95 highlights viral content  
- Outliers are expected  

---