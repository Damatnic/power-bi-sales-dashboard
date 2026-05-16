# Power BI Sales Management Dashboard

A three-page interactive Power BI dashboard on an AdventureWorks-style sales data model. Star schema (Sales fact + Employee, Date, Product, Promotion, Sales Reviews dimensions), DAX measures for the KPIs, and slicer-driven filtering for ad-hoc exploration.

**Stack:** Power BI Desktop · DAX · Star schema modeling · PBIP (Power BI Project)

The repo stores a **[Power BI Project](https://learn.microsoft.com/en-us/power-bi/developer/projects/projects-overview)** (`sales_dashboard.pbip`) so the report theme and definitions are plain text under Git — including the custom **`DamatoProfessional`** dark theme aligned with my portfolio site.

## Quick links

| | URL |
|---|---|
| **Download ZIP** | https://github.com/Damatnic/power-bi-sales-dashboard/archive/refs/heads/main.zip |
| **Portfolio case study** | https://damato-data.vercel.app/projects/power-bi-sales-dashboard |

Clone this repo:

```bash
git clone https://github.com/Damatnic/power-bi-sales-dashboard.git
cd power-bi-sales-dashboard
# Open sales_dashboard.pbip in Power BI Desktop (Windows), or open definition.pbir inside sales_dashboard.Report/
```

Need a single `.pbix` file for sharing? Open the project in Desktop and use **File → Save As → Power BI Desktop files (*.pbix)**.

## What's in the dashboard

### Page 1: Sales Overview
- KPI cards: Total Sales, Total Orders, Average Order Value, YoY Growth
- Sales-over-time line chart with year/month drilldown
- Sales by region on a map visual
- Top products by revenue
- Slicers: Year, Sales Territory, Product Category

### Page 2: Sales Details
- Employee performance scorecard, filtered to active sales staff via `Is Sales Employee = 1`
- Sales by employee with base rate vs earned commission compared side by side
- Promotion effectiveness: Sales Amount and Order Quantity by Promotion Type
- Drill-through into individual employee detail

### Page 3: Salary Analysis
- Employee compensation vs performance review scores
- Sales Reviews integration via a 1:1 relationship (EmployeeID to Employee Key)
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

Five active relationships across the model. All many-to-one from Sales to the dimensions, plus a 1:1 from Sales Reviews to Employee.

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

1. Install [Power BI Desktop](https://powerbi.microsoft.com/desktop/). Windows only, free. Enable **Preview features → Power BI Project (.pbip) save option** if your build asks for it.
2. Clone or unzip this repo, then open **`sales_dashboard.pbip`** (or **`sales_dashboard.Report/definition.pbir`**) from the repo root.
3. The semantic model loads embedded sample data — no external connections needed.

## Screenshots

Exports for the **portfolio site** live in the Next.js repo at  
`damato-portfolio/public/projects/power-bi/` (`page-1-sales-overview.png`, etc.).  
Right now those files are **layout placeholders**; swap them for real **1920×1080**
Desktop exports when you capture from Windows.

Optional: add the same PNGs under `screenshots/` in **this** repo so GitHub browsers
see them too — keep filenames aligned with the portfolio for consistency.

**Capture checklist:** collapse Filters / Bookmarks panes, reset slicers to a clear
story (e.g. latest full year), then **File → Export → Export current page as image**
(or OS screenshot).

## What I learned

- The whole point of a star schema is making DAX measures cheap to write. When the relationships are right, `SUM(Sales[Sales Amount])` filtered by any dimension column just works.
- Time intelligence functions like `SAMEPERIODLASTYEAR` need a proper Date table with a continuous date range marked as the date column. If you skip that, half your time-based measures silently return blank.
- Drill-through pages (right-click a data point, drill through) are a way better UX for "show me details on this thing" than cramming everything onto one page.
- Slicer layout matters more than you'd think. Putting them in the same spot on every page keeps the filter context predictable.

---

Built as the final project for WCTC's Data Visualization course.
