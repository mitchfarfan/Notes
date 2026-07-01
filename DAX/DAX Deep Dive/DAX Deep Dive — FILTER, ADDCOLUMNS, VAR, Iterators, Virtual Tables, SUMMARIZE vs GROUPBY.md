# **📘 DAX Deep Dive — FILTER, ADDCOLUMNS, VAR, Iterators, Virtual Tables, SUMMARIZE vs GROUPBY**

A modelling‑level guide for BI developers.

---

# ## **1. FILTER — The Foundation of Row-Level Filtering**

`FILTER()` returns a **table**, not a value.  
It is used inside:

- CALCULATE  
- SUMX  
- AVERAGEX  
- ADDCOLUMNS  
- Virtual tables  
- Iterator logic  

### **Syntax**
```DAX
FILTER(Table, Condition)
```

### **Example — Filter FactSales to only full-price sales**
```DAX
Full Price Revenue =
SUMX(
    FILTER(FactSales, FactSales[DiscountAmount] = 0),
    FactSales[UnitsSold] * FactSales[OriginalPrice]
)
```

### **What FILTER does**
- Creates a **virtual table** containing only rows where DiscountAmount = 0  
- SUMX iterates that filtered table  
- Evaluates the expression per row  

### **Mental Model**
> FILTER = “Give me only the rows that match this condition.”

---

# ## **2. ADDCOLUMNS — Build Virtual Columns Inside Virtual Tables**

`ADDCOLUMNS()` adds **calculated columns** to a virtual table.

### **Syntax**
```DAX
ADDCOLUMNS(
    Table,
    "NewColumnName", Expression
)
```

### **Example — Add a calculated revenue column**
```DAX
Sales With Revenue =
ADDCOLUMNS(
    FactSales,
    "Revenue", FactSales[UnitsSold] * FactSales[NetPrice]
)
```

This returns a **virtual table** with an extra column called Revenue.

### **Example — Combine FILTER + ADDCOLUMNS**
```DAX
HighValueSales =
ADDCOLUMNS(
    FILTER(FactSales, FactSales[UnitsSold] > 10),
    "Revenue", FactSales[UnitsSold] * FactSales[NetPrice]
)
```

### **Mental Model**
> ADDCOLUMNS = “Extend a table with new calculated columns.”

---

# ## **3. VAR Patterns — Clean, Fast, Professional DAX**

`VAR` blocks make DAX readable and efficient.

### **Example — Using VAR inside a measure**
```DAX
Margin % =
VAR Revenue = [Total Revenue]
VAR Cost    = [Total COGS]
VAR Margin  = Revenue - Cost
RETURN
DIVIDE(Margin, Revenue)
```

### **Why VAR is essential**
- Prevents repeated calculations  
- Makes code readable  
- Improves performance  
- Allows step-by-step logic  

### **Mental Model**
> VAR = “Store intermediate results so your DAX is clean and fast.”

---

# ## **4. Iterator Performance — SUMX vs SUM**

Iterators (`SUMX`, `AVERAGEX`, `FILTER`, etc.) are powerful but can be expensive.

### **General rules**
- SUMX is slower than SUM  
- SUMX over large fact tables can be costly  
- SUMX + FILTER is even heavier  
- SUMX + ADDCOLUMNS + FILTER is the heaviest pattern  

### **Performance tips**
- Push logic into the model (Power Query) when possible  
- Use VAR to avoid recalculating expressions  
- Use SUMX only when multi-column logic is required  
- Avoid SUMX over virtual tables with millions of rows  

### **Example — Optimised SUMX**
```DAX
Total Revenue =
VAR PriceQty =
    SUMX(
        FactSales,
        FactSales[UnitsSold] * FactSales[NetPrice]
    )
RETURN PriceQty
```

### **Mental Model**
> SUMX is powerful — use it intentionally, not casually.

---

# ## **5. Virtual Tables — The Hidden Engine Behind DAX**

Virtual tables exist **only during evaluation**.  
They are not stored in the model.

They are created by:

