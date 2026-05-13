# 🍷 JustDrinks LTD — Analytics Dashboard

> A complete end-to-end business intelligence project for **JustDrinks LTD**, a multi-store UK beverage retailer. Built from scratch covering data modelling, DAX measure development, Power BI dashboard design, and a responsive web analytics application. Covers FY 2016 sales, purchasing, and inventory analysis across 80 stores.

**Built by Bih Brandy Sango · May 2026**

---

## 📋 Table of Contents

- [Project Overview](#-project-overview)
- [Live Links](#-live-links)
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

- A **Power BI data model** built from raw source files — star-schema design with three dimensions (Date, DimStore, DimProduct) and 54 DAX measures organised into display folders
- A **custom JustDrinks brand theme** for Power BI matching the company identity
- A **responsive single-file web dashboard** with real embedded data, Power BI-style cross-filtering, dark mode, and LocalStorage persistence
- A **13-slide pitch deck** presenting the project to stakeholders with live clickable links
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

## 🌐 Live Links

| Resource | Link |
|---|---|
| 🌐 **Web Dashboard** | [Open in browser](https://bihbrandysango-cloud.github.io/justdrinks-ltd-analysis/JustDrinks_Dashboard.html) |
| 📊 **Power BI Report** | [Open in Power BI Service](https://app.powerbi.com/links/YR9nMdbf1D?ctid=74283479-689a-49e8-9a3b-d4098db086cb&pbi_source=linkShare) |
| 📁 **GitHub Repository** | [bihbrandysango-cloud/justdrinks-ltd-analysis](https://github.com/bihbrandysango-cloud/justdrinks-ltd-analysis) |

---

## 📁 Project Structure

```
justdrinks-ltd-analysis/
│
├── JustDrinks_Dashboard.html               # Single-file interactive web dashboard
├── JustDrinks_Theme.json                   # Power BI custom brand theme
├── JustDrinks_DataModel_Documentation.docx # Full data model docs + maturity rating
├── JustDrinks_Dashboard_BuildSpec.docx     # Power BI dashboard build specification
├── JustDrinks_PitchDeck_v3.pptx           # 13-slide pitch deck with live links
├── JustDrinks_Gamma_Outline.md             # Presentation outline
├── README.md                               # This file
├── LICENSE                                 # MIT License
└── .gitignore                              # Git ignore rules
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

### Measures — 54 across 7 display folders

| Folder | Count | Key Measures |
|---|---|---|
| 01 Sales | 8 | Total Sales ($), Avg Selling Price, Wine Sales, Spirit Sales, Brands Sold |
| 02 Purchases | 6 | Total Purchase ($), PO Count, Avg Purchase per PO, Critical Supplier Score |
| 03 Inventory | 9 | Inventory Turnover, Net Stock Change, Avg Shelf Time (Days), AvgInv Value |
| 04 Profitability | 5 | Gross Profit, Gross Profit Margin (%), GP Margin Status, Profit |
| 05 Time Intelligence | 5 | Sales YTD, Sales PY, Sales YoY $, Sales YoY %, Sales Growth % |
| 06 Store Analysis | 6 | Active Stores, Sales per Store, Highest/Lowest Avg Price |
| 07 Dashboard Helpers | 3 | Dynamic Title, Turnover Status, GP Margin Status |

---

## 📊 Web Dashboard Features

The web dashboard (`JustDrinks_Dashboard.html`) is a **zero-dependency single HTML file** that runs in any browser — no server, no installation required.

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
- Form: Date · Store · Product (autocomplete) · Category · Qty · Unit Price · Cost · City
- **Add Transaction** — updates all dashboard visuals instantly
- **⚡ Add 10 Random Transactions** — generates realistic data from real product/store list
- **🔄 Reset 60-Day Demo** — loads 60 days of representative daily transactions
- Mini KPI strip: Total Transactions · Today's Count · Total Added Revenue

#### ⚙️ Settings
- Dark / Light mode toggle — persists across sessions
- 5 accent colour swatches (Navy · Amber · Green · Wine · Sage)
- LocalStorage usage display and CSV export
- Full model statistics panel

### Cross-Filtering
Clicking any chart element sets a visual filter that affects all other visuals simultaneously. Active filters shown as removable chips. Clear all with one button.

---

## 📐 Power BI Dashboard

A complete build specification (`JustDrinks_Dashboard_BuildSpec.docx`) covers every visual with exact position, field bindings, and formatting.

### Brand Colour Palette

| Role | Hex | Usage |
|---|---|---|
| Primary — Deep Navy | `#1E2761` | Headers, primary measures, table headers |
| Accent — Warm Amber | `#F2A65A` | Highlights, targets, active filters |
| Wine — Bordeaux | `#7B2D26` | Wine category visuals |
| Spirits — Sage | `#5B8C7E` | Spirits category visuals |
| Good | `#059669` | Above-target KPI indicators |
| Watch | `#D97706` | Borderline indicators |
| Bad | `#DC2626` | Below-target indicators |

### How to import the theme
1. Open Power BI Desktop with your `.pbix` file
2. Go to **View → Themes → Browse for themes**
3. Select `JustDrinks_Theme.json`
4. Click **Got it** — theme applies instantly

---

## 🎤 Pitch Deck

A **13-slide PowerPoint pitch deck** (`JustDrinks_PitchDeck_v3.pptx`) was built to present this project to stakeholders. It includes:

| Slide | Content |
|---|---|
| 1 | Cover — live clickable links to Power BI and web app |
| 2 | Agenda |
| 3 | Executive Summary — the problem and solution in plain business language |
| 4 | Company Overview |
| 5 | The Problem |
| 6 | Our Solution |
| 7 | Data Model & Star Schema diagram |
| 8 | DAX Measures — all 54 measures listed by folder |
| 9 | Key Findings — revenue, profit, inventory insights |
| 10 | Power BI Dashboard — mock-up with 4 real charts |
| 11 | Web Analytics App — browser mock-up |
| 12 | Recommendations — 6 data-driven actions |
| 13 | Closing — thank you + all live links |

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
| Peak Month | **December 2016 — $52.3M revenue, $26.0M GP** |
| Top Store | **Store 76 · Doncaster — $25.5M** |
| Top Product | **Jack Daniels No 7 Black — $7.96M** |
| Avg Shelf Days | **7.7 days from receiving to first sale** |

---

## 🚀 Getting Started

### Run the web dashboard
1. Download `JustDrinks_Dashboard.html`
2. Double-click to open in any browser (Chrome, Edge, Firefox, Safari)
3. No server or installation required

### View the live dashboard online
```
https://bihbrandysango-cloud.github.io/justdrinks-ltd-analysis/JustDrinks_Dashboard.html
```

### Open the Power BI report
```
https://app.powerbi.com/links/YR9nMdbf1D?ctid=74283479-689a-49e8-9a3b-d4098db086cb&pbi_source=linkShare
```

### Apply the Power BI brand theme
1. Open your `.pbix` file in Power BI Desktop
2. Go to **View → Themes → Browse for themes**
3. Select `JustDrinks_Theme.json`

### Follow the Power BI dashboard build spec
1. Open `JustDrinks_Dashboard_BuildSpec.docx`
2. Import the theme first
3. Follow Section 5 step by step — each visual has type, position, field bindings, and formatting

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
| **Presentation** | PowerPoint (pptxgenjs) |
| **Documentation** | Word (.docx) · Markdown |
| **Version control** | Git · GitHub |
| **Hosting** | GitHub Pages |

---

## 📦 Data Sources

| Source file | Records | Description |
|---|---|---|
| `SalesFINAL12312016` | 12.8M | Sales transactions Jan–Dec 2016 |
| `PurchasesFINAL12312016` | 2.4M | Purchase orders |
| `InvoicePurchases12312016` | ~5K | Vendor invoices with freight |
| `BegInvFINAL12312016` | ~200K | Inventory snapshot — Jan 1 2016 |
| `EndInvFINAL12312016` | ~200K | Inventory snapshot — Dec 31 2016 |
| `2017PurchasePricesDec` | 12,261 | Product master with current prices |

---

## 🗺 Improvement Roadmap

### 🔴 High Priority
- [ ] **H1** — Delete dormant Sales↔Purchases Many-to-Many bidirectional relationship
- [ ] **H2** — Delete 4 unused auto-detected fact-to-fact InventoryId relationships
- [ ] **H3** — Mark `Date` as a date table in Power BI Desktop UI
- [ ] **H4** — Fix 4 products with `Size = "Unknown"` in Power Query
- [ ] **H5** — Replace Total COGS proxy with true cost-of-sold-goods

### 🟡 Medium Priority
- [ ] **M1** — Rename tables to dimensional conventions (FactSales, DimProduct, etc.)
- [ ] **M2** — Build a `DimVendor` dimension
- [ ] **M3** — Retire legacy calculated tables
- [ ] **M4** — Migrate visuals from alias measures to canonical names
- [ ] **M5** — Add `DimInventoryItem` dimension keyed on InventoryId

### 🟢 Low Priority
- [ ] **L1** — Add KPI goal thresholds for traffic-light visuals
- [ ] **L2** — Add Row-Level Security (RLS) for multi-store access
- [ ] **L3** — Convert `_ShelfTimeHelper` to Power Query M
- [ ] **L4** — Add fiscal-calendar columns to Date table
- [ ] **L5** — Add column descriptions on all dimension columns

---

## 🏆 Maturity Rating

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

Completing the 5 high-priority roadmap items would bring the score to approximately **84% (A−)**.

---

## 📄 License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgements

- **Project built by:** Bih Brandy Sango
- Charts powered by [Chart.js](https://www.chartjs.org/)
- Typography: [Sora](https://fonts.google.com/specimen/Sora) · [JetBrains Mono](https://fonts.google.com/specimen/JetBrains+Mono)
- AI assistance: Claude (Anthropic)

---

<div align="center">
  <strong>JustDrinks LTD Analytics · FY 2016 · Built May 2026</strong><br/>
  <sub>Power BI · DAX · Power Query M · HTML · CSS · JavaScript · Chart.js</sub><br/><br/>
  <a href="https://bihbrandysango-cloud.github.io/justdrinks-ltd-analysis/JustDrinks_Dashboard.html">🌐 Live Dashboard</a> ·
  <a href="https://app.powerbi.com/links/YR9nMdbf1D?ctid=74283479-689a-49e8-9a3b-d4098db086cb&pbi_source=linkShare">📊 Power BI Report</a> ·
  <a href="JustDrinks_DataModel_Documentation.docx">📄 Documentation</a> ·
  <a href="JustDrinks_PitchDeck_v3.pptx">🎤 Pitch Deck</a>
</div>
