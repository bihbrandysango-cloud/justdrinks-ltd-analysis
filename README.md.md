# 🍷 JustDrinks LTD — Analytics Dashboard

> A complete end-to-end business intelligence project for **JustDrinks LTD**, a multi-store UK beverage retailer. Built from scratch covering data modelling, DAX measure development, Power BI dashboard design, and a responsive web analytics application. Covers FY 2016 sales, purchasing, and inventory analysis across 80 stores.

---

## 📋 Table of Contents

- [Project Overview](#-project-overview)
- [Live Demo](#-live-demo)
- [Screenshots](#-screenshots)
- [Project Structure](#-project-structure)
- [Data Model](#-data-model)
- [Web Dashboard Features](#-web-dashboard-features)
- [Power BI Dashboard](#-power-bi-dashboard)
- [Key Metrics](#-key-metrics)
- [Getting Started](#-getting-started)
- [Tech Stack](#-tech-stack)
- [Data Sources](#-data-sources)
- [Improvement Roadmap](#-improvement-roadmap)
- [Maturity Rating](#-maturity-rating)
- [License](#-license)

---

## 📌 Project Overview

JustDrinks LTD is a multi-store beverage retailer operating 80 stores across 68 cities in the UK. This project was built from scratch as a full business intelligence solution delivering:

- A **Power BI data model** built from raw source files — star-schema design with three dimensions (Date, DimStore, DimProduct) and 44 DAX measures organised into display folders
- A **custom JustDrinks brand theme** for Power BI (`.json`) matching the company identity
- A **responsive single-file web dashboard** (`HTML/CSS/JS`) with real embedded data, Power BI-style cross-filtering, dark mode, and LocalStorage persistence
- **Full documentation** covering the data model architecture, dashboard build specification, improvement roadmap, and best-practice maturity rating

### Business Questions Answered

| Area | Questions |
|---|---|
| **Sales** | How much revenue and profit per store, city, and category? Which products drive the most volume? |
| **Purchasing** | Which suppliers are most critical? What is the average cost per PO? How do purchase trends compare to sales? |
| **Inventory** | How fast does inventory turn over? Which items sit on shelves the longest? |
| **Profitability** | What is the gross profit margin by category, store, and product? |
| **Time** | How does monthly performance trend across the year? Which months drive peak revenue? |

---

## 🌐 Live Demo

> 🔗 **[View the dashboard live →](https://BihBrandySango.github.io/justdrinks-ltd-analysis/JustDrinks_Dashboard.html)**

---

## 📸 Screenshots

### Executive Dashboard — Light Mode
![Dashboard Light Mode](screenshots/dashboard-light.png)

### Executive Dashboard — Dark Mode
![Dashboard Dark Mode](screenshots/dashboard-dark.png)

### Add Transaction Page
![Add Transaction](screenshots/add-transaction.png)

> *To add screenshots: take a browser screenshot of the dashboard and save to the `screenshots/` folder in this repo.*

---

## 📁 Project Structure

```
justdrinks-ltd-analysis/
│
├── JustDrinks_Dashboard.html               # Single-file interactive web dashboard
├── JustDrinks_Theme.json                   # Power BI custom brand theme
├── JustDrinks_DataModel_Documentation.docx # Full data model docs + maturity rating
├── JustDrinks_Dashboard_BuildSpec.docx     # Power BI dashboard build specification
├── JustDrinks_Gamma_Outline.md             # Presentation outline (Gamma AI)
├── README.md                               # This file
├── LICENSE                                 # MIT License
├── .gitignore                              # Git ignore rules
└── screenshots/                            # Dashboard screenshots
    ├── dashboard-light.png
    └── dashboard-dark.png
```

---

## 🗂 Data Model

The Power BI model follows a **star-schema design** built from six raw source files extracted year-end 2016.

### Dimensions

| Table | Type | Grain | Rows |
|---|---|---|---|
| `Date` | Power Query (M) | 1 row per day · 2015–2017 | 1,096 |
| `DimStore` | DAX calculated | 1 row per store with City | 80 |
| `2017PurchasePricesDec` | Source-loaded | 1 row per product (Brand) | 12,261 |

### Fact Tables

| Table | Rows | Description |
|---|---|---|
| `SalesFINAL12312016` | 12.8M | Sales transactions |
| `PurchasesFINAL12312016` | 2.4M | Purchase order line items |
| `InvoicePurchases12312016` | ~5K | Purchase invoices with freight |
| `BegInvFINAL12312016` | ~200K | Beginning-of-period inventory |
| `EndInvFINAL12312016` | ~200K | End-of-period inventory |

### Relationships

- **13 active** relationships — all Many-to-One, single-direction
- **6 inactive** role-playing date relationships activated via `USERELATIONSHIP()`
- **No bidirectional** relationships — ensures clean, unambiguous filter propagation

### Measures — 44 across 7 display folders

| Folder | Measures |
|---|---|
| 01 Sales | Total Sales ($), Total Quantity Sold, Avg Selling Price, Wine Sales, Spirit Sales, % of Total Sales, % Sales by Category, Total Quantity by Size |
| 02 Purchases | Total Purchase ($), Total Purchased Qty, PO Count, Avg Purchase per PO, Monthly Purchase ($), Critical Supplier Score |
| 03 Inventory | BegInv Value, EndInv Value, AvgInv, AvgInv Value, Inventory Turnover, Inventory Turnover Value, Net Stock Change, Shelf Time (Day), Avg Shelf Time (Days) |
| 04 Profitability | Total COGS, Gross Profit, Gross Profit Margin (%), Profit |
| 05 Time Intelligence | Sales YTD, Sales PY, Sales YoY $, Sales YoY %, Sales Growth % |
| 06 Store Analysis | Active Stores, Sales per Store, Highest Avg Price, Lowest Avg Price, Store with Highest Price, Store with Lowest Price |
| 07 Dashboard Helpers | Dynamic Title, GP Margin Status, Turnover Status, Brands Sold |

### Date Dimension (Power Query M)

The Date table is built entirely in Power Query M language — transparent, refreshable, and extensible without DAX recalculation overhead:

```m
let
    StartDate = #date(2015, 1, 1),
    EndDate = #date(2017, 12, 31),
    NumberOfDays = Duration.Days(EndDate - StartDate) + 1,
    Source = List.Dates(StartDate, NumberOfDays, #duration(1, 0, 0, 0)),
    ...
    AddMonthYear = Table.AddColumn(..., "MonthYear", each Date.ToText([Date], "MMM yyyy")),
    AddMonthYearSort = Table.AddColumn(..., "MonthYearSort", each Date.Year([Date]) * 100 + Date.Month([Date]))
in
    AddWeekNumber
```

Columns: Date · DateKey · Year · Quarter · MonthNumber · MonthName · MonthYear · MonthYearSort · DayOfWeek · DayOfWeekNumber · IsWeekend · WeekNumber

---

## 📊 Web Dashboard Features

The web dashboard (`JustDrinks_Dashboard.html`) is a **zero-dependency single HTML file** that runs directly in any browser — no server, no install required.

### 3 Pages

#### 📊 Dashboard
- **6 KPI cards** — Total Revenue, Gross Profit, Active Stores, Purchases, Units Sold, Avg Shelf Days
- **Monthly Revenue & GP bar chart** — click any bar to cross-filter all visuals
- **Wine vs Spirits donut chart** — click a slice to cross-filter everything
- **Top 10 Stores horizontal bar chart** — click a store to cross-filter
- **GP Margin by Category** bar chart
- **Sales vs Purchases combo chart** (bar + line overlay)
- **Store Performance scatter chart** (Revenue vs GP%, bubble size = sales volume)
- **Top 20 Products table** with conditional GP% badges and progress bars
- **Recent Transactions table** — highlights newly added entries in green

#### ➕ Add Transaction
- Form fields: Date · Store (dropdown) · Product (autocomplete) · Category · Quantity · Unit Price · Cost · City
- **Add Transaction** — updates all dashboard visuals instantly
- **⚡ Add 10 Random Transactions** — generates realistic data from real product/store list
- **🔄 Reset 60-Day Demo** — loads 60 days of representative daily transactions
- Mini KPI strip: Total Transactions · Today's Count · Total Added Revenue
- Scrollable recent transactions table (last 20 entries)

#### ⚙️ Settings
- 🌙 Dark / ☀️ Light mode toggle — persists across sessions
- 5 accent colour swatches (Navy · Amber · Green · Wine · Sage)
- LocalStorage usage display
- CSV export of all user-added transactions
- Full model statistics panel
- Data refresh timestamp

### Cross-Filtering (Power BI–style)

```
Click a bar on any chart  → filters all visuals by that month or store
Click a donut slice        → filters all visuals by that category
Active filters shown as chips with ✕ to remove individually
"Clear" button resets all filters instantly
```

### Data Persistence

All user-added transactions are stored in **browser LocalStorage** and survive page refresh. Export as CSV at any time from the Settings page.

---

## 📐 Power BI Dashboard

A complete build specification (`JustDrinks_Dashboard_BuildSpec.docx`) was created covering:

- **Brand theme** — import `JustDrinks_Theme.json` via View → Themes → Browse for themes
- 12-column layout grid with exact visual positions (X, Y, W, H in inches)
- Step-by-step instructions for all 9 visuals on the Executive Overview page
- Conditional formatting recipes for KPI traffic-light colours
- Cross-filter interaction map
- QA checklist: functional · visual · performance · accessibility

### Brand Colour Palette

| Role | Hex | Usage |
|---|---|---|
| Primary — Deep Navy | `#1E2761` | Headers, primary measures, table headers |
| Secondary — Royal Blue | `#3D5A9E` | Secondary series, hover states |
| Accent — Warm Amber | `#F2A65A` | Highlights, targets, active filters |
| Wine — Bordeaux | `#7B2D26` | Wine category visuals |
| Spirits — Sage | `#5B8C7E` | Spirits category visuals |
| Good | `#059669` | Above-target KPI indicators |
| Watch | `#D97706` | Borderline indicators |
| Bad | `#DC2626` | Below-target indicators |

---

## 📈 Key Metrics

| Metric | Value |
|---|---|
| Total Revenue (FY 2016) | **$452.1M** |
| Gross Profit | **$130.2M** |
| Gross Profit Margin | **28.8%** |
| Total Purchases | **$321.9M** |
| Units Sold | **32.9M** |
| Active Stores | **80** |
| Cities | **68** |
| Products | **12,261** |
| Purchase Orders | **5,543** |
| Avg Sales per Store | **$5.65M** |
| Peak Month | **July 2016 — $49.7M** |
| Top Store | **Store 76 · Doncaster — $25.5M** |
| Top Product | **Jack Daniels No 7 Black — $7.96M** |

---

## 🚀 Getting Started

### Run the web dashboard (no install needed)

1. Download `JustDrinks_Dashboard.html`
2. Double-click to open in any browser (Chrome, Edge, Firefox, Safari)
3. No server or installation required

### View on GitHub Pages

```
https://BihBrandySango.github.io/justdrinks-ltd-analysis/JustDrinks_Dashboard.html
```

### Apply the Power BI brand theme

1. Open your `.pbix` file in Power BI Desktop
2. Go to **View → Themes → Browse for themes**
3. Select `JustDrinks_Theme.json`
4. Click **Got it** — theme applies across the entire report instantly

### Follow the Power BI dashboard build spec

1. Open `JustDrinks_Dashboard_BuildSpec.docx`
2. Import the theme first (above)
3. Follow Section 5 step by step — each visual has type, position, field bindings, and formatting rules

---

## 🛠 Tech Stack

| Layer | Technology |
|---|---|
| **Data model** | Power BI Desktop · Tabular model (compatibility level 1567) |
| **Data transformation** | Power Query M |
| **Analytical calculations** | DAX (Data Analysis Expressions) |
| **Web dashboard** | HTML5 · CSS3 · Vanilla JavaScript (ES2020) |
| **Charts** | Chart.js 4.4.1 |
| **Typography** | Sora · JetBrains Mono (Google Fonts) |
| **Persistence** | Browser LocalStorage API |
| **Documentation** | Word (.docx) · Markdown |
| **Presentation** | Gamma AI |
| **Version control** | Git · GitHub |
| **Hosting** | GitHub Pages |

---

## 📦 Data Sources

All data comes from JustDrinks LTD's operations system, as year-end 2016 extracts:

| Source file | Records | Description |
|---|---|---|
| `SalesFINAL12312016` | 12.8M | Sales transactions Jan–Dec 2016 |
| `PurchasesFINAL12312016` | 2.4M | Purchase orders Dec 2015–Dec 2016 |
| `InvoicePurchases12312016` | ~5K | Vendor invoices with freight |
| `BegInvFINAL12312016` | ~200K | Inventory snapshot — Jan 1 2016 |
| `EndInvFINAL12312016` | ~200K | Inventory snapshot — Dec 31 2016 |
| `2017PurchasePricesDec` | 12,261 | Product master with current prices |

> **Note:** The web dashboard uses aggregated summary data embedded in the HTML file. Full row-level data resides in the Power BI model (`.pbix`).

---

## 🗺 Improvement Roadmap

### 🔴 High Priority

- [ ] **H1** — Delete dormant Sales↔Purchases Many-to-Many bidirectional relationship *(10 min)*
- [ ] **H2** — Delete 4 unused auto-detected fact-to-fact InventoryId relationships *(15 min)*
- [ ] **H3** — Mark `Date` as a date table in Power BI Desktop UI *(5 min)*
- [ ] **H4** — Fix 4 products with `Size = "Unknown"` in Power Query *(15 min)*
- [ ] **H5** — Replace Total COGS proxy with true cost-of-sold-goods calculation *(Medium)*

### 🟡 Medium Priority

- [ ] **M1** — Rename tables to dimensional conventions (FactSales, DimProduct, etc.)
- [ ] **M2** — Build a `DimVendor` dimension to consolidate vendor analysis
- [ ] **M3** — Retire legacy calculated tables (Sales by Category, Sales by Size, etc.)
- [ ] **M4** — Migrate visuals from alias measures to canonical measure names
- [ ] **M5** — Add `DimInventoryItem` dimension keyed on InventoryId

### 🟢 Low Priority

- [ ] **L1** — Add KPI goal thresholds for traffic-light visuals
- [ ] **L2** — Add Row-Level Security (RLS) for multi-store access control
- [ ] **L3** — Convert `_ShelfTimeHelper` from DAX calculated table to Power Query M
- [ ] **L4** — Add fiscal-calendar columns to the Date table
- [ ] **L5** — Add descriptive tooltips on all dimension columns

---

## 🏆 Maturity Rating

The Power BI data model was evaluated against 10 Power BI and dimensional modelling best-practice dimensions:

| # | Dimension | Score |
|---|---|---|
| 1 | Star schema integrity | ⭐⭐⭐⭐ 4/5 |
| 2 | Date dimension quality | ⭐⭐⭐⭐ 4/5 |
| 3 | Relationships hygiene | ⭐⭐⭐ 3/5 |
| 4 | Measure organisation | ⭐⭐⭐⭐⭐ 5/5 |
| 5 | DAX quality & efficiency | ⭐⭐⭐⭐ 4/5 |
| 6 | Naming conventions | ⭐⭐ 2/5 |
| 7 | Data quality handling | ⭐⭐⭐ 3/5 |
| 8 | Performance | ⭐⭐⭐⭐ 4/5 |
| 9 | UX for report builders | ⭐⭐⭐⭐ 4/5 |
| 10 | Documentation & governance | ⭐⭐⭐⭐ 4/5 |

### Overall: **37 / 50 · 74% · Grade B (Good)**

> Completing the 5 high-priority roadmap items would bring the score to approximately **84% (A−)**. Full scorecard with justification for each dimension is in `JustDrinks_DataModel_Documentation.docx`.

---

## 📄 License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgements

- **Project built by:** Bih Brandy Sango
- Charts powered by [Chart.js](https://www.chartjs.org/)
- Typography: [Sora](https://fonts.google.com/specimen/Sora) · [JetBrains Mono](https://fonts.google.com/specimen/JetBrains+Mono) via Google Fonts
- AI assistance: Claude (Anthropic)

---

<div align="center">
  <strong>JustDrinks LTD Analytics · FY 2016 · Built May 2026</strong><br/>
  <sub>Power BI · DAX · Power Query M · HTML · CSS · JavaScript · Chart.js</sub><br/><br/>
  <a href="https://BihBrandySango.github.io/justdrinks-ltd-analysis/JustDrinks_Dashboard.html">🌐 Live Dashboard</a> ·
  <a href="JustDrinks_DataModel_Documentation.docx">📄 Documentation</a> ·
  <a href="JustDrinks_Dashboard_BuildSpec.docx">📐 Build Spec</a>
</div>
