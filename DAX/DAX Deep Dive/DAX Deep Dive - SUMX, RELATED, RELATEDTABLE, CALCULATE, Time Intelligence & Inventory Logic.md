# **📘 DAX Deep Dive — SUMX, RELATED, RELATEDTABLE, CALCULATE, Time Intelligence & Inventory Logic**

A complete modelling‑level guide.

---

# ## **1. SUM vs SUMX — Column Logic vs Row Logic**

### **SUM**
- Aggregates **one column**.
- Works inside the **current filter context**.
- Cannot evaluate formulas per row.

Example:
```DAX
Total Units Sold = SUM(FactSales[UnitsSold])
```

### **SUMX**
- Iterates **row‑by‑row** through a table.
- Evaluates an **expression per row**.
- Sums the results.

Example:
```DAX
Original Price Total =
SUMX(
    FactSales,
    FactSales[UnitsSold] * FactSales[OriginalPrice]
)
```

### **Mental Model**
- **SUM = column logic**
- **SUMX = row logic**

SUMX is required whenever your calculation involves **multiple columns**.

---

# ## **2. SUMX + RELATED — Row Logic Across Tables**

If a column lives in another table, SUMX cannot see it directly.

You must use **RELATED()**.

### **Example**
OriginalPrice lives in **FactCost**, not FactSales.

```DAX
Original Price Total =
SUMX(
    FactSales,
    FactSales[UnitsSold] * RELATED(FactCost[OriginalPrice])
)
```

### **Why RELATED Works**
RELATED pulls a value from the **one‑side** of a relationship for the current row on the **many‑side**.

Requirements:
- Many → One relationship  
- Active  
- Correct direction  
- Matching keys  

### **SQL Equivalent**
```SQL
SELECT SUM(FS.UnitsSold * FC.OriginalPrice)
FROM FactSales FS
JOIN FactCost FC
  ON FS.ProductID = FC.ProductID
```

DAX does not write joins — the **model relationship** handles it.

---

# ## **3. RELATEDTABLE — The Reverse Direction**

If RELATED is “Fact → Dim”, then RELATEDTABLE is “Dim → Fact”.

### **RELATEDTABLE returns a *table***  
It returns **all rows** on the many‑side that match the current row on the one‑side.

### **Example**
Count how many sales rows exist for each product:

```DAX
Sales Rows per Product =
COUNTROWS(RELATEDTABLE(FactSales))
```

### **When to use RELATEDTABLE**
- Dim → Fact direction  
- You need a **table**, not a single value  
- You want to iterate over related rows  

### **Mental Model**
- **RELATED = lookup one value**
- **RELATEDTABLE = lookup many rows**

---

# ## **4. CALCULATE — The Filter Context Engine**

CALCULATE is the most important function in DAX.

### **Definition**
> CALCULATE changes the filter context, then evaluates an expression inside the new context.

### **Example**
Total revenue for a specific product:

```DAX
Revenue for Product 101 =
CALCULATE(
    [Total Revenue],
    FactSales[ProductID] = 101
)
```

### **What CALCULATE does**
1. Removes existing filters on ProductID  
2. Applies the new filter (ProductID = 101)  
3. Recalculates the measure under the new context  

### **Mental Model**
- **SUMX = row context**
- **CALCULATE = filter context**

Together, they form the backbone of modelling logic.


### **The filter modifiers (the rules for changing the filter context)**
Everything after the first argument is a filter.

Everything after the **first argument** in `CALCULATE()` is a **filter**.  
These filters reshape the **filter context** before the expression is evaluated.

### **Boolean Filters**
```DAX
FactCostChanges[ChangeDateKey] = LatestDate
```
A simple TRUE/FALSE condition applied to the table.

---

### **Table Filters**
```DAX
FILTER(FactSales, FactSales[Qty] > 10)
```
A table expression that returns only rows meeting a condition.

---

### **Context Removal**
```DAX
ALL(DimDate)
```
Removes filters from the specified table or column.

