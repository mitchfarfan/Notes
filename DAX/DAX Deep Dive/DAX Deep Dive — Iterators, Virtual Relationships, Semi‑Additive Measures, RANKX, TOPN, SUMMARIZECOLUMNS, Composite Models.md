# **📘 DAX Deep Dive — Iterators, Virtual Relationships, Semi‑Additive Measures, RANKX, TOPN, SUMMARIZECOLUMNS, Composite Models**

A complete modelling‑level guide.

---

# ## **1. Iterator Performance Tuning**

Iterators (`SUMX`, `AVERAGEX`, `FILTER`, `ADDCOLUMNS`) are powerful but expensive.  
Performance tuning is essential for large fact tables.

---

## **1.1 Why Iterators Are Slow**
Iterators:
- Create **row context**  
- Evaluate expressions per row  
- Often operate on **virtual tables**  
- Cannot use column storage optimisations  

This makes them slower than simple aggregations like `SUM`.

---

## **1.2 Performance Rules**
### ✔ Rule 1 — Push logic into the model when possible  
If a calculation can be done in Power Query or as a physical column, do it there.

### ✔ Rule 2 — Avoid iterating large virtual tables  
`SUMX(FILTER(FactSales, …))` is expensive.

### ✔ Rule 3 — Use VAR to avoid recalculating expressions  
```DAX
VAR Price = FactSales[NetPrice]
VAR Qty   = FactSales[UnitsSold]
RETURN Price * Qty
```

### ✔ Rule 4 — Prefer SUM over SUMX when possible  
If you can precompute a column, SUM becomes trivial.

---

## **1.3 Optimised SUMX Pattern**
```DAX
Total Revenue =
VAR BaseTable = FactSales
RETURN
SUMX(
    BaseTable,
    FactSales[UnitsSold] * FactSales[NetPrice]
)
```

---

# ## **2. Virtual Relationships**

Virtual relationships allow tables to interact **without physical relationships**.

They are created using:
- `TREATAS`  
- `CROSSFILTER`  
- `CALCULATETABLE`  
- `SUMMARIZECOLUMNS`  

---

## **2.1 Example — Virtual Relationship Using TREATAS**
```DAX
Revenue for Selected Categories =
CALCULATE(
    [Total Revenue],
    TREATAS(
        VALUES(DimCategory[Category]),
        FactSales[Category]
    )
)
```

This applies DimCategory filters to FactSales **without a physical relationship**.

---

## **2.2 Example — Virtual Relationship Using CROSSFILTER**
```DAX
Revenue with Temporary Bidirectional Filter =
CALCULATE(
    [Total Revenue],
    CROSSFILTER(
        DimProduct[ProductID],
        FactSales[ProductID],
        BOTH
    )
)
```

---

## **2.3 Why Virtual Relationships Matter**
- Composite models  
- Role-playing dimensions  
- Inactive relationships  
- Multi-grain fact tables  
- Advanced filtering logic  

---

# ## **3. Semi‑Additive Measures**

Semi‑additive measures **aggregate across some dimensions but not others**.

Common examples:
- Opening stock  
- Closing stock  
- Balance at end of period  
- Inventory levels  
- Headcount snapshots  

---

## **3.1 Closing Stock (Semi‑Additive Example)**
```DAX
Closing Stock =
CALCULATE(
    [Stock Movement],
    LASTDATE(DimDate[Date])
)
```

This returns the **last value** in the period, not the sum.

---

## **3.2 Opening Stock**
```DAX
Opening Stock =
CALCULATE(
    [Closing Stock],
    DATEADD(DimDate[Date], -1, DAY)
)
```

---

## **3.3 Why Semi‑Additive Measures Matter**
They are essential for:
- Inventory  
- Finance  
- HR  
- Subscription metrics  
- Any “snapshot” logic  

---

# ## **4. RANKX — Ranking with Row Context**

