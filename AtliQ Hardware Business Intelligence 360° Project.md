
# Executive Summary

This project demonstrates the design and implementation of a enterprise-grade business intelligence solution for a global hardware manufacturer. Using a data-first approach, I built a centralized analytics platform to transform fragmented operational data into actionable business insights, enabling strategic decision-making across multiple sales channels and geographic markets.

**Tech Stack:** SQL | Excel | Power BI  
**Core Competencies:** ETL Pipeline Development | Data Modeling | Interactive Reporting | Business Intelligence Strategy
**Domain: Computer Hardware | Brick-and-Mortar and E-Commerce**

## Project Objective

**Enable AtliQ to transition from a traditional operations-focused manufacturer to a data-driven organization** by:
1. **Digitizing the business model** through centralized data architecture
2. **Classifying the platform ecosystem** with clear performance metrics by channel and market
3. **Building analytics infrastructure** to support real-time decision-making
4. **Driving competitive resilience** through data-informed channel strategy and market prioritization

Check out the full business context here: [[AtliQ Business Model & Strategic Challenge]]

---

# Project Planning and Scoping

Following a **project kick-off meeting with AtliQ's Product Manager**, we established scope, timelines, and success criteria through a formal project charter. This documented all requirements, stakeholder expectations, and deliverables, ensuring alignment across the analytics team and business leadership.

[Project Charter](/assets/project-charter.pdf)

---



# Data Collection, Extraction, and Validation

### Data Inventory & Source Selection

From our comprehensive **`data catalog`**, we identified the required databases and tables needed to support the analytics solution:

|DB Server|Table Name|Description|
|---|---|---|
|GDB041|FactForecastMonthly|Historical and current forecast data at monthly level|
|GDB041|FactSalesMonthly|Sales data up to date at monthly level|
|GDB013|Regional Forecast|Forecast at different levels of granularity by region|
|GDB014|Regional Sales|Sales at different levels of granularity by region|
|GDB056|ManufacturingCost|Cost data in USD at fiscal year level|
|GDB056|PostInvoiceDeductions|Deductions in USD at monthly level|
|GDB017|Regional Cost & Deductions|Local currency cost and deduction data|
|GDB056|PreInvoiceDeductions|Deductions in USD at fiscal year level|
|GDB019|RegionalMarkUpCost|Local currency markup cost data|
|GDB020|RegionalOperatingExpenses|Local currency operating expense data|
|GDB041|DimCustomer, DimProduct, DimMarket|Dimensional reference tables|
|GDB022|Regional Freight|Local currency freight data|
|GDB056|FreightCost|Freight and other costs as % of Net Sales|
|GDB024|DailySales|Sales data at daily granularity|
|GDB056|GrossPrice|Pricing data in USD at fiscal year level|

**Primary databases selected:** `gdb041` (dimension tables, sales and forecast) and `gdb056` (cost and pricing)

---

## Extracting Data: Connecting Power BI to MySQL

Setting up the data pipeline required connecting Power BI to our MySQL databases (`gdb041` and `gdb056`). This step revealed real-world technical complexities worth documenting.

**Environment Challenges & Troubleshooting:**

I'm working on **macOS (ARM architecture)**, where Power BI is not natively supported. To work around this, I use **Windows VM via VMware Fusion** to run Power BI. This setup introduced several technical hurdles:
1. **Network Configuration:** I had to troubleshoot networking between the macOS host and Windows guest to identify the correct IP address for the MySQL connection.
2. **Database User Security:** Following production best practices, I avoided using the root user for remote database access, as this violates security governance standards in enterprise environments.
3. **MySQL Binding:** I updated the MySQL configuration to set `bind-address = 0.0.0.0`, which tells MySQL to listen on all available network interfaces, making it accessible from the Windows VM running Power BI.
4. **Secure Credentials:** Created a dedicated "powerbi" user with appropriate permissions to connect to the MySQL databases via IP address and port 3306.

These steps ensured a secure, stable connection between the database on macOS and Power BI running inside the virtualized Windows environment mirroring production setup best practices.

**Result:** Successfully loaded all tables from both MySQL databases into Power BI, ready for transformation and modelling.

![](/assets/1-extract.png)

![](/assets/2-extract.png)

---

## ### Validating Data Quality

Data validation is a critical step in any analytics project. Before proceeding to modeling or visualization, I used **Power Query tools** to inspect data integrity and structure.

![](/assets/3-validate.png)

---

**Creating a Date Table (`dim_date`)**

One key transformation was building a custom date dimension table to align with AtliQ's fiscal calendar:

