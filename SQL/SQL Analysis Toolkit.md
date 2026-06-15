# SQL Analysis Toolkit — AdventureWorks Portfolio

A reusable library of SQL patterns used across modules 3A–3H.

This toolkit summarises the core SQL techniques used to produce the analytical outputs in the AdventureWorks project. Each pattern is written to be reusable across BI, analytics, and data modelling work.

---

# 1. Revenue Ranking Pattern (3A)

**Purpose:** Identify top revenue SKUs, revenue concentration, and dependency risk.

## Core SQL Techniques Used
- CTE for revenue calculation  
  "The CTE isolates the revenue calculation so the ranking logic stays clean."
- Window functions for:
  - percent of total revenue  
  - running cumulative revenue  
  - ranking by revenue  
- Aggregation of quantity × price  
- ORDER BY with LIMIT for top N extraction  

## Reusable Pattern
```sql
WITH revenue_calc AS (
    SELECT
        fs.productkey,
        dp.productsku,
        dp.productname,
        SUM(fs.orderquantity * dp.productprice) AS total_revenue
    FROM aw.fact_sales fs
    LEFT JOIN aw.dim_products dp ON fs.productkey = dp.productkey
    GROUP BY fs.productkey, dp.productsku, dp.productname
)
SELECT
    productkey,
    productsku,
    productname,
    total_revenue,
    total_revenue / SUM(total_revenue) OVER () * 100 AS pct_of_total_revenue,
    SUM(total_revenue) OVER (ORDER BY total_revenue DESC)
        / SUM(total_revenue) OVER () * 100 AS running_pct_revenue,
    RANK() OVER (ORDER BY total_revenue DESC) AS revenue_rank
FROM revenue_calc
ORDER BY revenue_rank;
```

---

# 2. Profitability Ranking Pattern (3B)

**Purpose:** Identify high profit SKUs and margin concentration.

## Core SQL Techniques Used
- Two stage CTE pipeline  
  "The first CTE aggregates quantity, sales, and cost. The second CTE calculates profit cleanly."
- Profit calculation: total_sales - total_cost  
- Window functions for:
  - percent of total profit  
  - running cumulative profit  
  - ranking by profit  

## Reusable Pattern
```sql
WITH product_sales AS (
    SELECT
        fs.productkey,
        dp.productsku,
        dp.productname,
        SUM(fs.orderquantity) AS qty,
        SUM(fs.orderquantity * dp.productprice) AS total_sales,
        SUM(fs.orderquantity * dp.productcost) AS total_cost
    FROM aw.fact_sales fs
    LEFT JOIN aw.dim_products dp ON fs.productkey = dp.productkey
    GROUP BY fs.productkey, dp.productsku, dp.productname
),
profit_calc AS (
    SELECT *,
           total_sales - total_cost AS total_profit
    FROM product_sales
)
SELECT
    *,
    total_profit / SUM(total_profit) OVER () * 100 AS pct_of_total_profit,
    SUM(total_profit) OVER (ORDER BY total_profit DESC)
        / SUM(total_profit) OVER () * 100 AS running_pct_profit,
    RANK() OVER (ORDER BY total_profit DESC) AS profit_rank
FROM profit_calc
ORDER BY profit_rank;
```

---

# 3. Time Series Trend Detection (3C)

**Purpose:** Detect declining SKUs using moving averages.

## Core SQL Techniques Used
- Monthly aggregation CTE  
  "The CTE creates a clean monthly summary table."
- 3 month moving average using window frames:  
  `ROWS BETWEEN 2 PRECEDING AND CURRENT ROW`
- Trend comparison: qty < moving_avg  

## Reusable Pattern
```sql
WITH monthly_sales AS (
    SELECT
        DATE_TRUNC('month', fs.orderdate) AS month,
        dp.productname,
        SUM(fs.orderquantity) AS qty
    FROM aw.fact_sales fs
    LEFT JOIN aw.dim_products dp ON fs.productkey = dp.productkey
    GROUP BY month, dp.productname
)
SELECT
    month,
    productname,
    qty,
    AVG(qty) OVER (
        PARTITION BY productname
        ORDER BY month
        ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
    ) AS qty_moving_avg_3m
FROM monthly_sales
ORDER BY productname, month;
```