---

### **Context Preservation**
```DAX
ALLEXCEPT(FactCostChanges, FactCostChanges[ProductID])
```
Removes all filters **except** the ones listed.

---

### **Relationship Manipulation**
```DAX
CROSSFILTER(...)
```
Changes the direction or behavior of relationships during evaluation.

---

### **Virtual Tables**
```DAX
TREATAS(...)
```
Applies filters from one table to another, creating a virtual relationship.

---

### **What these filters do**
All of these are ways of telling **CALCULATE**:


---

# ## **5. SUMX Inside Time Intelligence**

SUMX is often used inside time intelligence when you need row‑level logic.

### **Example: Rolling 12‑Month Revenue**
```DAX
Rolling 12M Revenue =
CALCULATE(
    [Total Revenue],
    DATESINPERIOD(
        DimDate[Date],
        MAX(DimDate[Date]),
        -12,
        MONTH
    )
)
```

This works because `[Total Revenue]` is already a SUMX‑based measure.

### **Example: YoY Revenue Using SUMX**
```DAX
YoY Revenue =
CALCULATE(
    [Total Revenue],
    SAMEPERIODLASTYEAR(DimDate[Date])
)
```

### **When SUMX is needed**
If your revenue measure is row‑based:

```DAX
Total Revenue =
SUMX(
    FactSales,
    FactSales[UnitsSold] * FactSales[NetPrice]
)
)
```

Time intelligence automatically respects SUMX because CALCULATE rewrites the filter context.

---

# ## **6. SUMX Inside Inventory / WAC Logic**

Inventory logic is where SUMX becomes essential.

### **Weighted Average Cost (WAC) After Movement**
```DAX
WAC After Movement =
DIVIDE(
    SUMX(
        FactInventory,
        FactInventory[UnitsMoved] * FactInventory[UnitCost]
    ),
    SUM(FactInventory[UnitsMoved])
)
```

### **Why SUMX is required**
WAC requires:

- UnitsMoved × UnitCost  
- Per row  
- Then aggregated  

SUM cannot do this.  
SUMX is the correct pattern.

---

# ## **7. Inventory Movement Example**

### **Stock Movement**
```DAX
Stock Movement =
SUMX(
    FactInventory,
    FactInventory[UnitsReceived] - FactInventory[UnitsSold]
)
```

### **Opening Stock**
```DAX
Opening Stock =
CALCULATE(
    [Closing Stock],
    DATEADD(DimDate[Date], -1, DAY)
)
```

### **Closing Stock**
```DAX
Closing Stock =
SUMX(
    FactInventory,
    FactInventory[UnitsReceived] - FactInventory[UnitsSold]
)
```

Inventory logic is **row‑based**, so SUMX is the correct tool.

---

# ## **8. Putting It All Together — The Modelling Mental Model**

### ✔ **SUM**
Column logic  
Aggregates one column

### ✔ **SUMX**
Row logic  
Evaluates formulas per row  
Used for revenue, cost, margin, inventory, KPIs

### ✔ **RELATED**
Fact → Dim lookup  
Returns one value  
Requires Many → One relationship

### ✔ **RELATEDTABLE**
Dim → Fact lookup  
Returns a table  
Used for counting or iterating related rows

### ✔ **CALCULATE**
Filter context engine  
Rewrites filters  
Used for time intelligence, KPIs, conditional logic

### ✔ **SUMX + CALCULATE**
The foundation of:
- revenue modelling  
- cost modelling  
- margin modelling  
- inventory valuation  
- returns logic  
- time intelligence  
- KPIs  

This is the core of professional BI modelling.

---

# ## **9. Summary Cheat Sheet**

```
SUM     = column logic
SUMX    = row logic
RELATED = lookup one value (Many → One)
RELATEDTABLE = lookup many rows (One → Many)
CALCULATE = change filter context
SUMX + RELATED = row-by-row commercial logic
SUMX + CALCULATE = time intelligence + modelling
```

---