1. **Generate date range:** Created a continuous date list from September 1, 2017 to December 31, 2022 using M-code: `={Number.From(#date(2017,9,1))..Number.From(#date(2022,12,31))}`

2. **Month column:** Added a `month` column to extract the first day of each month, enabling month-level aggregations.

3. **Fiscal year calculation:** Created a custom `fiscal_year` column using the formula: `Date.Year(Date.AddMonths([month],4))`

> [!Note]
> **AtliQ's Fiscal Year**
> To align with AtliQ’s business calendar, where the fiscal year begins in September to capture peak sales periods around Diwali, Black Friday, Christmas, and New Year, we incorporated a **`custom fiscal_year`** column in the **`dim_date`** table. This ensures that all time-based analyses; especially those related to sales performance and forecasting will accurately reflect AtliQ’s operational and financial reporting cycles.

![](/assets/4-validate.png)

---

## Benchmark Validation & Data Accuracy

In real-world organizations, **validating large numbers and maintaining attention to detail are mission-critical skills**. Presenting inaccurate insights can mislead stakeholders and result in poor business decisions. Therefore, every metric was thoroughly cross-checked against benchmark figures provided by the Product Owner before proceeding to modelling or visualization.

**Benchmark Targets (provided by Product Owner):**

- Total Sales Qty 2018: 3.45415 M
- Total Sales Qty 2019: 10.78465 M
- Total Sales Qty 2020: 20.7734 M
- Total Sales Qty 2021: 50.16472 M
- Total Forecast Qty 2022: 86.82346 M
- Products Sold (FY 2022): 345
- Active Customers (FY 2022): 209
- Active Markets (FY 2022): 27

**Issue Discovery & Resolution:**

During validation, I noticed the **forecast quantity for FY 2022** calculated in Power BI was **86.44M** - a gap of 376K below the benchmark of 86.82M. This discrepancy triggered an investigation that showcases real-world cross-functional collaboration:

**Data Analyst to Data Engineer (via Teams):**

> "I found a gap of 376K in forecast quantity for 2022 against the benchmark numbers provided by PO. Can you please check what's going on?"

**Data Engineer Response:**

> "I had validated this before our recent Global Planning Meeting (GPM), and it was aligned. Could you double-check the benchmark numbers?"

**Data Lead Insight:**

> "Is there any chance a new product was launched after the GPM?"

**Product Owner Confirmation:**

> "That's a good point. Please ensure that **AQ Marquee P4 (A0921150602)** is included in the database. It was added post-GPM."

**Resolution:** After including the newly launched product in the source data, the forecast quantity aligned perfectly with the benchmark at 86.82M.

✅ **Lesson Learned:** Data discrepancies often stem from operational changes (new product launches, market entries, process updates) that occur after data pipelines are built. Establishing clear communication channels and maintaining detailed change logs prevents future misalignments.

![](/assets/5-data-accuracy.png)

---
### Data Visualization & Mockup Design

Before diving into code and calculations, we created **mockups to clarify the analytics narrative**. This step is often overlooked, but it's invaluable:

**Why Mockups Matter:**
- **Clarify the Story** – Define the core message and prioritize key insights early before any development
- **Align Stakeholders** – Provide a clear vision that invites feedback and reduces miscommunication down the line
- **Save Time & Effort** – Quick iterations on mockups prevent costly rework during development
- **User-Centric Design** – Validate audience needs and guide effective, intuitive visual choices

![](/assets/6-mockup.png)

---
## Data Transformation
### Building the Actuals + Forecast Unified Table

From the **Finance** and **Supply Chain** mockups, we identified the need for a **line chart** that displays **Net Sales performance over time**, comparing the **selected year vs. the previous year**. For the **Supply Chain** view, we also needed to show **Forecast Accuracy**.

To achieve this, we performed **data transformations in Power Query** to extract and structure forecast data strategically. Here's the challenge: for the year **2022** (our project timeline), we only have **actual sales records available for January to April**. Data beyond April will be treated as **forecasted values**.

But here's the twist - we needed to make this logic **dynamic**. If new monthly sales data (e.g., May or June) is later added to the database, it should **automatically shift** the forecast range, excluding newly available actuals from the forecast for 2022. This ensures the dashboard stays accurate and self-updating without manual

![](/assets/7-actuals-forecast.png)

**The Approach:**

1. **Identify the latest available sales month:**
	- Create a reference of the `fact_sales_monthly` table and extract the maximum date:  `last_sales_month = List.Max(#"gdb041 fact_sales_monthly"[date])`

2. **Extract remaining forecast data:**
    - Create a reference of `fact_forecast_monthly` and filter for dates after the last sales month: `remaining_forecast = Table.SelectRows(Source, each ([date] > last_sales_month))`

