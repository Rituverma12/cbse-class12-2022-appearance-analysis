# Methodology — CBSE Class 12 (2022) Appearance Analysis

This document explains how the dataset was prepared, validated, and analyzed to study student appearance patterns across regions and school types.  
The focus of the methodology is **data credibility**, **statistical weight**, and **reproducibility**.

## 1. Data Source & Scope

- Dataset: CBSE Class 12 (2022) — Registered vs Appeared students  
- Dimensions used:
  - Region
  - School Type
  - Registration status (Registered / Appeared)
- Unit of analysis:
  - One row = one Region × School Type combination

The analysis does **not** compare individual students.  
All insights are derived from **aggregated administrative counts**.

## 2. Data Modeling Approach

The workbook follows a layered structure inspired by real-world analytics workflows:

| Layer | Purpose |
| ----- | -------- |
| `01_DATA_MODEL_raw_wide` | Raw, wide-format source data |
| `02_FACT_APPEARANCE (VIEW)` | Canonical fact table for analysis |
| `03_DATA_QUALITY_LOG (VIEW)` | Integrity and audit checks |
| `04_CREDIBILITY` | Coverage-weighted credibility analysis |
| `05_ANALYSIS` | Anomaly and risk exploration |
| `06_VOLATILITY` | Stability and variability analysis |
| `07_DASHBOARD` | Executive summary |
| `99_NOTES` | Assumptions, thresholds, and methodology |

Only **one fact table** (`02_FACT_APPEARANCE (VIEW)`) is used for analysis.

## 3. Data Transformation (Power Query)

All row-level logic was implemented in **Power Query** to ensure:

- transparency
- reproducibility
- separation of data preparation from analysis

### Key transformations

- Wide school-type columns were unpivoted into a normalized structure
- Registered and Appeared counts were separated
- Analytical flags were derived per row

### Derived columns include

- `appearance_rate`
- `no_show_rate`
- `base_flag`
- `anomaly_flag`
- `risk_bucket`
- `is_usable_for_analysis`
- `fact_id`

No calculated columns were created in Power Pivot.

---

## 4. Base Size Classification (SMALL_BASE)

To avoid misleading conclusions from very small denominators, base sizes were classified.

### Threshold selection

- Smallest non-zero base: 22
- Median non-zero base: ~4,067
- Only 9 rows had Registered < 100

Based on this distribution, **Registered < 100** was classified as `SMALL_BASE`.

This ensures that:

- perfect appearance rates on small bases are flagged
- system-level conclusions remain stable

## 5. Anomaly Detection (LOW_RATE)

An appearance rate threshold was introduced to flag unusually low performance.

### Observations

- No regions fall below 95%
- Only 2 rows fall below 98%
- 25th percentile appearance rate ≈ 99.34%

### Decision

- `appearance_rate < 0.98` → flagged as `LOW_RATE`

This captures **meaningful underperformance** without over-flagging noise.

## 6. Integrity Checks (Audit Log)

Data integrity checks were separated into an audit log (`03_DATA_QUALITY_LOG (VIEW)`).

Checks include:

- Missing values
- Invalid rates
- Structural zeros (true absence vs missing data)

Structural zeros (e.g., school types not present in certain regions) are treated as **informational**, not errors.

## 7. Measures & Aggregations (Power Pivot)

All aggregation logic was implemented as **DAX measures**, including:

- Total Registered
- Coverage Share
- Usable Coverage Share
- Integrity Score
- Anomalous Coverage

This ensures:

- slicer responsiveness
- consistent weighting
- no hard-coded summaries

## 8. Coverage-Aware Interpretation

Insights are interpreted using **coverage share**, not just row counts.

This avoids misleading conclusions such as:

- overemphasizing small regions
- treating all segments as equally important

For example:

- a few large regions account for most registrations
- small-base perfect rates have negligible system impact

## 9. Limitations

- Analysis is based on aggregated data only
- Causes of absenteeism are not identifiable
- Results should be interpreted in an administrative context

## 10. Reproducibility

- All transformations are documented
- No manual overwrites of computed fields
- Refreshing the raw data updates the entire analysis

This project is designed to be **auditable, explainable, and extensible**.
