
## P&L Structure

| **Measure Name** | **DAX Formula** |
| --- | --- |
| GS $ |  = SUM(fact_actuals_estimates[gross_sales_amount]) |
| NIS $  | = SUM(fact_actuals_estimates[net_invoice_sales_amount]) |
| Pre Invoice Deductions $  | = [GS $] - [NIS $] |
| NS $ | = SUM(fact_actuals_estimates[net_sales_amount]) |
| Post Invoice Deductions $ | = SUM(fact_actuals_estimates[post_invoice_deductions_amount]) |
| Post Invoice Other Deductions $ | = SUM(fact_actuals_estimates[post_invoice_other_deductions_amount]) |
| Total Post Invoice Deductions $  | = [Post Invoice Deductions $] + [Post Invoice Other Deductions $] |
| Manufacturing Cost $  | = SUM(fact_actuals_estimates[manufacturing_cost]) |
| Freight Cost $ | = SUM(fact_actuals_estimates[freight_cost]) |
| Other Costs $ | = SUM(fact_actuals_estimates[other_cost]) |
| Total COGS $  | = [Manufacturing Cost $] + [Freight Cost $] + [Other Costs $] |
| Total Quantity  | = SUM(fact_actuals_estimates[qty]) |
| GM $  | = [NS $] - [Total COGS $] |
| GM %  | = DIVIDE([GM $],[NS $],0) |
| GM per Unit $  | = DIVIDE([GM $],[Total Quantity],0) |

```excel
P & L Values = 
SWITCH(TRUE(),
MAX('P & L Rows'[Order])=1,[GS $]/1000000,
MAX('P & L Rows'[Order])=2,[Pre Invoice Deductions $]/1000000,
MAX('P & L Rows'[Order])=3,[NIS $]/1000000,
MAX('P & L Rows'[Order])=4,[Post Invoice Deductions $]/1000000,
MAX('P & L Rows'[Order])=5,[Post Invoice Other Deductions $]/1000000,
MAX('P & L Rows'[Order])=6,[Total Post Invoice Deductions $]/1000000,
MAX('P & L Rows'[Order])=7,[NS $]/1000000,
MAX('P & L Rows'[Order])=8,[Manufacturing Cost $]/1000000,
MAX('P & L Rows'[Order])=9,[Freight Cost $]/1000000,
MAX('P & L Rows'[Order])=10,[Other Cost $]/1000000,
MAX('P & L Rows'[Order])=11,[Total COGS $]/1000000,
MAX('P & L Rows'[Order])=12,[GM $]/1000000,
MAX('P & L Rows'[Order])=13,[GM %]*100,
MAX('P & L Rows'[Order])=14,[GM per Unit $]
)
--
P & L LY = CALCULATE([P & L Values], SAMEPERIODLASTYEAR(dim_date[date]))
--
P & L YoY Change = [P & L Values] - [P & L LY]
--
P & L YoY Change % = DIVIDE([P & L YoY Change],[P & L LY],0)*100
```

```excel
-- Calculated Table
P & L Columns = 
VAR _X = ALLNOBLANKROW(fiscal_year[fy_desc])
RETURN
UNION(
    ROW("Column Header", "LY"),
    ROW("Column Header", "YoY Change"),
    ROW("Column Header", "YoY Change %"),
    _X
)

--
P & L Final Values = 
SWITCH(TRUE(),
SELECTEDVALUE(fiscal_year[fy_desc])=
MAX('P & L Columns'[Column Header]),[P & L Values],
MAX('P & L Columns'[Column Header]) = "LY", [P & L LY],
MAX('P & L Columns'[Column Header]) = "YoY Change", [P & L YoY Change],
MAX('P & L Columns'[Column Header]) = "YoY Change %", [P & L YoY Change %]
)
```

## YTD/YTG Slicers & Fiscal Quarters

