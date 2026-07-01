# **📘 DAX Deep Dive — TREATAS, CROSSFILTER, CALCULATETABLE, VALUES vs DISTINCT, ALL vs ALLEXCEPT, Context Deep Dive**

A complete modelling‑level guide.

---

# ## **1. TREATAS — The Most Powerful Relationship Function in DAX**

`TREATAS` applies the values of one table **as if** they were filters on another table.

It is used when:
- No relationship exists  
- You need to force a relationship  
- You need to override existing relationships  
- You need to apply filters across incompatible keys  

### **Syntax**
```DAX
TREATAS(TableToUseAsFilter, ColumnToApplyFilterTo)
```

### **Example — Filter FactSales using a virtual table of ProductIDs**
```DAX
Revenue for Selected Products =
CALCULATE(
    [Total Revenue],
    TREATAS(
        VALUES(DimProduct[ProductID]),
        FactSales[ProductID]
    )
)
```

### **What TREATAS does**
- Takes the ProductIDs from DimProduct  
- Applies them as filters to FactSales  
- Works even if the relationship is inactive or missing  

### **Mental Model**
> TREATAS = “Pretend this table is filtering that column.”

---

# ## **2. CROSSFILTER — Change Relationship Direction at Query Time**

`CROSSFILTER` modifies the direction or behavior of a relationship **inside a measure**.

### **Syntax**
```DAX
CROSSFILTER(Column1, Column2, Direction)
```

### **Directions**
- `NONE` — disable relationship  
- `BOTH` — make relationship bidirectional  
- `ONEWAY` — enforce single direction  

### **Example — Force bidirectional filtering**
```DAX
Revenue by Category =
CALCULATE(
    [Total Revenue],
    CROSSFILTER(
        DimProduct[ProductID],
        FactSales[ProductID],
        BOTH
    )
)
```

### **Use cases**
- When slicers don’t propagate  
- When inactive relationships block filters  
- When you need temporary bidirectional behavior  

### **Mental Model**
> CROSSFILTER = “Temporarily change how tables filter each other.”

---

# ## **3. CALCULATETABLE — The Table Version of CALCULATE**

`CALCULATETABLE` returns a **table** instead of a value.

It is used for:
- Virtual tables  
- Advanced filtering  
- Iterator logic  
- Debugging  
- TREATAS inputs  
- SUMMARIZE/GROUPBY pipelines  

### **Syntax**
```DAX
CALCULATETABLE(Table, Filters...)
```

### **Example — Get only last year’s sales rows**
```DAX
LastYearSales =
CALCULATETABLE(
    FactSales,
    SAMEPERIODLASTYEAR(DimDate[Date])
)
```

### **Example — Combine with SUMX**
```DAX
LastYearRevenue =
SUMX(
    CALCULATETABLE(
        FactSales,
        SAMEPERIODLASTYEAR(DimDate[Date])
    ),
    FactSales[UnitsSold] * FactSales[NetPrice]
)
```

### **Mental Model**
> CALCULATETABLE = “Return a filtered table instead of a filtered value.”

---

# ## **4. VALUES vs DISTINCT — They Look Similar, But Aren’t**

### **VALUES**
Returns:
- Unique values  
- **Plus BLANK** if BLANK exists  
- Preserves relationships  
- Used in TREATAS, CALCULATE, virtual tables  

### **DISTINCT**
Returns:
- Unique values  
- **Never returns BLANK**  
- Ignores relationships  
- Pure deduplication  

### **Example**
```DAX
VALUES(DimProduct[Category])
DISTINCT(DimProduct[Category])
```

### **When to use which**

| Use Case | VALUES | DISTINCT |
|----------|--------|----------|
| Filtering | ✔ | ❌ |
| TREATAS | ✔ | ❌ |
| Deduplication | ❌ | ✔ |
| Relationship-aware logic | ✔ | ❌ |
| Removing blanks | ❌ | ✔ |

### **Mental Model**
> VALUES = relationship-aware unique list  
> DISTINCT = pure unique list

---

# ## **5. ALL vs ALLEXCEPT — Clearing Filters Correctly**

### **ALL**
Removes **all filters** from a table or column.

```DAX
Total Revenue (Ignoring Filters) =
CALCULATE([Total Revenue], ALL(FactSales))
```

### **ALLEXCEPT**
Removes all filters **except** the ones you specify.

```DAX
Revenue by Product (Ignoring Others) =
CALCULATE(
    [Total Revenue],
    ALLEXCEPT(FactSales, FactSales[ProductID])
)
```

### **Use cases**

| Use Case | ALL | ALLEXCEPT |
|----------|-----|-----------|
| Grand totals | ✔ | ❌ |
| Percent of total | ✔ | ❌ |
| Keep one filter | ❌ | ✔ |
| Remove slicers | ✔ | ✔ |

### **Mental Model**
> ALL = remove everything  
> ALLEXCEPT = remove everything except these columns

---

# ## **6. Row Context vs Filter Context (Deep Dive)**

This is the core of DAX.

---

## **Row Context**
Row context = “I’m looking at one row right now.”

Created by:
- SUMX  
- AVERAGEX  
- FILTER  
- ADDCOLUMNS  
- Calculated columns  

### **Example**
```DAX
SUMX(FactSales, FactSales[UnitsSold] * FactSales[NetPrice])
```

Inside SUMX:
- UnitsSold refers to the current row  
- NetPrice refers to the current row  

---

## **Filter Context**
Filter context = “Which rows are visible to this calculation?”

Created by:
- Visuals  
- Slicers  
- CALCULATE  
- Relationships  
- ALL / ALLEXCEPT  

### **Example**
```DAX
CALCULATE([Total Revenue], FactSales[ProductID] = 101)
```

Filter context becomes:
- Only rows where ProductID = 101  

---

## **The Rule That Causes 90% of Confusion**

### **Row context does NOT automatically create filter context.**

This is why you need CALCULATE inside iterators.

### **Example**
```DAX
SUMX(
    FactSales,
    CALCULATE([Total Revenue])
)
```

CALCULATE converts row context → filter context.

---

## **Mental Model**
- **Row context = row-by-row evaluation**  
- **Filter context = which rows are visible**  
- **CALCULATE = bridge between the two**  

This is the foundation of professional DAX.

---

# ## **7. Summary Cheat Sheet**

```
TREATAS        = apply filters from one table onto another
CROSSFILTER    = temporarily change relationship direction
CALCULATETABLE = return a filtered table
VALUES         = relationship-aware unique list
DISTINCT       = pure unique list
ALL            = remove all filters
ALLEXCEPT      = remove all filters except specified columns
RowContext     = row-by-row logic
FilterContext  = which rows are visible
CALCULATE      = convert row context → filter context
```
