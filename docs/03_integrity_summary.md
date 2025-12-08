# Integrity Summary - CBSE Class 12 2022

## Purpose

- Validate logical consistency across appearance rates, registered counts, and missing values.
- Integrity checks were migrated from worksheet formulas into Power Query for reproducibility, then unpivoted into the Data Model.
- This sheet serves as a diagnostic pivot module feeding future dashboard panels.

## Setup

- Checks engineered in Power Query, producing two fields:
  - Integrity_CheckType (RateCheck, RegisteredCheck, MissingCheck)
  - Integrity_CheckResult (OK, FLAG)
- Pivot is built over the Data Model with slicers:
  - Region
  - School Type
  - Integrity_CheckResult
- Measures exposed:
  - FlagCount
  - TotalCount
  - PercentFlagged = DIVIDE(FlagCount, TotalCount)

## Integrity Checks (Power Query logic)

### Rate Check

    = if [Appearance Rate] = null then "OK" else if [Appearance Rate] < 0 or [Appearance Rate] > 1 then "FLAG" else "OK"

- **Purpose:** Ensures appearance rates fall withing logic bounds (0-1).

### Registered Check

    = if [Registered] = null or [Appeared] = null then "FLAG" else if [Appeared] > [Registered] then "FLAG" else "OK"

- **Purpose:** Prevents cases where appeared students exceed registered students.

### Missing Check

    = if [Registered] = null or [Appeared] = null then "FLAG" else if [Registered] = 0 or [Appeared] = 0 then "FLAG" else "OK"

- **Purpose:** Flags incomplete or invalid records before analysis.

> These formulas were first implemented in Excel. Now resides in Power Query for transparency and automatic recalculation on refresh.

## Pivot & Charts

- Pivot table fields:

  - **Rows**: Integrity_CheckType (RateCheck, RegisteredCheck, MissingCheck)

  - **Columns**: Integrity_CheckResult (FLAG, OK)

  - **Values**: Count of records

  - **Slicers**: Region, School Type, Integrity_CheckResult

- Example outputs:

  - Count of FLAG vs. OK by check type.

  - Distribution of integrity flags across regions and school types.

- Charts used:

  - **Stacked bar chart** showing FLAG vs. OK counts per check type.

  - **Heatmap** highlighting regions with high integrity failures.

  - **KPI card** showing % of rows flagged.

## Dashboard Panel — Integrity Summary

- Panel Name: **Integrity Panel**

- Slicers: Region, School Type, Integrity_CheckResult

- Visuals:

  - KPI card showing % of rows flagged.

  - Stacked bar chart comparing FLAG vs. OK counts.

  - Drill-through option to inspect flagged rows by slicer selection.

> All visuals now source from the unified Data Model, ensuring slicer consistency across dashboard panels.

- Analytical Role:

  - Quantifies integrity failures and isolates patterns via slicers.

  - Serves as a baseline credibility filter prior to anomaly and volatility analysis.
  
  - Ensures the dataset is audit-ready before profiling.

## Analytical Notes

- Integrity checks enforce logical consistency and prevent downstream distortions.

- Pivot summaries quantify the scale of integrity issues.

- Dashboard visuals make integrity risks transparent to any audience.

- Together, this stage ensures the dataset is audit‑ready before anomaly profiling.

- Integrity checks were migrated from Excel into Power Query, making them reproducible and independent of worksheet formulas.

## Pipeline Context

- Integrity summary corresponds to the validation stage of the pipeline.  
- Checks were originally engineered in fact_table but are now migrated into Power Query and unpivoted into the Data Model.  
- Enables downstream credibility profiling and anomaly detection with slicer-aware pivots.
