# 📘 **Power BI Relationship Pattern: Customer–Location–Sales (Snowflake with Inactive Relationship)**

## **1. Core Structure**
This modelling pattern is used when both **customers** and **sales** relate to a shared **location dimension**.

### **Active relationships**
- **FactSales[LocationID] → DimLocation[LocationID]**
- **DimCustomer[LocationID] → DimLocation[LocationID]**

### **Inactive relationship**
- **FactSales[CustomerID] → DimCustomer[CustomerID]**

Power BI deactivates the FactSales → DimCustomer relationship automatically to avoid ambiguous filter paths.

---

## **2. Why the Inactive Relationship Exists**
There are two possible filter paths between FactSales and DimCustomer:

- **Path A (active):**  
  FactSales → DimLocation → DimCustomer

- **Path B (inactive):**  
  FactSales → DimCustomer

Power BI disables Path B to prevent circular filtering.

This is expected and correct.

---

## **3. When This Model Is Correct**
Use this pattern when:

- Customers have a **home location** (suburb, region, postcode)
- Sales occur **at a location** (store, region)
- You want to analyse sales by customer location

In these cases, the indirect path via DimLocation is the correct business logic.

---

## **4. When to Activate the Inactive Relationship**
Activate **FactSales → DimCustomer** only inside DAX when you need **customer‑level analytics**, such as:

- Customer segmentation  
- Repeat customers  
- Customer lifetime value  
- Margin by customer type  
- Sales by customer segment  
- Customer retention  
- Customer profitability  

Use:

```DAX
CALCULATE(
    [Measure],
    USERELATIONSHIP(FactSales[CustomerID], DimCustomer[CustomerID])
)
```

This temporarily activates the relationship **only for that measure**.

---

## **5. When NOT to Activate It**
Do **not** activate the relationship when your analysis is:

- Store/location driven  
- Region driven  
- Suburb/postcode driven  
- SA3/SA4 driven  
- State driven  

In these cases, the correct path is:

**FactSales → DimLocation → DimCustomer**

---

## **6. Final Rule (the one-liner for your toolkit)**  
**Keep FactSales → DimCustomer inactive.  
Use USERELATIONSHIP only when you need customer‑level logic.  
Let DimLocation handle all location‑based filtering.**