3. **Merge actuals and forecast:**
    - Append `fact_sales_monthly` with `remaining_forecast` to generate a new combined table called `fact_actuals_estimates`

This merged table now contains both **actual sales** and **forecast data**, enabling us to build a dynamic **time series visual** that reflects real sales to date and projected figures for the remaining months of 2022.

> [!TIP] 
> When appending tables in Power Query, ensure column names and data types are consistent to avoid errors and data loss.

---
### P&L Structure

To build the P&L statement, we needed to perform a series of structured calculations. Here's how we layered them:

**Step 1: Add Fiscal Year to Actuals + Forecast Table**

Our appended table `fact_actuals_estimates` doesn't include `fiscal_year`, so we added it using the same custom column logic we applied to `dim_date`:

```spreadsheet
fiscal_year = Date.Year(Date.AddMonths([month], 4))
```

**Step 2: Calculate Gross Sales Amount**

We merged `fact_actuals_estimates` with `gross_price` on `product_code` and `fiscal_year`. Since `gross_price` is per unit, we created a custom column:

```spreadsheet
gross_sales_amount = [gross_price] * [qty]
```

**Step 3: Calculate Pre-Invoice Deductions**

We merged `fact_actuals_estimates` with `pre_invoice_deductions` using `customer_code` and `fiscal_year`, then calculated:

```spreadsheet
pre_invoice_discount_amount = [pre_invoice_discount_pct] * [gross_sales_amount]
```

**Step 4: Calculate Net Sales**

We computed the final net sales figure:

```spreadsheet
net_sales_amount = [gross_sales_amount] - [pre_invoice_discount_amount]
```

**P&L Line Items (So Far):**

|**Line Items**|
|---|
|Gross Price|
|– Pre-Invoice Deductions|
|**= Net Invoice Sales**|
|– Post-Invoice Deductions|
|= **Net Sales (NS)**|
|– Cost of Goods Sold (COGS)|
|= **Gross Margin (GM)**|
|Gross Margin % of Net Sales (GM/NS)|

**Why We're Stopping Here (For Now):**

You might be thinking it makes sense to complete the rest of the calculations right now, but we're intentionally holding off. The remaining P&L items (COGS, Gross Margin, and percentages) will be handled using a **different approach** in the data modelling phase, where you'll see some **optimization techniques in play**. This allows us to improve performance and maintain a cleaner separation between **data preparation** and **final calculations**.

**Glimpse of our changes**

![](/assets/8-fact-actual-estimates.png)

> [!TIP]
> **A Note on Data Model Hygiene:** 💡
> One thing you might have noticed in the image above is that **all our tables are loaded into the data model**—which isn't really necessary. Some data professionals load everything "just in case" for future enhancements or frequent changes. But that often results in a **chaotic, circuit board-style schema** (just adding a bit of fun here 😄) instead of a clean **star or snowflake schema**.
> 
> We're not going to be that person. Instead, we **disabled the load for any tables we don't actually need** in the model. This is considered a **best practice** because it helps reduce your **Power BI file size**, improves **load performance**, and keeps the model neat and maintainable, especially when working with large datasets.


![](/assets/9-query-organizing.png)

---
## **Data Modelling and Calculated Columns**

### **Power Query or DAX for  Calculated Columns?**

For the P&L visual, we needed to compute additional columns: COGS, Gross Margin, and margin percentages. The question was: **should we do this in Power Query or DAX?**

Both approaches work, but they have different trade-offs. Here's the decision framework:

**Decision Matrix**

|Criteria|Significance|Power Query|DAX Measure|DAX Calculated Column|
|---|---|---|---|---|
|**Power BI File Size**|Keep file size low for better performance|🟢 LOW|🟢 LOW|🔴 HIGH|
|**System Memory Consumption**|Higher memory affects performance, especially for complex multi-table calculations|🟡 MEDIUM|🔴 HIGH|🟢 LOW|
|**Developer Waiting Time**|Low waiting time means faster development cycles|🔴 HIGH|🟢 LOW|🟡 MEDIUM|
|**Skill Level Required**|Ease of learning and implementation|🟡 MEDIUM|🔴 HIGH|🟡 MEDIUM|

**Quick Decision Framework**

```
Start Here: What's your primary concern?
│
├── File Size → Use Power Query
├── Memory Usage → Use DAX Calculated Columns
├── Development Speed → Use DAX Calculated Columns
└── Data Transformation → Use Power Query
```

**Advanced Considerations**

- **Hybrid Approach:** Use Power Query for initial data prep, then DAX for analytical columns
- **Refresh Strategy:** Consider your data refresh schedule when choosing
- **User Requirements:** End-user needs may dictate the optimal choice
- **Data Volume:** Larger datasets may benefit more from Power Query compression

