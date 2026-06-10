# 📊 Sales Intelligence Dashboard — Power BI Project

A four-page interactive Power BI dashboard built on a Superstore-style sales dataset (2015–2018), analysing business health, target achievement, manager performance, and delivery/returns risk for KS AI & Cloud Solutions.

---

## 🗂️ Project Structure

```
├── Data/
│   ├── Orders_2015.xlsx
│   ├── Orders_2016.xlsx
│   ├── Orders_2017.xlsx
│   ├── Orders_2018.xlsx
│   ├── Target.xlsx
│   ├── People.xlsx
│   └── Returns.xlsx
├── SalesDashboard.pbix
└── README.md
```

---

## 📁 Dataset Overview

| Table | Description |
|---|---|
| Orders (2015–2018) | Appended sales transactions — Order ID, Product, Category, Region, Sales, Profit, Ship Date, Ship Mode |
| Target | Monthly sales targets by Category and Manager |
| People | Manager-to-Region mapping |
| Returns | Returned orders with Order ID references |

---

## 🔧 Data Preparation (Power Query)

- **Orders:** Loaded all four Excel files individually and appended into a single `Orders` table. Converted Excel serial date numbers to proper date format for Order Date and Ship Date columns.
- **Target:** Converted text values like `"190K"` to numeric using a custom column formula. Promoted first row as headers.
- **People:** Promoted first row as headers.
- **Returns:** Promoted first row as headers. Relationship set as single-direction to avoid Many-to-Many issues.
- **Date Table:** Created via blank query using:
  ```
  = List.Dates(#date(2015,1,1), 1461, #duration(1,0,0,0))
  ```
  Expanded to full date table with Year, Month, Quarter columns for time-intelligence functions.

---

## 🗃️ Data Model

Star schema with `Orders` as the central fact table:

```
Date Table  ──────►  Orders  ◄──────  People
                        │
                        ▼
                     Returns
                     Target (via LOOKUPVALUE in DAX)
```

- `Date[Date]` → `Orders[Order Date]` (one-to-many)
- `People[Region]` → `Orders[Region]` (one-to-many)
- `Returns[Order ID]` → `Orders[Order ID]` (single-direction)

---

## 📐 DAX Measures

```dax
-- Total Sales
Total Sales = SUM(Orders[Sales])

-- Total Profit
Total Profit = SUM(Orders[Profit])

-- Profit Margin %
Profit Margin % = DIVIDE([Total Profit], [Total Sales], 0)

-- Previous Year Sales
PY Sales = CALCULATE([Total Sales], SAMEPERIODLASTYEAR('Date'[Date]))

-- YoY Growth %
YoY Growth % = DIVIDE([Total Sales] - [PY Sales], [PY Sales], 0)

-- Sales Target (LOOKUPVALUE)
Sales Target =
LOOKUPVALUE(
    Target[Sales Target],
    Target[Category], MAX(Orders[Category]),
    Target[Month], MAX('Date'[Month])
)

-- Target Achievement %
Target Achievement % = DIVIDE([Total Sales], [Sales Target], 0)

-- Total Returns
Total Returns = COUNTROWS(Returns)

-- Return Rate %
Return Rate % = DIVIDE([Total Returns], COUNTROWS(Orders), 0)

-- Delayed Orders (Ship Date - Order Date > 3 days)
Delayed Orders =
CALCULATE(
    COUNTROWS(Orders),
    Orders[Ship Date] - Orders[Order Date] > 3
)
```

---

## 📊 Dashboard Pages

### Page 1 — Business Health
Gives the executive view of overall sales performance.

| Visual | Details |
|---|---|
| KPI Cards (6) | Total Sales · Profit · Profit Margin % · YoY Growth % · Orders · Avg Order Value |
| Line Chart | Monthly sales trend with year-over-year overlay |
| Bar Chart | Sales by Region |
| Slicer | Year filter (2015–2018) |

**Key Insight:** 2.94M total sales, 54.97% YoY growth, 12.69% profit margin.

---

### Page 2 — Target Achievement
Tracks how well each category and manager is hitting sales targets.

| Visual | Details |
|---|---|
| Clustered Bar Chart | Sales vs Target by Category |
| Matrix | Manager × Category with Target Achievement % |
| Slicer | Year / Month |

**Key Insight:** Furniture targets were progressively reduced from 2015–2018, masking declining performance.

---

### Page 3 — Manager Performance
Compares profitability and growth across regional managers.

| Visual | Details |
|---|---|
| Table | Manager · Sales · Profit · Margin % · YoY Growth |
| Clustered Column | Sales by Category per Manager |
| Bar Chart | Profit Margin % by Manager |

**Key Insight:** Damala Kotsonis leads with 14.67% margin; Ross DeVincentis shows the highest growth but lowest margin (10.8%) — a red flag for cost efficiency.

---

### Page 4 — Risk & Delays
Surfaces operational risks around returns and shipping delays.

| Visual | Details |
|---|---|
| KPI Cards | Total Returns · Return Rate % · Delayed Orders |
| Bar Chart | Delayed Orders by Ship Mode |
| Bar Chart | Return Rate % by Category |
| Text Box | Key recommended actions |

**Key Insight:** Standard Class has a 99.9% delay rate. Technology has the highest return rate despite being the top revenue category.

---

## 🔑 Key Business Insights

1. **Standard Class shipping** accounts for nearly all delayed orders — operational overhaul needed.
2. **Furniture** shows declining profitability with artificially lowered targets masking the real performance gap.
3. **Ross DeVincentis** drives high growth but at the cost of margins — needs a pricing/discount review.
4. **Technology returns** at the highest rate — warrants a product quality and post-sales support review.

---

## 🛠️ Tools & Technologies

- **Power BI Desktop** — Dashboard development
- **Power Query (M Language)** — Data transformation and cleaning
- **DAX** — Measures and calculated columns
- **Excel (.xlsx)** — Source data files

---

## 🎥 Project Recording

Dashboard walkthrough recorded using **Loom** (6 minutes 48 seconds), covering all four pages, key DAX measures, data model, and business insights.

---

## 👤 Author

**Prahadheesh S**
Data Analyst | ECE Graduate — Anna University (2025)
[LinkedIn](https://in.linkedin.com/in/prahadheesh-s-a24537251) · [GitHub](https://github.com/Prahadheesh26)
