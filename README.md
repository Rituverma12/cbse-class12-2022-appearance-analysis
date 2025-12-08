# CBSE Class 12 (2022) — Data Credibility Analysis

## Overview

- This repository documents a reproducible pipeline for analyzing the credibility of CBSE Class 12 (2022) results.

- The project consolidates multiple diagnostic layers — base flags, integrity checks, anomaly profiling, and volatility analysis — into pivot‑driven summaries.

- Each stage is designed to make data quality transparent, highlight risks, and provide slicer‑aware visuals that withstand scrutiny.

## Purpose

- Ensure the dataset is **audit‑ready** before interpretation or reporting.

- Validate logical consistency (registered vs appeared counts, rate bounds, missing values).

- Profile anomalies and unusual patterns that may distort credibility.

- Measure volatility across school types and regions to detect instability.

- Provide reviewers with clear, interactive summaries that can be filtered by region and school type.

## Workflow

1. **BaseFlag Summary(Doc. in progress)**  

   - Categorizes rows into ABSENT_BASE, SMALL_BASE, and VALID_BASE.  
   - Distinguishes between row frequency and statistical weight (coverage share).  
   - Surfaces the dominance of VALID_BASE and the zero‑weight nature of ABSENT_BASE.  

2. **Integrity Summary**  

   - Validates logical consistency: appearance rates, registered counts, and missing values.  
   - Quantifies the proportion of flagged records and isolates structural risks.  
   - Provides a baseline credibility filter before anomaly detection.  

3. **Anomaly Summary(Doc. in progress)**  

   - Profiles unusual patterns such as LOW_RATE or SUSPICIOUS_PERFECTION.  
   - Measures prevalence and coverage impact of anomalies.  
   - Highlights whether anomalies are marginal or concentrated in specific regions or school types.  

4. **Volatility Summary(Doc. in progress)**  
   - Examines fluctuations in appearance rates across school types.  
   - Identifies instability that may signal credibility concerns.  
   - Provides statistical measures (range, standard deviation) for volatility.  

5. **Dashboard (Planned)**  
   - Unifies all diagnostic panels into a single credibility dashboard.  
   - Presents KPIs, charts, and interpretation blocks in one audit‑ready view.

## Tools & Skills Used

- **Excel (`PivotTables`, `PivotCharts`, `Slicers`)** — interactive summaries of base flags, integrity checks, anomalies, and volatility.

- **Power Query** — migrated diagnostic formulas into reproducible query logic; unpivoted checks into the Data Model. 

- **Data Model & DAX Measures** — created measures such as `FlagCount`, `TotalCount`, `PercentFlagged`, `Coverage Share` for slicer‑aware KPIs.  

- **Visualization** — stacked column charts, dual‑axis charts, pie charts, and heatmaps to contrast frequency vs weight and highlight integrity risks.

- **Documentation (Markdown)** — README and sheet‑level `.md` files explaining purpose, setup, and analytical role of each pivot.  

## Project Progress

- ✅ BaseFlag Summary complete  
- ✅ Integrity Summary complete  
- ✅ Anomaly Summary drafted  
- ✅ Volatility pivots created  
- 🔜 Dashboard assembly (Credibility Dashboard panel)  

## Analytical Role

- **Transparency:** Every pivot and chart is slicer‑aware, ensuring reviewers can filter by region and school type.

- **Credibility:** Each stage enforces a different dimension of data quality — from logical consistency to anomaly detection.

- **Audit‑readiness:** KPIs and interpretation blocks make findings clear to non‑technical audiences.

- **Reproducibility:** All logic is migrated into the Data Model, avoiding hidden worksheet formulas.

## Repository Contents

- `class12-2022 cleaned (version 2).xlsb`: main Excel workbook with pivot summaries and profiling sheets.

        - `pivot_baseflag_summary`: frequency vs coverage analysis of base flags.

        - `pivot_integrity_summary`: logical consistency checks and flagged proportions. 

        - `pivot_anomaly_summary`: anomaly profiling by type and coverage share.  

        - `charts_volatility`: volatility measures across school types.  

        - `profiling_*` sheets: deeper statistical profiling of base flags, anomalies, and volatility.  

- `README.md` — project documentation and analytical context.  

This repository demonstrates a reproducible pipeline for credibility analysis of CBSE Class 12 (2022) results. By systematically documenting base flags, integrity checks, anomalies, and volatility, it ensures that the dataset is not only analyzed but defended — transparent, reproducible, and credible to any audience.