# **📚 DAX Pattern Library — Master Index (Final Edition)**  
A complete index of reusable DAX patterns for BI developers.

This library is organised into **10 core domains**, each containing the essential patterns used in professional semantic modelling.

---

# ## **1. Core Aggregation Patterns**
### **1.1 SUM vs SUMX**
- Column aggregation  
- Row-by-row evaluation  
- Multi-column logic  

### **1.2 AVERAGE vs AVERAGEX**
- Simple averages  
- Weighted averages  

### **1.3 COUNT, COUNTROWS, DISTINCTCOUNT**
- Fact row counts  
- Dimension counts  
- Validation measures  

---

# ## **2. Relationship Navigation Patterns**
### **2.1 RELATED**
- Many → One lookup  
- Fact → Dimension  

### **2.2 RELATEDTABLE**
- One → Many lookup  
- Dimension → Fact  

### **2.3 LOOKUPVALUE**
- Non-relationship lookup  
- Fallback for dirty models  

---

# ## **3. Iterator Patterns**
### **3.1 SUMX**
- Revenue  
- Cost  
- Margin  
- Inventory movement  

### **3.2 FILTER + SUMX**
- Conditional row logic  
- High-value sales  
- Excluding returns  

### **3.3 ADDCOLUMNS + SUMX**
- Virtual column creation  
- Layered inventory logic  

---

# ## **4. Context Transition Patterns**
### **4.1 CALCULATE**
- Filter context rewriting  
- Conditional logic  
- Time intelligence  

### **4.2 CALCULATETABLE**
- Virtual tables  
- Advanced filtering  
- Iterator pipelines  

### **4.3 Row Context vs Filter Context**
- The core DAX mental model  
- Context transition  
- Iterator behaviour  

---

# ## **5. Time Intelligence Patterns**
### **5.1 Standard Time Intelligence**
- YTD  
- QTD  
- MTD  
- YoY  
- Rolling windows  

### **5.2 Custom Fiscal Calendars**
- Non‑Jan start  
- Fiscal YTD  
- Fiscal YoY  

### **5.3 Offset Logic**
- Previous period  
- Previous quarter  
- Previous year  

### **5.4 Snapshot Measures**
- End-of-month inventory  
- Opening/closing balances  

---

# ## **6. Table & Virtual Table Patterns**
### **6.1 FILTER**
- Row-level filtering  
- Virtual table creation  

### **6.2 ADDCOLUMNS**
- Add calculated columns  
- Build enriched virtual tables  

### **6.3 SUMMARIZE**
- Flexible grouping  
- Multi-column aggregation  

### **6.4 GROUPBY**
- Strict grouping  
- CURRENTGROUP patterns  

### **6.5 SUMMARIZECOLUMNS**
- Modern grouping engine  
- Relationship-aware  

---

# ## **7. Ranking & Top-N Patterns**
### **7.1 RANKX**
- Global ranking  
- Category ranking  
- Dynamic ranking  

### **7.2 TOPN**
- Top products  
- Top customers  
- Pareto analysis  

### **7.3 KEEPFILTERS + TOPN**
- Top-N filtering inside CALCULATE  

---

# ## **8. Relationship Manipulation Patterns**
### **8.1 TREATAS**
- Virtual relationships  
- Multi-grain alignment  
- Custom filtering  

### **8.2 CROSSFILTER**
- Temporary bidirectional filters  
- Override inactive relationships  

### **8.3 USERELATIONSHIP**
- Activate inactive relationships  
- Role-playing dimensions  

---

# ## **9. Advanced Modelling Patterns**
### **9.1 Calculation Groups**
- Time intelligence groups  
- Currency conversion  
- KPI formatting  

### **9.2 Degenerate Dimensions**
- OrderID  
- InvoiceID  
- Transaction-level slicing  

### **9.3 Multi-Grain Fact Tables**
- Daily inventory + transactional sales  
- Monthly budgets + daily actuals  
- Virtual alignment via TREATAS  

### **9.4 Parent-Child Hierarchies**
- PATH  
- PATHITEM  
- PATHLENGTH  

---

# ## **10. Inventory Modelling Patterns**
### **10.1 WAC (Weighted Average Cost)**
- Standard retail inventory logic  
- Row-based cost evaluation  

### **10.2 FIFO Layers**
- Layered consumption  
- Virtual table ordering  

### **10.3 LIFO Layers**
- Reverse-layer consumption  
- Virtual table logic  

### **10.4 Stock Movement**
- Opening stock  
- Closing stock  
- Movement logic  

---

# ## **11. KPI Patterns**
### **11.1 Margin KPIs**
- Margin %  
- Margin per unit  

### **11.2 Revenue KPIs**
- ASP  
- Revenue per customer  

### **11.3 Inventory KPIs**
- Stock turnover  
- Return rate  

### **11.4 Operational KPIs**
- Refund rate  
- Cost delta  

---

# ## **12. Validation Patterns**
### **12.1 Row Count Measures**
- Fact table validation  
- Dimension validation  

### **12.2 Relationship Validation**
- Missing keys  
- Orphaned rows  

### **12.3 Data Quality Flags**
- Negative stock  
- Zero-price items  

---

# ## **13. Performance Patterns**
### **13.1 Iterator Optimization**
- VAR usage  
- Pre-aggregation  
- Avoiding large virtual tables  

### **13.2 Storage Mode Awareness**
- DirectQuery limitations  
- Import optimisations  

### **13.3 Composite Model Patterns**
- Hybrid tables  
- Virtual relationships  
- Aggregation tables  

---

# ## **14. Cheat Sheet Summary**

```
SUMX            = row logic
CALCULATE       = filter context rewrite
FILTER          = virtual table creation
RELATED         = many → one lookup
TREATAS         = virtual relationships
RANKX           = dynamic ranking
TOPN            = top-N table selection
SUMMARIZECOLUMNS = modern grouping engine
CalculationGroups = global measure transformations
Multi-Grain     = align facts with different granularity
Inventory       = FIFO/LIFO/WAC layers
TimeIntelligence = rolling windows, offsets, fiscal calendars
```

---
