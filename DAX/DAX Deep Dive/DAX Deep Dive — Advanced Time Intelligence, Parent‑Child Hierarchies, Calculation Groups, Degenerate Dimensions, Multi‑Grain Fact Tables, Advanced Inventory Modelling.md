# **📘 DAX Deep Dive — Advanced Time Intelligence, Parent‑Child Hierarchies, Calculation Groups, Degenerate Dimensions, Multi‑Grain Fact Tables, Advanced Inventory Modelling**

A complete modelling‑level guide.

---

# ## **1. Advanced Time Intelligence**

Time intelligence becomes “advanced” when you start manipulating **custom calendars**, **non-standard periods**, and **offset logic**.

---

## **1.1 Custom Fiscal Calendar (Non‑Jan Start)**

If your fiscal year starts in July:

```DAX
Fiscal YTD =
CALCULATE(
    [Total Revenue],
    DATESYTD(
        DimDate[Date],
        "06-30"   // Fiscal year ends June 30
    )
)
```

---

## **1.2 Rolling Windows (Flexible Periods)**

Rolling 90 days:

```DAX
Rolling 90D Revenue =
CALCULATE(
    [Total Revenue],
    DATESINPERIOD(
        DimDate[Date],
        MAX(DimDate[Date]),
        -90,
        DAY
    )
)
```

---

## **1.3 Offset Measures (Period Shifting)**

Previous quarter:

```DAX
Previous Quarter Revenue =
CALCULATE(
    [Total Revenue],
    DATEADD(DimDate[Date], -1, QUARTER)
)
```

---

## **1.4 Non‑Additive Time Intelligence (Snapshots)**

End-of-month inventory:

```DAX
End of Month Stock =
CALCULATE(
    [Closing Stock],
    ENDOFMONTH(DimDate[Date])
)
```

---

## **Mental Model**
> Advanced time intelligence = “custom periods, rolling windows, offsets, and snapshots.”

---

# ## **2. Parent‑Child Hierarchies**

Used for:
- Org charts  
- Chart of accounts  
- Product category trees  
- Store → Region → State hierarchies  

---

## **2.1 PATH — Build the Hierarchy Chain**

```DAX
HierarchyPath =
PATH(DimEmployee[EmployeeID], DimEmployee[ManagerID])
```

---

## **2.2 PATHITEM — Extract Levels**

```DAX
Level1 = PATHITEM(DimEmployee[HierarchyPath], 1)
Level2 = PATHITEM(DimEmployee[HierarchyPath], 2)
```

---

## **2.3 PATHLENGTH — Count Levels**

```DAX
HierarchyDepth = PATHLENGTH(DimEmployee[HierarchyPath])
```

---

## **2.4 Aggregating Over Parent‑Child**

```DAX
Total Under Manager =
CALCULATE(
    [Total Revenue],
    TREATAS(
        PATH(DimEmployee[EmployeeID], DimEmployee[ManagerID]),
        DimEmployee[EmployeeID]
    )
)
```

---

## **Mental Model**
> Parent‑child = “PATH builds the chain, PATHITEM extracts levels, PATHLENGTH counts depth.”

---

# ## **3. Calculation Groups (Tabular Editor)**

Calculation groups allow you to apply logic **across all measures** without rewriting DAX.

---

## **3.1 Example — Time Intelligence Calculation Group**

**Name:** Time Intelligence  
**Item:** YTD  
**Expression:**

```DAX
CALCULATE(
    SELECTEDMEASURE(),
    DATESYTD(DimDate[Date])
)
```

This applies YTD to **any measure** automatically.

---

## **3.2 Example — Currency Conversion Group**

```DAX
SELECTEDMEASURE() * SELECTEDVALUE(DimCurrency[Rate])
```

---

## **3.3 Why Calculation Groups Matter**
- Reduce duplicated DAX  
- Standardise logic  
- Apply transformations globally  
- Essential for enterprise models  

---

## **Mental Model**
> Calculation groups = “global transformations applied to SELECTEDMEASURE().”