- FILTER  
- ADDCOLUMNS  
- SUMMARIZE  
- GROUPBY  
- CALCULATETABLE  
- VALUES  
- ALL  
- ALLEXCEPT  

### **Example — Virtual table for high-value sales**
```DAX
HighValueSales =
FILTER(FactSales, FactSales[UnitsSold] > 10)
```

### **Example — Virtual table with added columns**
```DAX
SalesWithMargin =
ADDCOLUMNS(
    FactSales,
    "Margin", FactSales[NetPrice] - FactSales[UnitCost]
)
```

### **Example — Virtual table inside CALCULATE**
```DAX
Revenue for High Value Sales =
CALCULATE(
    [Total Revenue],
    FILTER(FactSales, FactSales[UnitsSold] > 10)
)
```

### **Mental Model**
> Virtual tables = “temporary tables created during measure evaluation.”

---

# ## **6. SUMMARIZE vs GROUPBY — Grouping Tables the Right Way**

Both functions group tables, but they behave differently.

---

## **SUMMARIZE — Flexible, Classic, Widely Used**

### **Syntax**
```DAX
SUMMARIZE(
    Table,
    GroupByColumn1,
    GroupByColumn2,
    "NewColumn", Expression
)
```

### **Example — Group sales by product**
```DAX
SalesByProduct =
SUMMARIZE(
    FactSales,
    FactSales[ProductID],
    "TotalUnits", SUM(FactSales[UnitsSold]),
    "TotalRevenue", SUMX(FactSales, FactSales[UnitsSold] * FactSales[NetPrice])
)
```

### **Pros**
- Flexible  
- Allows multiple calculated columns  
- Works with any table  

### **Cons**
- Can be slower  
- Can behave unexpectedly with relationships  

---

## **GROUPBY — More Strict, More Efficient**

GROUPBY requires **existing columns** and uses **iterators** for calculations.

### **Syntax**
```DAX
GROUPBY(
    Table,
    GroupByColumn,
    "NewColumn", Expression
)
```

### **Example — Same logic using GROUPBY**
```DAX
SalesByProduct =
GROUPBY(
    FactSales,
    FactSales[ProductID],
    "TotalUnits", SUMX(CURRENTGROUP(), FactSales[UnitsSold]),
    "TotalRevenue", SUMX(CURRENTGROUP(), FactSales[UnitsSold] * FactSales[NetPrice])
)
```

### **Pros**
- More efficient  
- Cleaner grouping logic  

### **Cons**
- Less flexible  
- Requires CURRENTGROUP()  
- Harder for beginners  

---

## **When to Use Which**

### ✔ Use **SUMMARIZE** when:
- You need flexibility  
- You want multiple calculated columns  
- You’re building virtual tables for debugging  
- You’re exploring data  

### ✔ Use **GROUPBY** when:
- You want performance  
- You’re grouping large fact tables  
- You need strict grouping behavior  

### **Mental Model**
> SUMMARIZE = flexible grouping  
> GROUPBY = strict, efficient grouping  

---

# ## **7. Putting It All Together — The Modelling Mental Model**

### ✔ FILTER  
Row-level filtering  
Creates virtual tables

### ✔ ADDCOLUMNS  
Adds calculated columns to virtual tables

### ✔ VAR  
Stores intermediate results  
Improves readability and performance

### ✔ SUMX  
Row-by-row logic  
Used for revenue, cost, margin, inventory, KPIs

### ✔ Virtual Tables  
Temporary tables created during evaluation  
Used inside CALCULATE, SUMX, FILTER, etc.

### ✔ SUMMARIZE vs GROUPBY  
Table grouping  
Flexible vs strict  
Exploration vs performance

---

# ## **8. Summary Cheat Sheet**

```
FILTER        = return only rows that match a condition
ADDCOLUMNS    = add calculated columns to a table
VAR           = store intermediate values for clean, fast DAX
SUMX          = row-by-row iterator for multi-column logic
VirtualTables = temporary tables created during evaluation
SUMMARIZE     = flexible grouping
GROUPBY       = strict, efficient grouping
```

---