`RANKX` ranks values inside a table.

---

## **4.1 Basic Example — Rank Products by Revenue**
```DAX
Product Rank by Revenue =
RANKX(
    ALL(DimProduct),
    [Total Revenue],
    ,
    DESC
)
```

### What happens:
- `ALL(DimProduct)` removes filters  
- `[Total Revenue]` is evaluated for each product  
- RANKX assigns rank based on DESC order  

---

## **4.2 Rank Within Category**
```DAX
Product Rank Within Category =
RANKX(
    ALLEXCEPT(DimProduct, DimProduct[Category]),
    [Total Revenue],
    ,
    DESC
)
```

---

## **4.3 RANKX Performance Tips**
- Always use `ALL` or `ALLEXCEPT`  
- Avoid iterating large virtual tables  
- Use VAR to store measure results  

---

# ## **5. TOPN — Selecting the Top Rows**

`TOPN` returns a **table** containing the top N rows.

---

## **5.1 Example — Top 5 Products by Revenue**
```DAX
Top5Products =
TOPN(
    5,
    SUMMARIZE(
        FactSales,
        DimProduct[ProductID],
        "Revenue", [Total Revenue]
    ),
    [Revenue],
    DESC
)
```

---

## **5.2 TOPN Inside CALCULATE**
```DAX
Revenue of Top 5 Products =
CALCULATE(
    [Total Revenue],
    KEEPFILTERS(
        TOPN(
            5,
            DimProduct,
            [Total Revenue],
            DESC
        )
    )
)
```

---

## **5.3 Why TOPN Matters**
- Pareto analysis  
- ABC classification  
- Top customers  
- Top products  
- Top stores  

---

# ## **6. SUMMARIZECOLUMNS — The Modern Grouping Engine**

`SUMMARIZECOLUMNS` is the recommended replacement for `SUMMARIZE`.

It is:
- Faster  
- Relationship-aware  
- Filter-aware  
- Cleaner  

---

## **6.1 Example — Revenue by Product**
```DAX
RevenueByProduct =
SUMMARIZECOLUMNS(
    DimProduct[ProductID],
    "TotalRevenue", [Total Revenue]
)
```

---

## **6.2 Example — Revenue by Product and Category**
```DAX
RevenueByProductCategory =
SUMMARIZECOLUMNS(
    DimProduct[Category],
    DimProduct[ProductID],
    "TotalRevenue", [Total Revenue]
)
```

---

## **6.3 Why SUMMARIZECOLUMNS Is Better**
- Automatically applies filter context  
- Works with relationships  
- No need for CALCULATETABLE  
- No need for CURRENTGROUP()  

---

# ## **7. Composite Models — The Future of Power BI Modelling**

Composite models allow:
- DirectQuery + Import  
- Multiple data sources  
- Local model tables  
- Virtual relationships  
- Hybrid modelling  

---

## **7.1 Example Architecture**
- FactSales (DirectQuery)  
- DimProduct (Import)  
- DimDate (Import)  
- Local measures  
- Virtual relationships using TREATAS  

---

## **7.2 Why Composite Models Matter**
- Large datasets  
- Real-time reporting  
- Mixed storage modes  
- Enterprise BI  
- Performance optimisation  

---

## **7.3 Key Rules**
- Avoid complex DAX on DirectQuery tables  
- Use Import for dimensions  
- Use virtual relationships when physical ones are impossible  
- Use SUMMARIZECOLUMNS for grouping  

---

# ## **8. Summary Cheat Sheet**

```
Iterators          = powerful but expensive; use VAR and model logic
VirtualRelationships = TREATAS, CROSSFILTER, CALCULATETABLE
SemiAdditive       = closing stock, opening stock, balances
RANKX              = ranking with row context
TOPN               = top-N table selection
SUMMARIZECOLUMNS   = modern, fast grouping engine
CompositeModels    = hybrid DirectQuery + Import modelling
```

---