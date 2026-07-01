# 📘 **Base Measures Toolkit (MetroNova Model)**  
A reusable library of foundational DAX measures for fact tables.  
These measures form the backbone of all reporting, time intelligence, and commercial analytics.

---

## 🧱 **1. Row Count Measures**  
Row count measures are used to validate fact table loads, check relationships, and support debugging.

```DAX
Sales Row Count = COUNTROWS(FactSales)

Returns Row Count = COUNTROWS(FactReturns)

Inventory Row Count = COUNTROWS(FactInventory)

Finance Row Count = COUNTROWS(FactFinance)

CostChanges Row Count = COUNTROWS(FactCostChanges)
```

**Pattern:**  
```DAX
<FactName> Row Count = COUNTROWS(<FactName>)
```

---

## 📦 **2. Quantity Measures**

```DAX
Total Units Sold = SUM(FactSales[UnitsSold])

Total Units Returned = SUM(FactReturns[UnitsReturned])

Total Stock Received = SUM(FactInventory[StockReceivedQty])

Total Stock Sold = SUM(FactInventory[StockSoldQty])
```

---

## 💰 **3. Revenue Measures**

```DAX
Total Revenue = SUM(FactSales[NetRevenue])

Original Price Total = SUMX(FactSales, FactSales[UnitsSold] * FactSales[OriginalPrice])

Discount Amount Total = SUM(FactSales[DiscountAmount])

Net Revenue = SUM(FactSales[NetRevenue])
```

---

## 🏷 **4. Cost Measures**

```DAX
Total COGS = SUM(FactSales[COGS])

Total Unit Cost = SUMX(FactSales, FactSales[UnitsSold] * FactSales[UnitCost])

Total Return Cost = SUM(FactReturns[ReturnCost])
```

---

## 📊 **5. Margin Measures**

```DAX
Total Margin = SUM(FactSales[Margin])

Margin % = DIVIDE([Total Margin], [Total Revenue])
```

---

## 🔄 **6. Inventory Measures**

```DAX
Opening Stock = SUM(FactInventory[OpeningStockQty])

Closing Stock = SUM(FactInventory[ClosingStockQty])

Stock Movement = [Opening Stock] + [Total Stock Received] - [Total Stock Sold]
```

Weighted Average Cost (WAC) after movement:

```DAX
WAC After Movement = AVERAGE(FactInventory[WACAfterMovement])
```

---

## 🔁 **7. Cost Change Measures**

Latest cost per product:

```DAX
Latest Cost =
VAR LatestDate =
    CALCULATE(
        MAX(FactCostChanges[ChangeDateKey]),
        ALLEXCEPT(FactCostChanges, FactCostChanges[ProductID])
    )
RETURN
    CALCULATE(
        MAX(FactCostChanges[NewCost]),
        FactCostChanges[ChangeDateKey] = LatestDate
    )
```

Cost delta:

```DAX
Cost Delta = [Latest Cost] - [WAC After Movement]
```

---

## 🕒 **8. Time Intelligence Wrappers**

### YTD Revenue
```DAX
YTD Revenue =
TOTALYTD(
    [Total Revenue],
    DimDate[Date]
)
```

### Rolling 12 Months Revenue
```DAX
Rolling 12M Revenue =
CALCULATE(
    [Total Revenue],
    DATESINPERIOD(DimDate[Date], MAX(DimDate[Date]), -12, MONTH)
)
```

### YoY Revenue
```DAX
YoY Revenue =
VAR Current = [Total Revenue]
VAR Prior =
    CALCULATE(
        [Total Revenue],
        SAMEPERIODLASTYEAR(DimDate[Date])
    )
RETURN
    Current - Prior
```

### YoY %
```DAX
YoY % =
DIVIDE(
    [YoY Revenue],
    CALCULATE([Total Revenue], SAMEPERIODLASTYEAR(DimDate[Date]))
)
```

---

## 🗂 **9. Recommended Measure Folder Structure**

```
📁 Sales
    - Total Revenue
    - Net Revenue
    - Units Sold
    - Discount Amount
    - YoY Revenue
    - YTD Revenue
    - Rolling 12M Revenue

📁 Cost
    - Total COGS
    - Unit Cost Total
    - Return Cost
    - Latest Cost
    - Cost Delta

📁 Margin
    - Total Margin
    - Margin %

📁 Inventory
    - Opening Stock
    - Closing Stock
    - Stock Movement
    - WAC After Movement

📁 Returns
    - Units Returned
    - Refund Amount

📁 Time Intelligence
    - YTD
    - Rolling
    - YoY

📁 Validation
    - Sales Row Count
    - Returns Row Count
    - Inventory Row Count
    - Finance Row Count
    - CostChanges Row Count
```

---

## 🎯 **10. Usage Notes**
- All measures rely on **filter context**, so they automatically aggregate by product, customer, store, region, etc.  
- Use these as **base measures**, then build KPI measures on top (GM%, DOH, Stock Turns, etc.).  
- Keep base measures **simple and atomic** — no business logic inside them.  
- This file becomes the foundation of your **DAX Pattern Library**.