_Remember: The best choice depends on your specific use case, data volume, and performance requirements. Test both approaches with your actual data to make the optimal decision._

---

### Building the Data Model: Star and Snowflake Schema

**Star Schema**

A **star schema** organizes data with a central fact table surrounded by dimension tables, resembling a star shape.

**Example**: E-commerce sales analysis

- **Fact Table**: Sales (contains metrics like revenue, quantity, profit)
- **Dimension Tables**: Customer, Product, Date, Store (each directly connected to Sales)

**Benefits**: Simple queries, fast performance, easy to understand

![](/assets/star-schema.png)

**Snowflake Schema**

A **snowflake schema** normalizes dimension tables into multiple related tables, creating a snowflake-like structure.

**Example**: Same e-commerce data, but:

- Product dimension splits into: Product → Category → Subcategory
- Customer dimension splits into: Customer → City → State → Country

**Trade-off**: More complex queries but reduced data redundancy

![](/assets/snowflake-dimension.png)


#### Relationship Architecture & The Many-to-Many Problem

Before we could build our model, we needed to understand how tables should relate to each other. There are three primary relationship types:

**One-to-One (1:1)**

Each record in Table A matches exactly one record in Table B.
**Example:** Employee → Employee Details (one employee has one detail record)

**One-to-Many (1:M)**

One record in Table A can relate to multiple records in Table B.
**Example:** Customer → Orders (one customer can have multiple orders)

**Why Avoid Many-to-Many (M:M)**

Many-to-many relationships create ambiguity and performance issues. This is where it gets tricky:

**Problem Example:** Products ↔ Orders

- One product appears in many orders
- One order contains many products
- Creates uncertainty in aggregations—does a total include duplicates? Which direction?

**Solution:** Use a bridge table

- Products → OrderDetails ← Orders
- OrderDetails acts as the intermediary with clear 1:M relationships on both sides

**Key Issue:** M:M relationships can produce incorrect totals and unclear filtering behavior in reports. Always break them down using bridge tables.

![](/assets/10-relationship-types.png)


---

#### Implementing the Bridge Table Pattern

For our AtliQ model, we had a complexity: `dim_date`, `freight_cost`, and `manufacturing_cost` needed to be connected, but `manufacturing_cost` used a different column name (`cost_year`) compared to `freight_cost` (which had no fiscal year at all).

**The Solution:** Create a bridge table using DAX that would serve as the central hub.

1. **Create a fiscal year bridge table in DAX:**

```spreadsheet
   fiscal_year = ALLNOBLANKROW(dim_date[fiscal_year])
```

This creates a clean list of all unique fiscal years from the date dimension.

2. **Connect relationships:**
    - `fiscal_year` → `freight_cost` (on `fiscal_year`)
    - `fiscal_year` → `manufacturing_cost` (on `cost_year`)

This pattern solved the naming inconsistency issue and created a clean hub-and-spoke architecture, enabling both cost tables to relate through a single temporal dimension without creating many-to-many relationships.

The result: a robust, maintainable data model ready for P&L calculations and analysis.`

![](/assets/11-pbi-relationship.png)

---

### **Data Model Overview & Optimization**

#### Understanding the Dimension Tables

The **dimension table serves as our master table**, containing all the records that provide essential context and details for analysis. Dimension tables are critical in a data model because they provide context and details about the data in the **fact table**.

**Key principle:** You need to use fields from dimension tables when building visuals because they provide context and meaningful categories for the data in the fact table.

![](/assets/12-pbi-data-model.png)

#### Query Folding: Optimizing at the Source

****Query folding** is a technique that pushes data transformations back to the source database rather than processing them in Power BI. This is a game-changer for performance with large datasets.

**Example:** Filtering sales data for 2023

- **With folding:** Database filters before sending data to Power BI
- **Without folding:** All data sent to Power BI, then filtered locally

By applying query folding, we drastically reduce the volume of data Power BI needs to process.

**Implementation:**

1. In Power Query, we navigated to the `fact_sales_monthly` table
2. Using the **Choose Columns** option, we deselected unnecessary columns such as `division`, `category`, `market`, and similar fields
3. We retained only the essential columns needed for analysis and establishing relationships: `date`, `product_code`, `customer_code`, and `qty`

![](/assets/13-query-folding.png)

**Impact on Performance:**

By limiting the columns at the query stage, Power BI generates an optimized SQL statement, as seen in the **Native Query** from the applied steps:

```sql
select `date` as `date`,
       `product_code` as `product_code`,
       `customer_code` as `customer_code`,
       `sold_quantity` as `qty`