```excel
fiscal_month = MONTH(DATE(YEAR(dim_date[date]),MONTH(dim_date[date])+4,1))

fiscal_quarter = "Q" & ROUNDUP(dim_date[fiscal_month]/3,0)

ytd_ytg = 
VAR _LASTSALESDATE = MAX(fact_sales_monthly[date])
VAR _FYMONTHNUM = MONTH(DATE (YEAR(_LASTSALESDATE), MONTH(_LASTSALESDATE) +4, 1))
RETURN
IF (dim_date[fiscal_month]>_FYMONTHNUM, "YTG", "YTD")
```

```excel
P & L Values = 
    VAR _RESULT = 
        SWITCH(
            TRUE(),
            MAX('P & L Rows'[Order])=1,[GS $]/1000000,
            MAX('P & L Rows'[Order])=2,[Pre Invoice Deductions $]/1000000,
            MAX('P & L Rows'[Order])=3,[NIS $]/1000000,
            MAX('P & L Rows'[Order])=4,[Post Invoice Deductions $]/1000000,
            MAX('P & L Rows'[Order])=5,[Post Invoice Other Deductions $]/1000000,
            MAX('P & L Rows'[Order])=6,[Total Post Invoice Deductions $]/1000000,
            MAX('P & L Rows'[Order])=7,[NS $]/1000000,
            MAX('P & L Rows'[Order])=8,[Manufacturing Cost $]/1000000,
            MAX('P & L Rows'[Order])=9,[Freight Cost $]/1000000,
            MAX('P & L Rows'[Order])=10,[Other Cost $]/1000000,
            MAX('P & L Rows'[Order])=11,[Total COGS $]/1000000,
            MAX('P & L Rows'[Order])=12,[GM $]/1000000,
            MAX('P & L Rows'[Order])=13,[GM %]*100,
            MAX('P & L Rows'[Order])=14,[GM per Unit $]
            )

RETURN
IF(HASONEVALUE('P & L Rows'[Description]),_RESULT, [NS $]/1000000)
```

```excel
Selected P & L Row = IF(HASONEVALUE('P & L Rows'[Description]),SELECTEDVALUE('P & L Rows'[Description]), "Net Sales") & " Performance Over Time"
```


```excel
ads_promotion = 
VAR _result = CALCULATE(MAX('Operational Expenses'[ads_promotions_pct])
, RELATEDTABLE('Operational Expenses'))
RETURN _result * fact_actuals_estimates[net_sales_amount]


other_operational_expenses = 
VAR _result = CALCULATE(MAX('Operational Expenses'[other_operational_expense_pct])
, RELATEDTABLE('Operational Expenses'))
RETURN _result * fact_actuals_estimates[net_sales_amount]

All Operational Expenses $ = [Other Operational Expense $] + [Ads & Promotions $]

Net Profit $ = [GM $] - [All Operational Expenses $]

Net Profit % = DIVIDE([Net Profit $],[NS $],0)
```

```excel
P & L Values = 
    VAR _RESULT = 
        SWITCH(
            TRUE(),
            MAX('P & L Rows'[Order])=1,[GS $]/1000000,
            MAX('P & L Rows'[Order])=2,[Pre Invoice Deductions $]/1000000,
            MAX('P & L Rows'[Order])=3,[NIS $]/1000000,
            MAX('P & L Rows'[Order])=4,[Post Invoice Deductions $]/1000000,
            MAX('P & L Rows'[Order])=5,[Post Invoice Other Deductions $]/1000000,
            MAX('P & L Rows'[Order])=6,[Total Post Invoice Deductions $]/1000000,
            MAX('P & L Rows'[Order])=7,[NS $]/1000000,
            MAX('P & L Rows'[Order])=8,[Manufacturing Cost $]/1000000,
            MAX('P & L Rows'[Order])=9,[Freight Cost $]/1000000,
            MAX('P & L Rows'[Order])=10,[Other Cost $]/1000000,
            MAX('P & L Rows'[Order])=11,[Total COGS $]/1000000,
            MAX('P & L Rows'[Order])=12,[GM $]/1000000,
            MAX('P & L Rows'[Order])=13,[GM %]*100,
            MAX('P & L Rows'[Order])=14,[GM per Unit $],
            MAX('P & L Rows'[Order])=15,[All Operational Expenses $]/1000000,
            MAX('P & L Rows'[Order])=16,[Net Profit $]/1000000,
            MAX('P & L Rows'[Order])=17,[Net Profit %]*100
)

RETURN
IF(HASONEVALUE('P & L Rows'[Description]),_RESULT, [NS $]/1000000)
```




