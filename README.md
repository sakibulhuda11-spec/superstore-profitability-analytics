# 📊 Superstore Profitability Analytics
## *End-to-End Business Intelligence Pipeline*

### 📋 Project Overview
This project addresses a critical business paradox: **High Revenue ($2.3M) but Low Profitability (12.5% Margin).** 

By building a multi-stage pipeline, I analyzed 9,994 transactions to pinpoint exactly where capital is being lost. The project demonstrates a full-stack data workflow: **Data Engineering (Python) → Relational Database Modeling (SQL) → Business Logic (DAX) → Visual Storytelling (Power BI).**

---

### 🏗️ Pipeline Architecture

1.  **Data Extraction & Cleaning (Python):** 
    *   Standardized disparate column naming conventions for SQL compatibility.
    *   Handled data types (datetime conversion) and engineered features: `profit_margin`, `shipping_days`, and `discount_buckets`.
2.  **Relational Storage (PostgreSQL):** 
    *   Migrated cleaned DataFrames into a PostgreSQL environment using `SQLAlchemy`.
    *   Applied indexing on `order_date` and `customer_id` for query optimization.
3.  **Analytical Processing (SQL):** 
    *   Leveraged **CTEs (Common Table Expressions)** for modular query design.
    *   Used **Window Functions** to rank product performance and calculate running totals.
4.  **Visualization & Dashboarding (Power BI):** 
    *   Modeled a Star Schema (fact and dimension tables).
    *   Developed custom **DAX measures** for dynamic period-over-period analysis.

---

### 🔍 Deep-Dive Insights

#### 1. The Discount Correlation & Loss Attribution
Using a `CASE WHEN` bucketing strategy in SQL, I discovered a "Profit Cliff."
*   **Insight:** Discounts between 0–20% maintain a healthy **20%+ margin**.
*   **The Tipping Point:** At 21% discount, the margin drops to **-15%**. At 50%+ discount, the margin collapses to **-110%**.
*   **Loss Value:** Orders with >20% discount accounted for **$135,129 in pure profit loss**.

#### 2. Sub-Category Performance Variance
I used the `RANK()` window function to evaluate performance within categories:
*   **The Bleeders:** `Tables`, `Bookcases`, and `Supplies` showed negative net profit across all four years.
*   **The Drivers:** `Copiers` and `Phones` carry the highest profit-per-unit, suggesting a need for inventory reallocation.

#### 3. Geographic Profitability Decay
Analysis by State revealed that high-revenue states like **Texas** and **Ohio** are heavily unprofitable due to aggressive localized discounting strategies.
*   **Texas:** $170k Revenue | **-$25k Loss** | ~37% Avg. Discount
*   **California:** $457k Revenue | **+$76k Profit** | ~7% Avg. Discount

---

### 🛠️ Technical Implementation Highlights

#### Advanced SQL: Year-over-Year Growth
```sql
WITH yearly_sales AS (
    SELECT year, SUM(sales) as total_sales
    FROM orders
    GROUP BY 1
)
SELECT year, total_sales,
       LAG(total_sales) OVER (ORDER BY year) as prev_year_sales,
       (total_sales - LAG(total_sales) OVER (ORDER BY year)) / 
       LAG(total_sales) OVER (ORDER BY year) * 100 as growth_pct
FROM yearly_sales;
```

#### Power BI DAX: Dynamic Margin
```dax
Profit Margin % = 
VAR TotalProfit = SUM(Orders[Profit])
VAR TotalSales = SUM(Orders[Sales])
RETURN
DIVIDE(TotalProfit, TotalSales, 0)
```

---

### 💡 Strategic Recommendations

1.  **Discount Threshold Policy:** Implement an automated block on discounts exceeding 20% in the POS (Point of Sale) system.
2.  **Product Sunsetting:** Evaluate the supply chain costs for `Tables`. If logistics costs cannot be reduced by 15%, consider removing this line from the "Home Office" segment.
3.  **Geographic Re-pricing:** Adjust the base price in "Loss States" (TX, OH, PA, IL) to offset the high cost of local competition and shipping.
4.  **Customer Tiering:** Use the VIP classification identified in the SQL segment to launch a high-margin "Executive" loyalty program targeting the Home Office segment.

---

### 📂 File Guide
*   `/Notebooks`: Jupyter Notebook with full Python EDA.
*   `/SQL`: Scripts for Table Creation, Window Functions, and CTEs.
*   `/Dashboard`: `.pbix` file and screenshots of the 3-page report.
*   `/Exports`: Final Excel summary with conditional formatting.

Does this level of detail work for your portfolio, or should we expand more on the specific Python libraries used?