from `gdb041`.`fact_sales_monthly` `$Table`
```

This means Power BI requests only the selected columns from the source database, minimizing the volume of data imported into the model. It's important to note that during the preview phase in Power Query, Power BI may still display a full dataset snapshot; however, after applying transformations, only the defined columns are retrieved at load time.

**Why This Matters:**

- **Leaner dataset:** Reduced memory consumption and faster processing
- **Better performance:** Query execution and report load times improve, especially with large datasets
- **Context preserved:** Product and customer details are still accessible via properly connected dimension tables in our Star Schema design

We applied the same column reduction approach to other key fact tables (`fact_forecast_monthly` and `fact_actuals_estimates`), ensuring consistency and optimal model performance across the entire project.

---
#### Calculated Columns using DAX

With our optimized data model in place, we now needed to calculate the complete P&L statement. Using DAX, we created calculated columns for all P&L line items:

```excel
post_invoice_deductions_amount =
VAR _result = CALCULATE(MAX(post_invoice_deductions[discounts_pct])
, RELATEDTABLE(post_invoice_deductions))
RETURN _result * fact_actuals_estimates[net_invoice_sales_amount]

post_invoice_other_deductions_amount = 
VAR _result = CALCULATE(MAX(post_invoice_deductions[other_deductions_pct])
, RELATEDTABLE(post_invoice_deductions))
RETURN _result * fact_actuals_estimates[net_invoice_sales_amount]

net_sales_amount = 
fact_actuals_estimates[net_invoice_sales_amount]
- fact_actuals_estimates[post_invoice_deductions_amount]
- fact_actuals_estimates[post_invoice_other_deductions_amount]

manufacturing_cost = 
VAR _result = CALCULATE(MAX(manufacturing_cost[manufacturing_cost])
, RELATEDTABLE(manufacturing_cost))
RETURN _result * fact_actuals_estimates[qty]

freight_cost = 
VAR _result = CALCULATE(MAX(freight_cost[freight_pct])
, RELATEDTABLE(freight_cost))
RETURN _result * fact_actuals_estimates[net_sales_amount]

other_cost = 
VAR _result = CALCULATE(MAX(freight_cost[other_cost_pct])
, RELATEDTABLE(freight_cost))
RETURN _result * fact_actuals_estimates[net_sales_amount]

total_cogs_amount = 
fact_actuals_estimates[manufacturing_cost]
+ fact_actuals_estimates[freight_cost]
+ fact_actuals_estimates[other_cost]

gross_margin_amount = 
fact_actuals_estimates[net_sales_amount]
- fact_actuals_estimates[total_cogs_amount]

