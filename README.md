# C-NOS Query - P&G Japan Insights

## Overview

This repository contains a Databricks notebook for analyzing C-NOS (Consumer Net Operating Sales) data for Procter & Gamble Japan operations. The query generates comprehensive sales performance metrics across categories, quarters, and months.

## 📊 What is C-NOS?

**C-NOS (Consumer Net Operating Sales)** represents the net sales to consumers after deductions. This is a key metric for tracking business performance and comparing against targets and year-ago actuals.

## 📁 Contents

- **C-NOS Query.ipynb** - Databricks notebook with SQL queries for C-NOS analysis

## 🎯 Query Purpose

The notebook generates a comprehensive sales dashboard that includes:

1. **Quarterly Performance**
   - JAS (July-August-September) quarter metrics
   - OND (October-November-December) quarter metrics
   - Index vs Year Ago (IYA) comparisons

2. **Monthly Performance**
   - Monthly sales in millions (MM) with currency conversion (JPY/150.1)
   - Month-over-month IYA percentages
   - Coverage: July through November 2025

3. **Half-Year Metrics**
   - H1 (First Half) target performance
   - Half-year to date actuals
   - IYA comparisons

4. **Category Breakdown**
   - Sales metrics per product category
   - Target vs actual performance
   - Year-over-year growth rates

## 📋 Data Source

**Source Table**: `hive_metastore.japan_gold.cnos_vw`

**Key Columns**:
- `category` - Product category
- `quarter` - Business quarter (JAS, OND)
- `month` - Month identifier (e.g., '2025M07')
- `account` - Account type (focused on 'C-NOS')
- `forecast_act_est` - Forecast/Actual/Estimate values
- `target` - Target values
- `ya_actual` - Year-ago actual values

## 🔧 Configuration

### Time Period

The query currently analyzes:
- **Months**: 2025M07 through 2025M12
- **Quarters**: JAS and OND

To update the time period, modify the WHERE clause:
```sql
WHERE month >= '2025M07' AND month <= '2025M12'
  AND quarter IN ('JAS','OND')
```

### Currency Conversion

Sales are converted to millions using the exchange rate:
```sql
cnos / 1000 / 1000 / 150.1  -- Converts to millions with JPY rate
```

Update the rate (150.1) as needed for different periods.

## 📊 Output Metrics

### Column Naming Convention

- **_MM** suffix: Values in millions (currency converted)
- **_IYA** suffix: Index vs Year Ago percentage
- **1H**: Half-year metrics
- **JAS/OND**: Quarter identifiers

### Sample Output Columns

| Column | Description |
|--------|-------------|
| `category` | Product category name |
| `JAS_IYA` | July-Aug-Sep Index vs Year Ago (%) |
| `OND_IYA` | Oct-Nov-Dec Index vs Year Ago (%) |
| `1H_MM` | Half-year target in millions |
| `1H_IYA` | Half-year Index vs Year Ago (%) |
| `JUL_MM` | July sales in millions |
| `JUL_IYA` | July Index vs Year Ago (%) |
| `AUG_MM` | August sales in millions |
| `AUG_IYA` | August Index vs Year Ago (%) |
| ... | (continues for each month) |
| `1H_To_Date_MM` | Half-year to date actuals in millions |
| `1H_To_Date_IYA` | Half-year to date IYA (%) |

## 🚀 How to Use

### Prerequisites

1. Access to Databricks workspace
2. Access to `hive_metastore.japan_gold.cnos_vw` table
3. Read permissions on the gold layer

### Execution Steps

1. **Open Notebook**: Import `C-NOS Query.ipynb` into Databricks
2. **Attach Cluster**: Connect to an appropriate Spark cluster
3. **Run Query**: Execute the SQL cell
4. **Export Results**: Download results as CSV or visualize in Databricks

### Using the Results

The output can be used for:
- Executive dashboards
- Monthly business reviews
- Category performance analysis
- Target vs actual tracking
- Year-over-year trend analysis

## 📈 Query Structure

The query uses CTEs (Common Table Expressions) for clarity:

```
base_cnos           → Base C-NOS data filtered by period
    ↓
quarter_rollup      → Quarterly aggregations
half_year           → Half-year aggregations
month_wide          → Monthly metrics pivoted wide
    ↓
category_metrics    → Final category-level KPIs
```

## 🔍 Key Calculations

### Index vs Year Ago (IYA)

```sql
(current_value / NULLIF(year_ago_value, 0)) * 100
```

### Currency Conversion to Millions

```sql
value / 1000 / 1000 / 150.1
```

Where:
- First `/1000` converts to thousands
- Second `/1000` converts to millions
- `/150.1` adjusts for JPY exchange rate

## ⚠️ Important Notes

1. **NULL Handling**: Uses `NULLIF()` to prevent division by zero errors
2. **Data Availability**: Ensure data exists for the specified period
3. **Exchange Rate**: Update the 150.1 rate based on current business requirements
4. **Quarter Definition**: JAS and OND are specific to P&G fiscal calendar

## 🔄 Maintenance

### Monthly Updates

When updating for new months:

1. Extend the month range in WHERE clause
2. Add new month columns in `month_wide` CTE
3. Update the `1H_to_date` calculation to include new months
4. Verify quarter assignments match fiscal calendar

### Example for Adding December:

```sql
MAX(CASE WHEN month = '2025M12' THEN cnos END)/1000/1000/150.1 AS DEC_MM,
MAX(CASE WHEN month = '2025M12' THEN cnos/NULLIF(cnos_ya,0)*100 END) AS DEC_IYA,
```

## 📞 Support

For questions or issues:
- Check data availability in `japan_gold.cnos_vw`
- Verify access permissions to Databricks workspace
- Contact data team for schema changes or data quality issues

## 📅 Version History

- **Current**: Covers 2025M07-2025M12 (JAS and OND quarters)
- Initial query setup for H1 2025 reporting

---

**Last Updated**: December 25, 2025
