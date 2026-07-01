Counting Functions.md

---

# 📘 **DAX Counting Functions — Reference Notes**  
A practical guide to understanding how DAX counts rows, values, and filtered contexts.  
Use this as your mental model when building base measures.

---

## 🧱 **1. COUNTROWS(table)**  
Counts **rows in a table**.

### ✔ Correct usage
```DAX
Sales Row Count = COUNTROWS(FactSales)
```

### ✔ What it does  
- Returns the number of rows in the table **after filters are applied**  
- Automatically respects relationships  
- Automatically filters by dimensions on the visual

### ✔ When to use  
- Fact table validation  
- Debugging relationships  
- Base measures  
- When you want “how many transactions / rows exist?”

### ❌ Common mistake  
`COUNTROWS([FactSales], SalesID)`  
- COUNTROWS **never** takes a column  
- COUNTROWS **never** takes two arguments  
- The argument must be a **table**, not a column

---

## 🔢 **2. COUNT(column)**  
Counts **non‑blank values** in a column.

### ✔ Example
```DAX
Count of SalesID = COUNT(FactSales[SalesID])
```

### ✔ What it does  
- Counts only non‑blank values  
- Ignores blanks  
- Equivalent to COUNTROWS when the column is always populated

### ✔ When to use  
- When you need to count values in a specific column  
- When the column may contain blanks  
- When you want to count “how many IDs exist?”

---

## 🔁 **3. DISTINCTCOUNT(column)**  
Counts **unique values** in a column.

### ✔ Example
```DAX
Distinct Customers = DISTINCTCOUNT(FactSales[CustomerID])
```

### ✔ What it does  
- Counts unique values  
- Respects filter context  
- Useful for customer counts, product counts, store counts

### ✔ When to use  
- Customer metrics  
- Product penetration  
- Store coverage  
- Unique transaction types

---

## 🔍 **4. COUNTX(table, expression)**  
Iterates a table and counts rows where the expression is **not blank**.

### ✔ Example
```DAX
Count Valid Sales =
COUNTX(
    FactSales,
    FactSales[UnitsSold]
)
```

### ✔ What it does  
- Evaluates the expression for each row  
- Counts rows where the expression returns a non‑blank value  
- More flexible than COUNT or COUNTROWS

### ✔ When to use  
- Conditional counting  
- Counting based on calculated expressions  
- Counting rows where a condition is met

---

# 🎯 **5. The Mental Model (the part most people never internalise)**

### ✔ COUNTROWS  
Counts rows in a **table**.

### ✔ COUNT  
Counts **non‑blank values** in a **column**.

### ✔ DISTINCTCOUNT  
Counts **unique values** in a **column**.

### ✔ COUNTX  
Counts rows where an **expression** is non‑blank.

### ✔ You NEVER pass a column into COUNTROWS  
This is the mistake that causes confusion.

---

# 🧠 **6. How counting interacts with filter context**

### Example  
Visual axis: `DimProduct[ProductName]`  
Measure:

```DAX
Sales Row Count = COUNTROWS(FactSales)
```

DAX automatically filters `FactSales` by each product row context.

You do **not** need:

- GROUP BY  
- COUNTROWS(FactSales, ProductID)  
- COUNT(FactSales[ProductID])  

Filter context handles it for you.

This is why base measures stay simple.

---

# 🧩 **7. Quick reference table**

| Function | Input | Counts | Use Case |
|---------|--------|--------|----------|
| **COUNTROWS** | Table | Rows | Fact table row counts |
| **COUNT** | Column | Non‑blank values | Counting IDs, values |
| **DISTINCTCOUNT** | Column | Unique values | Customer/product/store counts |
| **COUNTX** | Table + Expression | Non‑blank expression results | Conditional counting |

---