---

# ## **4. Degenerate Dimensions**

Degenerate dimensions are dimensions stored **inside fact tables**.

Examples:
- Order Number  
- Invoice Number  
- Transaction ID  
- Line Number  

These don’t have separate dimension tables because:
- They have extremely high cardinality  
- They don’t need attributes  
- They are only used for slicing or grouping  

---

## **4.1 Example — Degenerate Dimension in FactSales**

FactSales contains:
- OrderID  
- LineNumber  

You can still slice by OrderID without a dimension table.

---

## **4.2 When to Create a Dimension Anyway**
Create a dimension if:
- You need attributes (Order Date, Order Type)  
- You need relationships  
- You need hierarchies  

---

## **Mental Model**
> Degenerate dimensions = “dimension keys stored directly in the fact table.”

---

# ## **5. Multi‑Grain Fact Tables**

Occurs when different fact tables have **different levels of granularity**.

Examples:
- Daily inventory snapshots vs. per‑transaction sales  
- Monthly budgets vs. daily actuals  
- Store-level targets vs. product-level sales  

---

## **5.1 Example — Daily Inventory + Transactional Sales**

### FactInventory (daily)
- Date  
- ProductID  
- ClosingStock  

### FactSales (transactional)
- DateTime  
- ProductID  
- UnitsSold  

---

## **5.2 Handling Multi‑Grain with Relationships**

Use:
- Role-playing date dimensions  
- Virtual relationships  
- Calculation groups  
- Aggregation tables  

---

## **5.3 Example — Align Daily Inventory with Sales**

```DAX
Inventory on Sales Date =
CALCULATE(
    [Closing Stock],
    TREATAS(
        VALUES(FactSales[Date]),
        DimDate[Date]
    )
)
```

---

## **Mental Model**
> Multi‑grain = “facts at different levels require virtual relationships and careful alignment.”

---

# ## **6. Advanced Inventory Modelling (FIFO, LIFO, WAC Layers)**

This is specialist-level modelling.

---

## **6.1 FIFO (First In, First Out)**

FIFO requires:
- Layered inventory  
- Consumption from oldest layer first  
- Virtual tables to simulate layers  

### **FIFO Example (Simplified)**

```DAX
FIFO Cost =
SUMX(
    FILTER(
        InventoryLayers,
        InventoryLayers[Consumed] > 0
    ),
    InventoryLayers[Consumed] * InventoryLayers[UnitCost]
)
```

---

## **6.2 LIFO (Last In, First Out)**

Same logic, but consume newest layer first.

```DAX
LIFO Cost =
SUMX(
    FILTER(
        InventoryLayers,
        InventoryLayers[Consumed] > 0
    ),
    InventoryLayers[Consumed] * InventoryLayers[UnitCost]
)
```

---

## **6.3 WAC (Weighted Average Cost)**

WAC is the simplest and most common.

```DAX
WAC =
DIVIDE(
    SUMX(FactInventory, FactInventory[Units] * FactInventory[UnitCost]),
    SUM(FactInventory[Units])
)
```

---

## **6.4 Why FIFO/LIFO Are Hard in DAX**
- They require row-by-row consumption logic  
- They require virtual tables  
- They require ordering  
- They require layer simulation  

Most companies use SQL or warehouse logic for FIFO/LIFO.

---

## **Mental Model**
> FIFO/LIFO = layered consumption  
> WAC = weighted average  
> Inventory modelling = “row logic + virtual tables + ordering.”

---

# ## **7. Summary Cheat Sheet**

```
Advanced Time Intelligence = custom calendars, rolling windows, offsets
Parent-Child Hierarchies   = PATH, PATHITEM, PATHLENGTH
Calculation Groups         = global transformations via SELECTEDMEASURE()
Degenerate Dimensions      = dimension keys inside fact tables
Multi-Grain Fact Tables    = align facts with different granularity
Advanced Inventory         = FIFO/LIFO layers, WAC averages
```

---