```
---

### **✅ Data Validation: Getting the Numbers Right**

Before anything goes live, we must always validate the numbers, not once, but twice or even three times if needed. We’re not just building visuals; we’re helping stakeholders make decisions and if the numbers are off, the decisions will be too, which could cost the business.

That’s why it’s critical to verify our **DAX calculations and Data Model** against the raw data. It’s better to catch issues early than explain them later. Whenever possible review key metrics with stakeholders to make sure everything aligns.

At the end of the day, our job is to **support the business with the right insights** and that starts with getting the numbers right.

![](/assets/14-number-check.png)

#### File Size Optimization: Performance vs. Storage

Following the implementation of our transformations and calculations, we observed the `.pbix` file size escalate from **120 MBs** to **257 MBs**. This prompted an investigation into optimization opportunities.

**Using DAX Studio for Analysis:**

We leveraged **DAX Studio** to analyze memory consumption and identified that the `fact_actuals_estimates` table was the primary contributor. Columns like `total_cogs_amount`, `gross_margin_amount`, `net_sales_amount`, and various deduction amounts consumed the most significant portions of space.

![](/assets/15-dax-studio1.png)

![](/assets/16-dax-studio2.png)

**Optimization Strategy:**

A key insight: our Power BI report is **directly connected to the MySQL database**, enabling dynamic data retrieval. This means certain columns don't need to be pre-cached in Power Query—they can be fetched on-the-fly from the database.

**Decision:** Remove `gross_price`, `pre_invoice_discount_pct`, and `pre_invoice_discount_amount` from Power Query, allowing them to be retrieved dynamically. This strategic removal yielded a **~20 MB reduction** in file size.

Additionally, we examined `total_cogs_amount` and `gross_margin_amount`, which were initially created as calculated columns. Since their calculations are straightforward arithmetic, they can be computed as **dynamic measures** instead, further reducing pre-calculated storage.

![](/assets/17-file-size.png)

**Trade-off:** This optimization introduces a slight delay in calculations due to dynamic retrieval, but the benefit of reduced file size and improved refresh performance outweighs the negligible latency.

**Final Result:** After these optimizations, the `.pbix` file size decreased to **189 MBs** - a 27% reduction from the peak.

---

>[!NOTE]
> Now it's time to create DAX measures to support our metrics, visuals, and KPIs. Once the core measures are in place, we'll begin building dashboards and data visualizations. Additional measures may be created on the fly to enhance specific visual elements, support our data storytelling, and improve the overall user experience.
>
> 👉 **Explore all DAX Measures [here](/Supplemental%20Docs/DAX%20Calculations.md)**

---

#### Creating Temporal Filters: Quarters, YTD, and YTG

To enable flexible time-based analysis aligned with AtliQ's fiscal calendar, we created several temporal dimensions:

**Fiscal Quarter Alignment:**

AtliQ uses a fiscal year starting in September (not January). To align dates correctly:

1. Created a **fiscal month number** (`fy_month_num`) by shifting calendar months by 4 positions
    - Example: September (calendar month 9) becomes fiscal month 1
2. Calculated quarters by grouping every three fiscal months (Q1, Q2, Q3, Q4)
3. This ensures reports and filters match AtliQ's fiscal reporting periods

**Year to Date (YTD) and Year to Go (YTG):**

We added dynamic time period markers:

- **Year to Date (YTD):** From fiscal year start (September 1) to the last recorded sales date
- **Year to Go (YTG):** From the day after last sales to fiscal year end (August 31)

The logic compares each date's fiscal month number with the fiscal month of the last recorded sale:

- If the date is after the last sale month → mark as YTG
- Otherwise → mark as YTD

This column is stored in `dim_date` and can be used in page slicers and filters for dynamic period-based analysis.

Next, we enhanced the `P & L Values` measure to make it more dynamic, and also created a dynamic title measure called `Selected P & L Row` for the P&L report.

---
> [!NOTE] 
> Before proceeding further, we scheduled a check-in with the Product Owner to review our progress and share a preview of the P&L report. This turned out to be a valuable step, as the Product Owner mentioned that both they and the CEO are particularly interested in tracking ***Net Profit*** figures. Based on this feedback, we will now work on our P&L setup to take the **Net Profit** into consideration.

![](/assets/18-p-l-check.png)

---
#### From Gross Margin to Net Profit: Adding Operational Expenses

During a check-in with the **Product Owner**, we learned that both they and the CEO are particularly interested in tracking **Net Profit** figures—not just Gross Margin. This feedback prompted an important expansion of our P&L analysis.

**The Distinction:**

- **Gross Margin** = Net Sales minus Cost of Goods Sold (COGS)
- **Net Profit** = Gross Margin minus all operational expenses

AtliQ incurs various operational expenses:

- **Advertising & Promotions:** Social media campaigns, billboards, newspaper ads
- **Operating Costs:** Rent, utilities, accounting fees, and other overheads required to keep the business running

**Implementing Net Profit:**

The Product Owner provided an additional Excel file containing **country-wise spending data** on advertising, promotions, and operational costs, broken down by fiscal year. We:

1. Loaded the new dataset into Power BI using Power Query
2. Transformed and integrated it into our existing data model
3. Created custom columns for `other_operational_expenses` and `ads_promotion`
4. Built DAX measures for:
    - Ads Promotions $
    - Other Operational Expenses $
    - All Operational Expenses $
    - **Net Profit $**
    - **Net Profit %**
5. Updated the `P & L Rows` static table to include these new line items
6. Enhanced the `P & L Values` DAX measure to account for the new calculations

The result: a **comprehensive P&L statement** spanning from Gross Price down to Net Profit, providing stakeholders with a complete view of profitability at every stage of the value chain.

Now with the data model complete, validated, and optimized, we're ready to move into **dashboard design and visualization** translating all these calculations into intuitive, interactive reports that tell AtliQ's business story.

---
## **Dashboard design and visualization** 

### Finance View

Our Finance view is already after implementing the above steps.
- ﻿﻿In the finance view, we provide a detailed P&L statement along with essential visualizations and charts. These visuals help highlight key metrics like revenue, expenses, and profit trends, enabling better financial analysis and decision-making.
- ﻿﻿Instead of using columns directly, we are utilizing measures, which allow for more dynamic calculations and offer greater flexibility.
- ﻿﻿Creating measures is more efficient than using columns directly as they allow dynamic calculations, optimize performance, and reduced file size.

| ![](/assets/18.2-finance-mockup.png) | Finance Mockup & Dash Finance View | ![](/assets/20-sales-view-pbi.png) |
| ---------------------------- | ---------------------------------- | -------------------------- |


### Sales View

Before selecting KPIs and visuals for the Sales view, it's important to understand the underlying business dynamics:

**Gross Margin vs. Net Profit** Gross margin is a better indicator of sales performance because it measures profit after direct costs (COGS), isolating the effectiveness of the sales operation itself. Net profit includes additional expenses beyond sales, making it less focused on sales-specific performance.

**Marketing Spend Evolution** Growing companies typically invest heavily in advertising and promotions early on to build market presence. As the company expands and establishes itself, the relative need for these promotional activities decreases, resulting in lower ad spend as a percentage of revenue.

**Operational Efficiency** As companies grow and reduce advertising spend, operational expenses may increase in absolute terms, but they become more streamlined and efficient on a per-unit or per-revenue basis. This efficiency gain is a sign of healthy scaling.


We have drafted our visuals for the Sales view page. Next up we will be prepping up our Marketing View.

| ![](/assets/19-sales-view-mockup.png) | Sales Mockup & Dash Sales View | ![](/assets/20-sales-view-pbi.png) |
| ----------------------------- | ------------------------------ | -------------------------- |


### Marketing View

- For the Marketing view, we mainly look at net sales, net profit, and net profit percentage to measure our success and profitability.
- ﻿﻿Net profit is obtained by subtracting operational expenses, taxes, and other costs from the gross margin.

| ![](/assets/21-marketing-mockup.png) | Marketing Mockup | ![](/assets/22-marketing-dash.png) |
| ---------------------------- | ---------------- | -------------------------- |


### Supply Chain View

#### Supply Chain - Forecasting Fundamentals

- **Excess Inventory** occurs when a business stocks more than it can sell, leading to additional storage and maintenance costs.
- **Actual Absolute Net Error** is calculated by taking the absolute difference between forecasted and actual values for each period and summing them up.
- **Absolute Net Error Percentage** is calculated by dividing the Absolute Net Error by the total forecasted values and multiplying by 100.
- **Forecast Accuracy** is calculated as 1 minus the Absolute Net Error Percentage.
- **Net Error** is the difference between the actual sales quantity and the forecast sales quantity, indicating how accurate the forecast is.

Take a look at [[Supply Chain Forecasting]] for a proper breakdown of the concept to understanding the importance of correct forecasting.

| ![](/assets/23-supply-chain-mockup.png) | Supply Chain Mockup & Dash View | ![](/assets/24-supply-chain-dash.png) |
| ------------------------------- | ------------------------------- | ----------------------------- |


Let's polish our dashboard following the designing principles.

![](/assets/25-designing-principles.png)

---

## User Acceptance Testing

The purpose of UAT is to identify and fix any issues or inconsistencies before presenting our LIVE solution to stakeholders.

![](/assets/26-UAT.png)

### Power BI Service

Key capabilities:
- Export reports to Excel for analysis
- Review Semantic Model
- Configure user access via workspace settings

![](/assets/27-export-to-excel.png)


## Stakeholder Meeting

Now that we have published our report in the UAT environment, it's time to schedule a meeting with Stakeholders to get some feedback.

Below is my Stakeholder Mapping Analysis matrix - I use this to align goals, requirements and success metrics:
- Identifies stakeholder interests and influence levels
- Improves communication and engagement strategies
- Captures expectations and implements feasible recommendations
- Keeps informed parties included if they have valuable expertise

![](/assets/28-stakeholder-mapping-analysis.png)

The meeting didn't go as expected - we got critical feedback showing gaps between the solution and actual needs. But this is exactly what we needed to avoid building another report that sits unused.

Documented all stakeholder feedback. Now we will be implementing the requested features to turn this into a decision-making tool that actually delivers business value.

| Serial No | Feedback List from Stakeholder meeting                                        | Requester        |
| --------- | ----------------------------------------------------------------------------- | ---------------- |
| 1         | Reports Loads Slow                                                            | Bruce Haryali    |
| 2         | Add date of refresh, currency type, values are in Millions, Currency is USD.  | Tchalla Tiwari   |
| 3         | Fix Label (SC Label)                                                          | Tchalla Tiwari   |
| 4         | 2022 Est chart does not look correct                                          | Tchalla Tiwari   |
| 5         | Show GM %, not GM in sales and Marketing Chart                                | Nick Puri        |
| 6         | Show Targets                                                                  | Nick Puri        |
| 7         | Ideally have a button to switch between Targets & YoY comparison              | Thor Hathodawala |
| 8         | Highlight Market / Customers by GM % Target Variance - (Add a dynamic slicer) | Nick Puri        |
| 9         | Possible to switch between GM % and NP % in the Marketing view?               | Natasha Rao      |
| 10        | Can we see products per customer in one table?                                | Thor Hathodawala |
| 11        | Is it possible to show Market Share in Executive view?                        | Carol Marvari    |
| 12        | I need to see full Trend over Table (Drill through / Tool Tip)                | Bruce Haryali    |

### Quick Fixes

Let's begin by working on quick fixes

| Quick Fixes                                                                  | Action Taken                                                                                                                                                                                                                                                                                                                                                                                    |
| ---------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 2022 Est chart does not look correct (Supply Chain)                          | Fixed the Forecast Quantity DAX Formula                                                                                                                                                                                                                                                                                                                                                         |
| Show GM %, not GM in sales and Marketing Chart                               | Updated the scatter plots in marketing and sales view by replacing Gross Margin $ with Gross Margin %                                                                                                                                                                                                                                                                                           |
| Fix Label (SC Label)                                                         | Corrected KPI labels in Supply Chain View                                                                                                                                                                                                                                                                                                                                                       |
| Add date of refresh, currency type, values are in Millions, Currency is USD. | - Created DAIssueResolution2022 Forecast chart incorrect (Supply Chain)Fixed Forecast Quantity DAX formulaGM $ showing instead of GM % in Sales & MarketingReplaced Gross Margin $ with Gross Margin % in scatter plotsSupply Chain label errorsCorrected KPI labelsMissing metadataAdded data refresh date, currency type (USD), and values in Millions using DAX measures and Power Query<br> |

```spreadsheet
-- Report Refresh Date