```excel
Forecast Quantity = 
VAR _LASTSALEDATE = MAX(last_sales_month[last_sales_month])
RETURN
CALCULATE(SUM(fact_forecast_monthly[forecast_quantity]),fact_forecast_monthly[date]<= _LASTSALEDATE)

Sales Quantity = CALCULATE([Total Quantity], fact_actuals_estimates[date] <= MAX(last_sales_month[last_sales_month])) 

Net Error = [Forecast Quantity] - [Sales Quantity]

Net Error % = DIVIDE([Net Error],[Forecast Quantity],0)

ABS Error = 
    SUMX(DISTINCT(dim_date[date]),
        SUMX(DISTINCT(dim_product[product_code]),ABS([Net Error]))
)

ABS Error % = DIVIDE([ABS Error],[Forecast Quantity],0)

Forecast Accuracy % = IF([ABS Error %] <> BLANK(), 1 - [ABS Error %], BLANK())

Forecast Accuracy % LY = CALCULATE([Forecast Accuracy %], SAMEPERIODLASTYEAR(dim_date[date]))

Risk = IF([Net Error] > 0, "Excess Inventory", "Out of Stock" )
```

```excel
NS % LY = CALCULATE([NS],SAMEPERIODLASTYEAR(dim_date[date]))

GM % LY = CALCULATE([GM %],SAMEPERIODLASTYEAR(dim_date[date]))

NP % LY = CALCULATE([Net Profit %],SAMEPERIODLASTYEAR(dim_date[date]))

Net Error LY = CALCULATE([Net Error],SAMEPERIODLASTYEAR(dim_date[date]))

ABS Error LY = CALCULATE([ABS Error],SAMEPERIODLASTYEAR(dim_date[date]))

Last Sale Data Load = "Sales Data Loaded Until: " & FORMAT(MAX(last_sales_month[last_sales_month]),"MMM YYYY")

Report Refresh = "Last Refresh Date: " & FORMAT(MAX('Report Refresh Date'[Report Last Refreshed]),"MMM DD YYYY")
```


```excel
Customer / Product Filter Check = ISCROSSFILTERED(dim_product[product]) || ISFILTERED(dim_customer[customer])

NS $ Target = 
VAR _TARGET = SUM(NsGmTarget[ns_target])
RETURN IF([Customer / Product Filter Check], BLANK(),_TARGET)

GM $ Target = 
VAR _TARGET = SUM(NsGmTarget[gm_target])
RETURN IF([Customer / Product Filter Check], BLANK(),_TARGET)

NP $ Target = 
VAR _TARGET = SUM(NsGmTarget[np_target])
RETURN IF([Customer / Product Filter Check], BLANK(),_TARGET)

GM % Target = DIVIDE([GM $ Target],[NS $ Target],0)

NP % Target = DIVIDE([NP $ Target],[NS $ Target],0)

Target Gap Tolerance Value = SELECTEDVALUE('Target Gap Tolerance'[Target Gap Tolerance])

GM % Variance = [GM % LY] - [GM %]
```

```excel

RC % = DIVIDE([NS $],CALCULATE([NS $],ALL(dim_market),ALL(dim_customer),ALL(dim_product)))

Market Share % = DIVIDE(SUM(marketshare[sales_$]),SUM(marketshare[total_market_sales_$]),0)

AtliQ MS % = CALCULATE('Key Measures'[Market Share %],marketshare[manufacturer]="AtliQ")
```