# Power BI Sales Management Dashboard

A 3-page interactive Power BI dashboard built on the AdventureWorks-style sales data model. Combines a star schema (Sales fact + Employee, Date, Product, Promotion, Sales Reviews dimensions), DAX measures for KPIs, and slicer-driven filtering for ad-hoc business exploration.

**Tech stack:** Power BI Desktop · DAX · Star schema modeling

---

## What's in the dashboard

### Page 1 — Sales Overview
- KPI cards: Total Sales, Total Orders, Average Order Value, YoY Growth
- Sales-over-time line chart with year/month drilldown
- Sales by region (map visual)
- Top products by revenue
- Filter slicers: Year, Sales Territory, Product Category

### Page 2 — Sales Details
- Employee performance scorecard (filtered to active sales employees via `Is Sales Employee = 1`)
- Sales by employee with base rate vs. earned commission comparison
- Promotion effectiveness — Sales Amount and Order Quantity by Promotion Type
- Drill-through into individual employee detail

### Page 3 — Salary Analysis
- Employee compensation vs. performance review scores
- Sales Reviews integration (1:1 relationship to Employee via EmployeeID → Employee Key)
- Outliers: highest performers by review score, lowest base rate
- Salary distribution by Title and Sales Territory

## Data model

```
       Date ───────────┐
                       │
   Promotion ───────── Sales ─────── Product
                       │
                    Employee ─── Sales Reviews
```

- **Sales** (fact): Discount Amount, Employee Key, Extended Amount, Freight, Sales Amount, Order Quantity, Sales Order Number, Order Date Key, Product Key, Promotion Key
- **Employee**: Employee Key, Base Rate, First Name, Last Name, Full Name (calculated), Sales Territory Key, Title
- **Date**: Date Key, Calendar Year, Day Number Of Month, Full Date Alternate Key
- **Product**: Model Name, List Price, Product Key
- **Promotion**: Promotion Key, Promotion Type, Promotion Name
- **Sales Reviews**: EmployeeID, FirstName, JobTitle, LastName, Review Overall Score

5 active relationships across the model. All many-to-one from Sales to dimensions, plus a 1:1 from Sales Reviews to Employee.

## Key DAX measures

```dax
-- Filter for sales staff only (used on pages 2 and 3)
Is Sales Employee = IF(NOT(ISBLANK(Employee[Sales Territory Key])), 1, 0)

-- Sales metrics
Total Sales = SUM(Sales[Sales Amount])
Total Orders = DISTINCTCOUNT(Sales[Sales Order Number])
Average Order Value = DIVIDE([Total Sales], [Total Orders])

-- Year-over-year growth
Sales Previous Year = CALCULATE([Total Sales], SAMEPERIODLASTYEAR(Date[Full Date Alternate Key]))
YoY Growth = DIVIDE([Total Sales] - [Sales Previous Year], [Sales Previous Year])
```

## How to open

1. Install [Power BI Desktop](https://powerbi.microsoft.com/desktop/) (Windows only — free)
2. Open `sales_dashboard.pbix`
3. The dashboard loads the embedded sample data; no external connections needed

## Screenshots

Screenshots of each page live in `screenshots/`. Captured at 1920×1080 from Power BI Desktop.

*(Note: screenshots will be added when the project is captured from a Windows machine.)*

## What I learned

- The star schema's whole point is making DAX measures cheap to write — when relationships are right, `SUM(Sales[Sales Amount])` filtered by any dimension column "just works."
- Time intelligence functions like `SAMEPERIODLASTYEAR` only work if you have a proper Date table with a continuous date range marked as the table's date column.
- Drill-through pages (right-click a data point → drill through) are a much better UX for "show me details on this thing" than cramming everything onto one page.
- Slicer panel design matters: putting slicers in a consistent location across all pages keeps the filter context predictable for the user.

---

*Built as the final project for WCTC's Data Visualization course. Demonstrates star schema modeling, DAX measures, and interactive dashboard design — the foundation of any BI analyst's day-to-day work.*