---

# 4. Lowest Profit SKU Identification (3D)

**Purpose:** Identify long tail SKUs with low contribution.

## Core SQL Techniques Used
- Same CTE structure as 3B  
- Ranking by total_profit ASC  
- Interpretation: low profit due to low demand, not margin weakness  
  "These products generate minimal total profit simply because they have weak demand."

## Reusable Pattern
```sql
SELECT *
FROM profit_calc
ORDER BY total_profit ASC;
```

---

# 5. Customer Lifetime Value Distribution (3E)

**Purpose:** Identify whether revenue is concentrated or fragmented.

## Core SQL Techniques Used
- Aggregation by customerkey  
- Percent of total revenue  
- Ranking by revenue  

## Reusable Pattern
```sql
SELECT
    fs.customerkey,
    SUM(fs.orderquantity * dp.productprice) AS customer_revenue,
    SUM(fs.orderquantity * dp.productprice)
        / SUM(SUM(fs.orderquantity * dp.productprice)) OVER () * 100 AS pct_total_revenue,
    RANK() OVER (ORDER BY SUM(fs.orderquantity * dp.productprice) DESC) AS revenue_rank
FROM aw.fact_sales fs
LEFT JOIN aw.dim_products dp ON fs.productkey = dp.productkey
GROUP BY fs.customerkey
ORDER BY revenue_rank;
```

---

# 6. Daily Anomaly Detection (3F)

**Purpose:** Identify spikes/dips in daily sales.

## Core SQL Techniques Used
- Daily aggregation  
- Deviation from moving average  
- Flagging anomalies  

## Reusable Pattern
```sql
WITH daily_sales AS (
    SELECT
        fs.orderdate::date AS day,
        SUM(fs.orderquantity * dp.productprice) AS revenue
    FROM aw.fact_sales fs
    LEFT JOIN aw.dim_products dp ON fs.productkey = dp.productkey
    GROUP BY day
)
SELECT
    day,
    revenue,
    AVG(revenue) OVER (ORDER BY day ROWS BETWEEN 6 PRECEDING AND CURRENT ROW) AS avg_7d,
    revenue - AVG(revenue) OVER (ORDER BY day ROWS BETWEEN 6 PRECEDING AND CURRENT ROW) AS deviation
FROM daily_sales
ORDER BY day;
```

---

# 7. Sales Distribution Analysis (3G)

**Purpose:** Understand right skewed SKU distribution.

## Core SQL Techniques Used
- Aggregation by product  
- Percent of total quantity  
- Distribution curve creation  

## Reusable Pattern
```sql
SELECT
    dp.productname,
    SUM(fs.orderquantity) AS total_qty,
    SUM(fs.orderquantity) / SUM(SUM(fs.orderquantity)) OVER () * 100 AS pct_of_total_qty
FROM aw.fact_sales fs
LEFT JOIN aw.dim_products dp ON fs.productkey = dp.productkey
GROUP BY dp.productname
ORDER BY total_qty DESC;
```

---

# 8. Margin Analysis (3H)

**Purpose:** Compare margin % across categories.

## Core SQL Techniques Used
- Margin calculation: (price - cost) / price  
- Category level aggregation  
- Ranking by margin %  

## Reusable Pattern
```sql
SELECT
    dp.productcategory,
    SUM(fs.orderquantity * (dp.productprice - dp.productcost))
        / SUM(fs.orderquantity * dp.productprice) AS margin_pct
FROM aw.fact_sales fs
LEFT JOIN aw.dim_products dp ON fs.productkey = dp.productkey
GROUP BY dp.productcategory
ORDER BY margin_pct DESC;
```

