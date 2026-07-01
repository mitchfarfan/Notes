# ⭐ **COMMERCIAL KPI TOOLKIT**  
These are the core commercial analytics KPIs used in retail, FMCG, logistics, and supply chain.

I’ll assume your model has:

- `FactSales[NetRevenue]`
- `FactSales[COGS]`
- `FactInventory[StockOnHand]`
- `FactInventory[DaysOfSupply]`
- `FactOrders[DeliveredOnTime]`
- `FactOrders[TotalOrders]`

If your column names differ, I’ll adjust them.

---

## **1. Gross Margin %**
```
Gross Margin % =
    DIVIDE(
        [Total Revenue] - [Total COGS],
        [Total Revenue]
    )
```

## **2. Days of Inventory on Hand (DOH)**
```
DOH =
    DIVIDE(
        [Average Inventory Value],
        [Average Daily COGS]
    )
```

If you want, I can generate:

- `Average Inventory Value`  
- `Average Daily COGS`  

based on your fact tables.

---

## **3. Stock Turns**
```
Stock Turns =
    DIVIDE(
        [Total COGS],
        [Average Inventory Value]
    )
```

---

## **4. OTIF (On‑Time In‑Full)**
```
OTIF % =
    DIVIDE(
        [Orders Delivered On Time],
        [Total Orders]
    )
```

---

## **5. Return Rate**
```
Return Rate % =
    DIVIDE([Total Returns], [Total Orders])
```

---

## **6. Fill Rate**
```
Fill Rate % =
    DIVIDE([Units Shipped], [Units Ordered])
```

---

## ⭐ **If you want, I can generate the FULL commercial KPI layer:**

- GM$  
- GM%  
- Contribution Margin  
- DOH  
- DOS (Days of Supply)  
- Stock Turns  
- OTIF  
- DIFOT  
- Service Level  
- Backorder Rate  
- Perfect Order %  
- Shrinkage %  
- Sell‑Through %  
- Markdown %  
- Price Realisation %  
- Promo Uplift %  
- Basket Size  
- Basket Value  

All clean, all structured, all GitHub‑ready.

Just tell me which ones you want next.