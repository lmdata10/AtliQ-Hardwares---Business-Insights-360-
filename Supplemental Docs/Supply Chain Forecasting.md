
# Forecasting Concepts for Supply Chain Analysis

## Why Forecasting Matters

Forecasting helps predict future demand so businesses can prepare efficiently. Without it, you either end up with excess inventory eating your budget or run out of stock and lose customers to competitors.

## Key Metrics

### 1. Forecast vs Actuals

- **Forecast**: Your prediction of future demand based on historical data and trends
- **Actuals**: The real sales numbers—your reality check

### 2. Net Error

**Formula:** `Forecast - Actual`

Shows the difference between prediction and reality:

- **Positive value** = over-forecasted (predicted more than sold)
- **Negative value** = under-forecasted (predicted less than sold)

**Example:**

- Forecast: 80 units, Actual: 50 units
- Net Error = 80 - 50 = +30 units (over-forecasted by 30)

#### The Critical Flaw

Net Error has a sneaky problem—errors cancel each other out across time periods.

**Scenario:**

- Oct: Forecast 80, Actual 50 → Net Error = +30
- Nov: Forecast 75, Actual 95 → Net Error = -20
- Dec: Forecast 90, Actual 100 → Net Error = -10
- **Total Net Error = 0**

This makes it look like your forecast was perfect when you were actually wrong every single month!

### 3. Absolute Error (Absolute Net Error)

**Formula:** `|Forecast - Actual|`

Ignores direction and captures the true size of the error. No more hiding mistakes!

**Using the same example:**

- Oct: |80 - 50| = 30
- Nov: |75 - 95| = 20
- Dec: |90 - 100| = 10
- **Total Absolute Error = 60 units**

Now we see the real deviation—60 units of total error, not zero.

### 4. Net Error % (Absolute Error %)

**Formula:** `(Absolute Error / Actual) × 100`

Standardizes errors as a percentage, making it easy to compare across different products or time periods.

**Example:**

- Absolute Error: 60 units
- Total Actuals: 245 units (50+95+100)
- Net Error % = (60 / 245) × 100 = 24.49%

### 5. Forecast Accuracy

**Formula:** `100% - Net Error %`

The gold standard metric—tells you how close you were to the target.

**Example:**

- Net Error %: 24.49%
- Forecast Accuracy = 100% - 24.49% = **75.51%**

## Why Absolute Error is Better

|Net Error|Absolute Error|
|---|---|
|Errors cancel out|Captures true deviation|
|Can show 0% error when forecast is wrong|Honest evaluation|
|Misleading for multi-period analysis|Accurate performance picture|

## Practical Impact

**Scenario 1 (Using Net Error):**

- Shows 0% error and 100% accuracy
- Leads to false confidence
- No action taken to improve forecasting

**Scenario 2 (Using Absolute Error):**

- Shows 24.49% error and 75.51% accuracy
- Reveals need for improvement
- Drives better forecasting practices
![[22-supply-chain-forecasting.png]]
## Quick Reference Table

|Metric|Formula|Purpose|
|---|---|---|
|Net Error|Forecast - Actual|Shows over/under forecast direction|
|Absolute Error|\|Forecast - Actual\||True error magnitude|
|Net Error %|(Absolute Error / Actual) × 100|Standardized error comparison|
|Forecast Accuracy|100% - Net Error %|Overall performance measure|

## Bottom Line

Always use **Absolute Error** for evaluating forecast performance. Net Error alone will hide problems and give you a false sense of accuracy. Accurate forecasting = optimized inventory, better planning, and happier customers.