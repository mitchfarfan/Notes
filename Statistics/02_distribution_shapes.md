# ✅ **02_distribution_shapes.md**  

---

# **Distribution Shapes — Analyst Toolkit**

Understanding distribution shape is one of the most important skills for a mid‑level analyst. Shape determines which statistics to use, how to interpret data, how to detect outliers, and how to communicate insights clearly.

This file covers the four major distribution types you encounter in real business datasets.

---

# **1. Normal Distribution (Bell‑Shaped)**

### **Visual Mental Model**
```
      🔔
   Symmetrical
```

### **Key Features**
- Symmetrical  
- Mean ≈ Median ≈ Mode  
- Values cluster around the centre  
- Tapers evenly on both sides  
- Outliers are rare and meaningful  

### **Empirical Rule**
For roughly normal data:
- **68%** of values fall within **1 SD**  
- **95%** within **2 SD**  
- **99.7%** within **3 SD**

### **Real‑World Examples**
- Human height  
- Test scores (large populations)  
- Manufacturing tolerances  
- Measurement errors  

### **Analyst Notes**
- SD and z‑scores work well  
- Outliers are meaningful  
- Mean is reliable  
- Normality assumptions often hold  

---

# **2. Right‑Skewed Distribution (Positive Skew)**

### **Visual Mental Model**
```
📈───
Long tail to the right
```

### **Key Features**
- Long right tail  
- **Mean > Median**  
- Most values are low, few high extremes  

### **Examples**
- Income  
- House prices  
- Customer wait times  
- Sales per customer  

### **Analyst Notes**
- Median is more reliable  
- Outliers may be normal  
- Mean is pulled upward  
- Use IQR instead of SD for spread  

---

# **3. Left‑Skewed Distribution (Negative Skew)**

### **Visual Mental Model**
```
───📉
Long tail to the left
```

### **Key Features**
- Long left tail  
- **Mean < Median**  
- Most values are high, few low extremes  

### **Examples**
- Age at retirement  
- Test scores when most people score high  
- Time to complete simple tasks (most fast, few slow)  

### **Analyst Notes**
- Median again more stable  
- Tail values may represent rare delays or failures  
- Mean is pulled downward  

---

# **4. Bimodal Distribution (Two Peaks)**

### **Visual Mental Model**
```
🔔   🔔
Two humps
```

### **Key Features**
- Two distinct peaks  
- Indicates two underlying groups  
- Mean/median hide structure  

### **Examples**
- Customer ages in a theme park (kids + parents)  
- Commute times (city workers + remote workers)  
- Product sales (two popular price points)  
- Height distribution in mixed‑gender datasets  

### **Analyst Notes**
- Segmentation required  
- Do not treat dataset as one group  
- Mean/median misleading  

---

# **5. Heavy‑Tailed Distribution (Fat Tails)**

### **Visual Mental Model**
```
📉────────
Long, thick tail
```

### **Key Features**
- Outliers expected  
- Extreme values more common  
- SD inflated  
- Median + IQR often better  

### **Examples**
- Financial returns  
- Insurance claims  
- Website traffic spikes  
- Fraudulent transactions  
- Social media engagement (most posts small, few viral)  

### **Analyst Notes**
- SD can be misleading  
- Z‑scores unreliable  
- Median + IQR preferred  
- Risk underestimated if assuming normality  

---

# **6. Why Distribution Shape Matters (Mid‑Level Analyst)**

Distribution shape determines:

### **Which statistic to use**
- Normal → mean + SD  
- Skewed → median + IQR  
- Heavy‑tailed → median + IQR  
- Bimodal → segmentation  

### **How to detect outliers**
- Normal → z‑scores  
- Skewed → IQR  
- Heavy‑tailed → IQR  
- Bimodal → segment first  

### **How to visualise**
- Histograms  
- Boxplots  
- Percentiles  
- Density plots  

### **How to model**
- Avoid mean for skewed data  
- Avoid SD for heavy‑tailed data  
- Segment bimodal data  
- Use percentiles for thresholds (P90, P95)  

### **How to communicate**
- “Median is more stable due to skew.”  
- “Outliers are expected in heavy‑tailed data.”  
- “Two peaks indicate two customer groups.”  
- “SD is inflated due to extreme values.”  

---