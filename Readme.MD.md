
# 🎯 AtliQ Hardware - Business Intelligence 360°

[![Power BI](https://img.shields.io/badge/Power%20BI-Latest-yellow?logo=powerbi)](https://powerbi.microsoft.com/)
[![MySQL](https://img.shields.io/badge/MySQL-8.0-blue?logo=mysql)](https://www.mysql.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](https://opensource.org/licenses/MIT)

Enterprise-grade Business Intelligence platform providing 360° analytics across Finance, Sales, Marketing, Supply Chain, and Executive functions for a global hardware manufacturer.

**[🔗 Live Dashboard](https://app.powerbi.com/view?r=eyJrIjoiMTBjNjNmNDItYzUyMi00NzEwLTkwMDEtYTVkYjU4MmVmMGJkIiwidCI6IjcwZjQ5MWZkLTA3ODAtNDNhOS1iOTRmLThjZmEzOTlkZWRkOCJ9)** | **[📖 Documentation](/AtliQ%20Hardware%20Business%20Intelligence%20360°%20Project.md)**

---

## Overview

AtliQ Hardware, a global manufacturer operating across 27 markets with both brick-and-mortar and e-commerce channels, needed to transition from fragmented operational data to a unified, data-driven decision-making platform.

This project delivers a comprehensive Business Intelligence solution that:
- Consolidates 50M+ transaction records from multiple MySQL databases
- Provides real-time P&L visibility from Gross Sales to Net Profit
- Enables forecast accuracy tracking to minimize inventory risks
- Offers competitive market share analysis for strategic positioning

---

## 🔍 Business Problem

**Challenge:**
- Disconnected data across multiple channels and 27 markets
- No unified view of profitability or operational performance
- Inability to track forecast accuracy leading to inventory issues
- Limited visibility into competitive market positioning

**Impact:**
- Delayed decision-making
- Excess inventory costs
- Missed revenue opportunities
- Reactive rather than proactive strategy

---

## 🏗️ Solution Architecture

```

┌─────────────────┐ │ MySQL Database │ │ (gdb041) │──┐ └─────────────────┘ │ │ ┌──────────────────┐ ┌─────────────────┐ ├───→│ Power Query │ │ MySQL Database │ │ │ ETL Pipeline │ │ (gdb056) │──┘ └──────────────────┘ └─────────────────┘ │ ↓ ┌──────────────────┐ │ Data Model │ │ (Star Schema) │ └──────────────────┘ │ ↓ ┌──────────────────┐ │ DAX Measures │ │ (40+ KPIs) │ └──────────────────┘ │ ↓ ┌──────────────────┐ │ 5 Dashboards │ │ Interactive UI │ └──────────────────┘

````

---

## ✨ Key Features

### 📊 Dashboard Views

1. **Finance View**
   - Complete P&L statement (Gross Sales → Net Profit)
   - YoY performance comparison
   - Target vs Actuals analysis
   - Top/Bottom products and customers by Net Sales

2. **Sales View**
   - Customer and product performance analysis
   - Gross Margin trends and distribution
   - Performance matrix scatter plots
   - Regional sales breakdown

3. **Marketing View**
   - Product segment performance
   - Net Profit vs Gross Margin analysis
   - Regional operational expenses
   - Marketing ROI metrics

4. **Supply Chain View**
   - Forecast accuracy tracking
   - Net Error and Absolute Error analysis
   - Inventory risk identification
   - Key supply chain metrics by customer and product

5. **Executive View**
   - High-level KPIs dashboard
   - Market share analysis
   - Revenue trends across divisions
   - Top customers and products overview

### Advanced Features

- **Dynamic Fiscal Calendar** - Custom Sept-Aug fiscal year alignment
- **Bookmark-Based View Switching** - Toggle between GM% and NP% views
- **Drill-Through Analysis** - Detailed trend exploration via tooltips
- **Dynamic Target Variance** - Parameter-based tolerance filtering
- **Automated Refresh Scheduling** - Daily data updates
- **Top N Filtering** - Configurable top/bottom performers

---

## 🛠️ Tech Stack

| Category                 | Technologies                    |
| ------------------------ | ------------------------------- |
| **Database**             | MySQL                           |
| **ETL**                  | Power Query (M Language)        |
| **Data Modeling**        | Star Schema, Bridge Tables      |
| **Analytics**            | DAX (Data Analysis Expressions) |
| **Visualization**        | Power BI Desktop                |
| **Deployment**           | Power BI Service                |
| **Performance Analysis** | DAX Studio                      |
| **Version Control**      | Git, GitHub                     |

---

## 📸 Dashboard Views

### Executive View
![Executive Dashboard](/assets/exec-view.png)

### Finance View
![Finance Dashboard](/assets/finance-view.png)

### Sales View
![Sales Dashboard](/assets/sales-view.png)

### Marketing View
![Marketing Dashboard](/assets/marketing-view.png)

### Supply Chain View
![Supply Chain Dashboard](/assets/supply-chain-view.png)

---

## Data Model

### Star Schema Architecture

![Data Model](/assets/data-model.png)

#### Key Relationships
- One-to-Many relationships throughout
- Bridge table pattern for fiscal year alignment
- No many-to-many relationships (resolved via bridge tables)

---

## 💡 Key Insights

From the analysis, we identified several critical business insights:

1. **Sales Data Validation Issues**
    - Identified $376K discrepancy in sales figures
    - Root cause: New product launch (AQ Marquee P4) post-forecast cycle
    - **Action:** Implemented process for capturing mid-cycle product additions

2. **Performance Optimization**
    - Reduced file size by 27% (257MB → 189MB)
    - **Method:** Query folding, calculated columns → measures conversion
    - **Impact:** Faster refresh times and improved user experience

3. **P&L Visibility**
    - CEO/PO required Net Profit tracking, not just Gross Margin
    - **Solution:** Extended P&L to include operational expenses
    - **Result:** Complete profitability picture from Gross Sales → Net Profit

4. **Supply Chain Efficiency**
    - Forecast accuracy tracking enabled proactive inventory management
    - Risk identification before stockouts or excess inventory occur

---

## ⚡ Performance Optimization

### Query Folding Implementation

- Reduced columns in fact tables to essential fields only
- Leveraged native SQL queries for data retrieval
- **Result:** 40% faster query execution

### DAX Optimization

```dax
// Before: Calculated Column (increases file size)
Total_COGS = [manufacturing_cost] + [freight_cost] + [other_cost]

// After: Dynamic Measure (computed on-demand)
Total COGS = 
    SUMX(
        fact_actuals_estimates,
        [manufacturing_cost] + [freight_cost] + [other_cost]
    )
```

### Memory Management

- Analyzed memory consumption using DAX Studio
- Removed pre-computed columns where dynamic measures sufficed
- Strategic use of variables in complex measures

---

## 📚 Lessons Learned

### Technical

1. **Query Folding is Critical** - Always verify which transformations fold back to source
2. **Bridge Tables Solve M:M** - Never leave many-to-many relationships in production
3. **Measures > Calculated Columns** - For most aggregations, measures are superior
4. **DAX Studio is Essential** - Performance analysis should be standard practice

### Process

1. **Mockups Save Time** - Design before development prevents rework
2. **Stakeholder Validation** - Regular check-ins catch misalignment early
3. **Data Validation is Non-Negotiable** - Cross-check every number before presenting
4. **Documentation Matters** - Future you (and your team) will thank you

### Business

1. **Understand Fiscal Calendars** - Business operations rarely align with calendar years
2. **Context is Everything** - Numbers without context are just numbers
3. **Forecasting is Hard** - Build processes to capture operational changes mid-cycle
4. **Net Profit ≠ Gross Margin** - Know which metric matters for which stakeholder


---

## 📧 Contact

**Your Name**

- LinkedIn: [Limesh Mahial](https://linkedin.com/in/lmahial)
- Email: [lm.datadev.10@gmail.com](mailto:lm.datadev.10@gmail.com)

---

## 🙏 Acknowledgments

- AtliQ Technologies (Company data for educational purposes)
- [Codebasics](https://codebasics.io/) for project inspiration
- Power BI community for DAX optimization techniques

---

<div align="center">

**⭐ If you found this project helpful, please give it a star! ⭐**

Made with ❤️ by Limesh Mahial

</div>

---
