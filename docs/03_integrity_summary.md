# Integrity Summary - CBSE Class 12 2022

## Purpose

- The integrity summary validates logical consistency in the `fact_table` before analysis.
- It ensures that appearance rates, registered counts, and missing values are flagged prior to credibility profiling.
- These checks were unpivoted in Power Query into `Integrity_CheckType` and `Integrity_CheckResult` for dashboard pivots.

## Setup

- Derived directly from the `fact_table` diagnostic columns.
- Each integrity formula was added in Excel for transparency and reproducibility.
- Results were unpivoted in Power Query to feed into dashboard slicers and pivot tables.
- Slicers included: `Region`, `School Type`, and `Integrity_CheckResult` (FLAG vs OK).

## Integrity Checks

### Rate Check

    = IF(OR([@[Appearance Rate]]<0, [@[Appearance Rate]]>1), "FLAG", "OK")

- **Purpose:** Ensures appearance rates fall withing logic bounds (0-1).

### Registered Check

    = IF([@Appeared]>[@Registered], "FLAG", "OK")

- **Purpose:** Prevents cases where appeared students exceed registered students.

### Missing Check

    = IF(OR(ISBLANK([@Registered]), ISBLANK([@Appeared]), [@Registered]=0, [@Appeared]=0), "FLAG", "OK")

- **Purpose:** Flags incomplete or invalid records before analysis.

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

- Analytical Role:

  - Provides a baseline credibility filter before anomaly and volatility analysis.

  - Highlights structural patterns in integrity failures (e.g., concentrated missing data in specific school types).

## Analytical Notes

- Integrity checks enforce logical consistency and prevent downstream distortions.

- Pivot summaries quantify the scale of integrity issues.

- Dashboard visuals make integrity risks transparent to any audience.

- Together, this stage ensures the dataset is audit‑ready before anomaly profiling.

## Pipeline Context

- This integrity summary corresponds to the validation stage of the data analysis pipeline.

- Fact_table → enriched with diagnostic flags.

- Integrity checks → unpivoted for pivots and dashboard visuals.

- Enables downstream credibility profiling and anomaly detection.