let
Source = #table(type table[Report Last Refreshed=datetime], {{DateTime.LocalNow()}})
in
Source

= #table(type table[Report Last Refreshed=datetime], {{DateTime.LocalNow()}})
```

### Feature Implementation

#### Show Targets / Target Integration
Loaded target data from Excel and added reference labels to Finance view KPIs. Implemented filtering by region/country to display relevant targets. Added N/A handling since targets only exist at country level.
#### Target vs YoY Comparison
Instead of adding a toggle button (which would overcomplicate things), we display both target and last year values in KPIs. This keeps it simple and functional. Added a footer note to clarify when targets aren't applicable.

![](/assets/29-targets.png)
#### GM % Target Variance Highlighting
Created dynamic parameter and GM % variance slicer with custom filters for scatter plot visuals. Built DAX measures: `Target Gap Tolerance Value` & `GM % Variance`.

![](/assets/30-gap-tolerance-param.png)

#### GM % / NP % Toggle (Marketing View)
Implemented button bookmark switching to toggle between Gross Margin % and Net Profit % views.

![](/assets/31-net-profit-vs-gross-margin-booswitch.png)

#### Trend Analysis

Added trend tooltips for detailed trend visualization without cluttering the main dashboard.
![](/assets/32-trend-tooltip.png)

#### Market Share Analysis (Executive View)

Loaded market share data and created DAX measures for `Revenue Contribution` and `AtliQ's Market Share`. Built top-level executive dashboard for market share comparison.

