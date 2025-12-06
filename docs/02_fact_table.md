# Fact Table Construction - CBSE Class 12 2022

The `fact_table` serves as the analytical backbone of this project. It consolidates cleaned data from Power Query and adds diagnostic columns that enable credibility profiling. These derived fields form the feature engineering stage of the data analysis pipeline.

## Setup

- Source: `class12-2022 result` (cleaned output from Power Query).
- Copied into a new worksheet named `fact_table`
- All diagnostic columns were originally added using Excel formulas for transparency.
- These remain in the fact_table for reference, but the active analysis now uses equivalent logic migrated into Power Query and the Data Model.

## Diagnostic Columns

- The following diagnostic columns were implemented in Excel within fact_table.
- For current logic, see [01_Power_Query.md](01_Power_Query.md).

### Base Status — Flags whether a row has any registered students

    = IF([@Registered] = 0, "ABSENT", "NON_ZERO")

- **Purpose:** Simplifies filtering by excluding rows with no base.

### Base Flag — Classifies denominator stability

    = IF([@[Registered]] = 0, "ABSENT_BASE", IF([@[Registered]]<100, "SMALL_BASE", "VALID_BASE"))

- **Purpose:** Protects against fragile denominators.  
- **Threshold:** 100 registered students chosen based on profiling distribution (see [04_baseflag_summary.md](04_baseflag_summary.md)).

### Anomaly Flag — Identifies suspicious or rare behaviors in appearance rates

    = IF([@Registered]=0, "ABSENT", IF([@[Appearance Rate]]<0.98, "LOW_RATE", IF(AND([@[Appearance Rate]]=1, [@Registered]<100), "SUSPICIOUS_PERFECTION", "NORMAL")))

- **Purpose:** Detects credibility risks-
  - `LOW_RATE`: Rare underperformers (<98%) (see [05_anomaly_summary.md](05_anomaly_summary.md)).  
  - `SUSPICIOUS_PERFECTION`: Perfect rates in small bases.  
  - `NORMAL`: Stable rates.

### Coverage Share — Measures school type dominance within a region

    = [@Registered]/SUM([Registered])

- **Purpose:** Shows proportional contribution of school type to regional totals.  
- **Analytical Use:** Highlights whether anomalies occur in dominant or marginal categories.

### Integrity Checks — Logical Consistency Tests

#### Integrity_RateCheck

    = IF(OR([@[Appearance Rate]]<0, [@[Appearance Rate]]>1), "FLAG", "OK")

#### Integrity_RegisteredCheck

    = IF([@Appeared]>[@Registered], "FLAG", "OK")

#### Integrity_MissingCheck

    = IF(OR(ISBLANK([@Registered]), ISBLANK([@Appeared]), [@Registered]=0, [@Appeared]=0), "FLAG", "OK")

- **Purpose:** Ensures data integrity before analysis.  
- **Pipeline role:** These checks were later unpivoted in Power Query to create `Integrity_CheckType` and `Integrity_CheckResult` for dashboard pivots.

## Analytical Notes

- `Base Flag` and `Anomaly Flag` were engineered features in fact_table.
- They are now recalculated in Power Query: the fact_table version is retained only for comparison and validation.

- `Coverage Share` contextualizes anomalies by showing their weight in the dataset.

- `Integrity Checks` enforce logical consistency and feed directly into the dashboard.

- Together, these columns move the project from the cleaning/preprocessing -> feature engineering stage.

## **Pipeline Context**

- The fact_table represented the Feature Engineering stage in the initial pipeline.

- It now serves as a staging artifact only, while active feature engineering resides in Power Query and the Data Model.

- Raw cleaned data -> initially enriched with diagnostic flags in fact_table (Excel formulas).

- These flags have since been migrated into Power Query and the Data Model, which now serve as the active source for exploratory pivots and profiling sheets.

- The Data Model provides the structured inputs for credibility dashboard visuals, while fact_table is retained only for historical validation.
