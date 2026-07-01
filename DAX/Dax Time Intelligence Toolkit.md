---

# ✅ **PRE‑NOTE — REQUIRED BASE MEASURES**  
Make sure these exist first.  
If not, **add them** — all time‑intelligence measures depend on them.

```
Total Revenue = SUM(FactSales[NetRevenue])
Total COGS = SUM(FactSales[COGS])
Total Returns = SUM(FactSales[Returns])
Units Ordered = SUM(FactSales[UnitsOrdered])
Units Shipped = SUM(FactSales[UnitsShipped])
Orders Delivered On Time = SUM(FactOrders[DeliveredOnTime])
Total Orders = SUM(FactOrders[TotalOrders])
Stock On Hand = SUM(FactInventory[StockOnHand])
Inventory Value = SUM(FactInventory[InventoryValue])   // if available
```

---

# ⭐ **DAX TIME INTELLIGENCE TOOLKIT**  

---

# 🔵 **1. Current & Prior Period Measures**

### **Current Period Revenue**
```
Revenue Current =
    [Total Revenue]
```

### **Prior Period Revenue**
```
Revenue Prior =
    CALCULATE(
        [Total Revenue],
        SAMEPERIODLASTYEAR('DimDate'[Date])
    )
```

---

# 🔵 **2. YoY Measures**

### **YoY Revenue**
```
YoY Revenue =
    [Revenue Current] - [Revenue Prior]
```

### **YoY % Growth**
```
YoY Revenue % =
    DIVIDE([YoY Revenue], [Revenue Prior])
```

---

# 🔵 **3. Rolling Window Measures**

### **Rolling 12‑Month Revenue**
```
Revenue Rolling 12M =
    CALCULATE(
        [Total Revenue],
        DATESINPERIOD('DimDate'[Date], MAX('DimDate'[Date]), -12, MONTH)
    )
```

### **Rolling 12‑Month YoY**
```
YoY Revenue Rolling 12M =
    [Revenue Rolling 12M]
    - CALCULATE(
        [Revenue Rolling 12M],
        SAMEPERIODLASTYEAR('DimDate'[Date])
    )
```

---

# 🔵 **4. MTD / QTD / YTD Measures**

### **MTD**
```
Revenue MTD =
    TOTALMTD([Total Revenue], 'DimDate'[Date])
```

### **QTD**
```
Revenue QTD =
    TOTALQTD([Total Revenue], 'DimDate'[Date])
```

### **YTD**
```
Revenue YTD =
    TOTALYTD([Total Revenue], 'DimDate'[Date])
```

---

# 🔵 **5. Prior MTD / QTD / YTD**

### **Prior MTD**
```
Revenue MTD Prior =
    CALCULATE([Revenue MTD], SAMEPERIODLASTYEAR('DimDate'[Date]))
```

### **Prior QTD**
```
Revenue QTD Prior =
    CALCULATE([Revenue QTD], SAMEPERIODLASTYEAR('DimDate'[Date]))
```

### **Prior YTD**
```
Revenue YTD Prior =
    CALCULATE([Revenue YTD], SAMEPERIODLASTYEAR('DimDate'[Date]))
```

---

# 🔵 **6. MTD / QTD / YTD YoY**

### **MTD YoY**
```
Revenue MTD YoY =
    [Revenue MTD] - [Revenue MTD Prior]
```

### **QTD YoY**
```
Revenue QTD YoY =
    [Revenue QTD] - [Revenue QTD Prior]
```

### **YTD YoY**
```
Revenue YTD YoY =
    [Revenue YTD] - [Revenue YTD Prior]
```

---

# ⭐ **This is the complete, final, enterprise‑grade Time Intelligence Toolkit.**

You now have:

- Current  
- Prior  
- YoY  
- YoY %  
- Rolling 12M  
- Rolling 12M YoY  
- MTD / QTD / YTD  
- Prior MTD / QTD / YTD  
- MTD / QTD / YTD YoY  

This is the **full set** used in:

- Retail  
- FMCG  
- Supply Chain  
- Commercial Finance  
- Logistics  
- Executive dashboards  
- Trend analysis  
- Forecasting models  