![](/assets/33-atliq-market-share.png)

### Product-Customer Analysis

Added Top N filtering with visuals for Top 5 Customers and Top 5 Products.

![](/assets/34-top-5.png)


We took the feedback constructively and implemented the requested features step by step.

Now our dashboard is ready and published to Power BI Service.

In real-world scenarios, transactional databases get updated with new records daily. We need to make sure we're analyzing updated data with a live report. So we set up automated data refresh scheduling.

![](/assets/35-data-refresh.png)

For non-system data sources, we can grant access through credentials and refresh the data on demand.

That's it folks, we now have our report live and running with complete documentation, fully simulating a real-world project.

Check out the live dashboard here: [AtliQ Business Intelligence Dashboard](https://app.powerbi.com/view?r=eyJrIjoiMTBjNjNmNDItYzUyMi00NzEwLTkwMDEtYTVkYjU4MmVmMGJkIiwidCI6IjcwZjQ5MWZkLTA3ODAtNDNhOS1iOTRmLThjZmEzOTlkZWRkOCJ9)

**By: Limesh Mahial**

---


- [^1] [AtliQ Business Model & Strategic Challenge](/Supplemental%20Docs/AtliQ%20Business%20Model%20&%20Strategic%20Challenge.md)
- [^2] [Data Storage](/Supplemental%20Docs/Data%20Storage.md)
- [^3] [DAX Calculations](/Supplemental%20Docs/DAX%20Calculations.md)
- [^4] [Profit & Loss Statement](/Supplemental%20Docs/Profit%20&%20Loss%20Statement.md)
- [^5] [Supply Chain Forecasting](/Supplemental%20Docs/Supply%20Chain%20Forecasting.md)
