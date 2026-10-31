# JustDrinks LTD
## Power BI Data Model Documentation

Capstone Analytics Project · May 2026

Prepared by JustDrinks Analytics Team

---

# Executive Summary

A complete overhaul of the JustDrinks LTD data model — supporting inventory, sales, and procurement analysis across the full store network for fiscal 2016.

**Key statistics**
- ~172 MB model size
- 12.8M sales rows · 2.4M purchase rows
- 80 stores across 68 cities
- 12,261 products
- 44 measures, 15 tables
- Date range: Dec 2015 – Dec 2016

---

# Business Questions Addressed

The model is designed to answer recurring questions across teams:

- How much revenue, profit, and margin per store / city / category?
- Which products and stores have the highest sales velocity?
- How quickly does inventory move (turnover, shelf time)?
- Which suppliers drive the most purchasing volume?
- How does monthly activity trend through the year?
- Which inventory items show negative stock movement?

---

# Stakeholders

| Audience | Use case |
|---|---|
| Executive leadership | Revenue, gross profit, turnover dashboards |
| Store operations | Per-store performance comparison |
| Purchasing team | Vendor performance, PO efficiency |
| Inventory team | Shelf time, stock movement, snapshots |

---

# Data Sources

Five source files from the JustDrinks operations system:

| Source file | Business meaning |
|---|---|
| BegInvFINAL12312016 | Inventory snapshot at start of period |
| EndInvFINAL12312016 | Inventory snapshot at end of period |
| SalesFINAL12312016 | Sales transactions across all stores |
| PurchasesFINAL12312016 | Purchase transactions (received goods) |
| InvoicePurchases12312016 | Purchase invoices and freight |
| 2017PurchasePricesDec | Product master with current prices |

---

# Model Architecture

A star-schema design with three dimensions and five fact tables.

**Dimensions**
- **Date** — Power Query (M), 2015–2017
- **DimStore** — calculated, one row per store
- **2017PurchasePricesDec** — product master (Brand)

**Fact tables**
- SalesFINAL12312016 (12.8M rows)
- PurchasesFINAL12312016 (2.4M rows)
- BegInvFINAL12312016 / EndInvFINAL12312016 (~200K each)
- InvoicePurchases12312016 (~5K)

---

# Date Dimension

Built in Power Query M for transparency and refresh-time updates.

**Columns**
- Date, DateKey, Year, Quarter
- MonthNumber, MonthName, MonthYear
- DayOfWeek, IsWeekend, WeekNumber
- Sort helpers: MonthYearSort, DayOfWeekNumber

Sort-by-column is set on MonthName, MonthYear, and DayOfWeek so charts display in chronological order — not alphabetical.

---

# Store Dimension (DimStore)

A single source of truth for stores — replaces fact-table-level Store columns that previously couldn't filter across tables.

**80 stores · 68 cities · 1 row per store**

```
SUMMARIZE (
    EndInvFINAL12312016,
    EndInvFINAL12312016[Store],
    EndInvFINAL12312016[City]
)
```

Connected to all four fact tables (Sales, Purchases, BegInv, EndInv).

---

# Product Dimension

The 2017PurchasePricesDec table holds the product master.

**Key columns**
- Brand (PK) — unique product identifier
- Description, Size, Volume
- Classification (1=Wine, 2=Spirits)
- Price, PurchasePrice
- VendorNumber, VendorName

**Known issue:** 4 products have Size = "Unknown" and load with non-fatal errors.

---

# Relationships at a Glance

**Active**
- All fact tables → Date (on most analytical date column)
- All fact tables → DimStore (on Store)
- All fact tables → DimProduct (on Brand)
- _ShelfTimeHelper → DimProduct (on Brand)

**Inactive (USERELATIONSHIP)**
Multiple alternate dates per fact: ReceivingDate, InvoiceDate, PayDate

All relationships are Many-to-One, single-direction. **No bidirectional relationships** — the previous Sales↔Purchases bidirectional was removed.

---

# Measures: Sales

Folder: **01 Sales**

- Total Sales ($)
- Total Quantity Sold
- Avg Selling Price
- Wine Sales · Spirit Sales
- % of Total Sales · % Sales by Category
- Total Quantity by Size

---

# Measures: Purchases

Folder: **02 Purchases**

- Total Purchase ($)
- Total Purchased Qty
- PO Count
- Avg Purchase per PO
- Monthly Purchase ($)
- Critical Supplier Score

---

# Measures: Inventory

Folder: **03 Inventory**

- BegInv Value · EndInv Value
- AvgInv · AvgInv Value
- Inventory Turnover (qty-based)
- Inventory Turnover Value ($-based)
- Net Stock Change
- Shelf Time (Day) · Avg Shelf Time (Days)

---

# Measures: Profitability & Time

**04 Profitability**
- Total COGS
- Gross Profit
- Gross Profit Margin (%)
- Profit (per-row)

**05 Time Intelligence**
- Sales Growth %

**06 Store Analysis**
- Highest / Lowest Avg Price
- Store with Highest / Lowest Price

---

# Performance Optimization: _ShelfTimeHelper

The Avg Shelf Time (Days) measure was timing out in visuals — correlating sales and purchases on InventoryId across 12.8M rows is expensive.

**Solution:** precompute per-InventoryId shelf days at refresh time into a hidden helper table. Measure becomes a simple AVERAGEX over a small materialized table.

**Result:** ~4,300ms → ~9ms — a **50× speedup**.

---

# What Was Improved

**Removed**
- 9 auto-generated LocalDateTable_* tables
- Bidirectional Sales↔Purchases relationship
- 13 duplicate measures (kept as hidden aliases for back-compat)
- A broken empty measure

**Added**
- Power Query M-based Date table (2015–2017)
- DimStore dimension wired to all facts
- _ShelfTimeHelper for fast shelf-time calc
- Format strings, descriptions, display folders on every measure
- Sort-by-column properties

---

# What Was Fixed

- **Avg Shelf Time** was returning ~365 for almost every brand because MIN/MAX dates weren't correlated to InventoryId
- **Profit by Store** referenced a deleted `[Total Revenue]` measure — rewritten to use `[Total Sales ($)]`
- **Date dimension** originally covered only 2023, missing the entire 2016 fact range

---

# Known Issues

- 4 products with Size = "Unknown" in the master file
- Some inventory items have a recorded first sale before their first receive date (source data anomaly)
- Several legacy calculated tables remain (Sales by Category ($), Sales by Size, etc.) — retained because existing visuals were bound to them

---

# Recommended Future Work

1. Rename source-loaded tables to dimensional names (FactSales, FactPurchases, DimProduct) once visuals can be coordinated
2. Build a DimVendor dimension to consolidate vendor analysis
3. Replace Total COGS proxy with true cost-of-sold-goods correlation
4. Mark Date table as a date table in Power BI Desktop UI
5. Retire legacy calculated tables after visual migration
6. Clean Size = "Unknown" rows in Power Query
7. Add fiscal-calendar columns if needed

---

# Glossary

| Term | Definition |
|---|---|
| Brand | Unique product identifier (Integer) |
| Classification | 1 = Wine, 2 = Spirits |
| Category | Friendly label: Wine / Spirits / Other |
| InventoryId | Composite Store + product key |
| Shelf time | Days from receive to first sale |
| Inventory turnover | Sold units / average on-hand |
| PO | Purchase Order |
| COGS | Cost of Goods Sold |

---

# Thank You

JustDrinks LTD · Power BI Data Model Documentation

For questions, contact the JustDrinks Analytics Team.
