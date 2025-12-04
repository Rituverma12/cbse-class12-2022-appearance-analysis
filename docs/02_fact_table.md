# Fact Table Construction - CBSE Class 12 2022

The **fact_table** serves as the analytical backbone of this project. It consolidates cleaned data from Power Query and adds diagnostic columns that enable credibility profiling. These derived fields form the feature engineering stage of the data analysis pipeline.

## **Setup**

- Source: `class12-2022 result` (cleaned output from Power Query).
- Copied into a new worksheet named **fact_table**
- All diagnostic columns were added using Excel formulas for transparency and reproducibility.

## **Diagnostic Columns**

1. **Base Status** - Flags whether a row has any registered students.

    ```excel
      = IF([@Registered] = 0, "ABSENT", "NON_ZERO")```

    - **Purpose:** Simplifies filtering by excluding rows with no base.

2. **Base Flag** - Classifies denominator stability

    ```excel
      = IF([@[Registered]] = 0, "ABSENT_BASE", IF([@[Registered]]<100, "SMALL_BASE", "VALID_BASE"))```
    
    - **Purpose:** Protects against fragile denominators.

    - **Threshold:** 100 registered students chosen based on profiling distribution (see [04_baseflag_summary.md](04_baseflag_summary.md)).

3. **Anomaly Flag** - Identifies suspicious or rare behaviors in appearance rates.

    ```excel
      = IF([@Registered]=0, "ABSENT", IF([@[Appearance Rate]]<0.98, "LOW_RATE", IF(AND([@[Appearance Rate]]=1, [@Registered]<100), "SUSPICIOUS_PERFECTION", "NORMAL")))```
    
    - **Purpose:** Detects credibility risks:
      - **LOW_RATES:** Rare Underperformers(<98%)(see 05_anomaly_summary.md).
      - **SUSPICIOUS_PERFECTION:** Perfect rates in small bases.
      - **NORMAL:** Stable rates.

4. **Coverage Share** - Measures school type dominance within a region.

    ```excel
      = [@Registered]/SUM([Registered])```
    
    - **Purpose:** Shows proportional contribution of school type to regional totals.

    - **Analytical Use:** Highlights whether anomalies occur in dominant or marginal categories.

5. **Integrity Checks** - Logical Consistency Tests:

    - **Integrity_RateCheck**

        ```excel
        = IF(OR([@[Appearance Rate]]<0, [@[Appearance Rate]]>1), "FLAG", "OK")```
        

    - **Integrity_RegisteredCheck**

        ```excel
        = IF([@Appeared]>[@Registered], "FLAG", "OK")```

    - **Integrity_MissingCheck**

        ```excel
        = IF(OR(ISBLANK([@Registered]), ISBLANK([@Appeared]), [@Registered]=0, [@Appeared]=0), "FLAG", "OK")```
    
    - **Purpose:** Ensures data integrity before analysis.

    - **Pipeline role:** These checks were letter unpivoted in Power Query to create `Integrity_CheckType` and `Integrity_CheckResult` for dashboard pivots.

## **Analytical Notes**

- **Base Flag** and **Anomaly Flag** are engineered features that encode credibility logic.

- **Coverage Share** contexualizes anomalies by showing their weight in the dataset.

- **Integrity Checks** enforce logical consistency and feed directly into the dashboard.

- Together, these columns move the project from cleaning/preprocessing -> feature engineering stage.

## **Pipeline Context**

This fact_table construction to the **Feature Engineering** stage of the data analysis pipeline.

- Raw cleaned data -> enriched with diagnostic flags.

- Enables downstream **Exploratory Analysis** (pivots, profiling sheets).

- Provides structured inputs for credibility dashboard visuals.
