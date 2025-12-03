# CBSE Class XII 2022 Appearance Analysis

This project analyzes CBSE Class XII 2022 results across regions and school types.  
It goes beyond simple appearance rates to include:

- **Base-size flags** (credibility thresholds for Registered counts)
- **Anomaly detection** (low rates, suspicious perfection)
- **Coverage share** (school type dominance in regions)
- **Volatility index** (performance spread across school types)
- **Integrity checks** (absence vs missing vs failure vs error)

The goal is to build a transparent, annotated dashboard and dataset pipeline that withstands scrutiny and demonstrates analytical depth.

## Current progress

- **Raw dataset uploaded**

- **Power Query Transformation Completed**

  - Loaded raw dataset into Power Query Editor
  - Promoted headers and set data types
  - Renamed 'Type' column to 'Region' (for clarity and consistency)
  - Unpivoted School Types from CTSA to KV, resulting in 'Attribute' and 'Value' columns.
  - Renamed 'Attribute' column to 'School Type.'
  - Pivoted 'Status' into 'Regd.' and 'Appd.' columns.
  - Added computed column:

    - **Appearance Rate**

      ```powerquery
      = if [#"Regd."] > 0 then [#"Appd."]/[#"Regd."] else null

      Renamed 'Regd.' and 'Appd.' to 'Registered' and 'Appeared'

    - **No-show Rate**

      ```powerquery
      = if [Registered] > 0 then 1 - ([Appeared] / [Registered]) else null

    - **Absence Flag**

      ```powerquery
      = if [Registered] = 0 and [Appeared] = 0 then "ABSENT" else null
      
    - **Error Flag**

      ```powerquery
      = if [Registered] = 0 and [Appeared] > 0 then "DATA_ERROR" else null

    - **Missing Flag**
  
      ```powerquery
      = if [Registered] = null or [Appeared] = null then "MISSING" else null
      
    - **Failure Flag**

      ```powerquery
      = if [Registered] > 0 and [Appeared] = 0 then "FAIL" else null

  - Removed: Error Flag, Missing Flag, and Failure Flag. Since no cases were found in the dataset.

  - Cleaned query output is renamed 'class12-2022 result' worksheet in Excel.

- **Fact Table Preparation**

  - Copied the result sheet 'class12-2022 result' into a new sheet named 'fact_table'.
  
  - Added diagnostic columns:

    - **Base Status**

      ```excel
      = IF([@Registered] = 0, "ABSENT", "NON_ZERO")

    - **Base Flag**

      ```excel
      = IF([@[Registered]] = 0, "ABSENT_BASE", IF([@[Registered]]<100, "SMALL_BASE", "VALID_BASE"))

    - **Anomaly Flag**

      ```excel
      = IF([@Registered]=0, "ABSENT", IF([@[Appearance Rate]]<0.98, "LOW_RATE", IF(AND([@[Appearance Rate]]=1, [@Registered]<100), "SUSPICIOUS_PERFECTION", "NORMAL")))

    - **Coverage Share**

      ```excel
      = [@Registered]/SUM([Registered])

    - **Integrity_RateCheck**

      ```excel
      = IF(OR([@[Appearance Rate]]<0, [@[Appearance Rate]]>1), "FLAG", "OK")

    - **Integrity_RegisteredCheck**

      ```excel
      = IF([@Appeared]>[@Registered], "FLAG", "OK")

    - **Integrity_MissingCheck**

      ```excel
      = IF(OR(ISBLANK([@Registered]), ISBLANK([@Appeared]), [@Registered]=0, [@Appeared]=0), "FLAG", "OK")

- **Pivot Table Setup**

  A pivot table named 'minbase_pivot' was created from the 'fact_table' to profile the size of operating bases across regions and school types. This pivot was necessary because a 100% appearance rate for 2 students and a 100% appearance rate for 1000 students do not carry the same credibility.

  Small bases can produce fragile or misleading rates, while large bases provide stable evidence of performance.

  - Create a pivot table from 'fact-table.'
  - Rows: Region, School Type
  - Values: Registered -> Value Field Settings -> Value Field = Min
  - Filter: Base Status -> Dropped-down arrow -> Select only "NON-ZERO" to exclude ABSENT status.
  - Slicer: Added for School Type to allow interactive exploration of patterns within each category. For example, CTSA and GOVT AIDED often show small bases, while KV and INDEPENDENT operate at large bases.

- **"profiling_baseflag" sheet**

  The profiling sheet was created to support the credibility of setting up a small base later. To determine the size of the base, so that the denominator is large enough to be statistically stable. It specifically analyzes the distribution of registered counts across different thresholds.

  The table below summarizes key metrics:

    | Metric                   | Value          |
    | -------------------------|----------------|
    |Smallest non-zero base    | 22             |
    |25th percentile (non-zero)| 1385.75        |
    |Median (non-zero)         | 4067.5         |
    |Count below 30            | 1              |
    |Count below 50            | 2              |
    |Count below 100           | 9              |
    |Count below 1000          | 16             |

  Formulas used above:

  - Smallest non-zero base

    ```excel
    = MINIFS(fact_table!E:E, fact_table!E:E, ">0")

  - 25th percentile (non-zero)
  
    ```excel
    = PERCENTILE.INC(FILTER(fact_table!E:E, fact_table!E:E > 0), 0.25)

  - Median (non-zero)

    ```excel
    = MEDIAN(FILTER(fact_table!E:E, fact_table!E:E > 0))

  - Count below 30

    ```excel
    = COUNTIFS(fact_table!E:E, "<30", fact_table!E:E, ">0")

  - Count below 50
  
    ```excel
    = COUNTIFS(fact_table!E:E, "<50", fact_table!E:E, ">0")

  - Count below 100
  
    ```excel
    = COUNTIFS(fact_table!E:E, "<100", fact_table!E:E, ">0")

  - Count below 1000
  
    ```excel
    = COUNTIFS(fact_table!E:E, "<1000", fact_table!E:E, ">0")

  These result shows that data is heavily skewed toward large operating bases. Only 9 out of 97 rows(including 0 registered) fall below 100, while the 25th percentile is 1385.75 and the median is 4067.5. Based on this, a conservative threshold was set at 100, so that Base Flag statistically flags fragile rows without compromising overall coverage.

- **"profiling_anomalyflag" sheet**

  This profiling sheet was created to support the credibility of setting up anomaly detection later. To determine the behavior of appearance rates, so that the reported percentages are trustworthy and not misleading. It specifically analyzes the distribution of appearance rate across different thresholds.

  The table below summarizes key matrices:

  | Metric                 | Value          |
  | -----------------------|----------------|
  |Smallest non-zero rate  | 0.952095808    |
  |25th percentile         | 0.993447241    |
  |Median                  | 0.997006237    |
  |Count below 90%         | 0              |
  |Count below 95%         | 0              |
  |Count below 96%         | 1              |
  |Count below 98%         | 2              |
  |Count below 98.5%       | 3              |
  |Count below 98.6%       | 3              |
  |Count below 98.7%       | 4              |
  |Count below 98.8%       | 7              |
  |Count below 98.9%       | 9              |
  |Count below 99%         | 11             |
  |Blank fields            | 22             |
  |Non-empty rows          | 74             |
  |Total Counts            | 96             |

  This diagnostic step ensures that the anomaly flag is based on real data patterns rather than assumptions. Hence, set up a threshold for "LOW_RATE" classification.

  Formulas used above:

  - Smallest non-zero rate
  
    ```excel
    = MINIFS(Table1_14[Appearance Rate], Table1_14[Registered], ">0")

  - 25th percentile

    ```excel
    = PERCENTILE.INC(FILTER(Table1_14[Appearance Rate], Table1_14[Registered]>0), 0.25)

  - Median
  
    ```excel
    = MEDIAN(FILTER(Table1_14[Appearance Rate], Table1_14[Registered]>0))

  - Count below 90%
  
    ```excel
    = COUNTIFS(Table1_14[Appearance Rate], "<0.90")

  - Count below 95%
  
    ```excel
    = COUNTIFS(Table1_14[Appearance Rate], "<0.95")

  - Count below 96%
  
    ```excel
    = COUNTIFS(Table1_14[Appearance Rate], "<0.96")

  - Count below 98%
  
    ```excel
    = COUNTIFS(Table1_14[Appearance Rate], "<0.98")

  - Count below 98.5%
  
    ```excel
    = COUNTIFS(Table1_14[Appearance Rate], "<0.985")

  - Count below 98.6%
  
    ```excel
    = COUNTIFS(Table1_14[Appearance Rate], "<0.986")

  - Count below 98.7%
  
    ```excel
    = COUNTIFS(Table1_14[Appearance Rate], "<0.987")

  - Count below 98.8%
  
    ```excel
    = COUNTIFS(Table1_14[Appearance Rate], "<0.988")

  - Count below 98.9%
  
    ```excel
    = COUNTIFS(Table1_14[Appearance Rate], "<0.989")

  - Count below 99%
  
    ```excel
    = COUNTIFS(Table1_14[Appearance Rate], "<0.99")

  - Blank fields
  
    ```excel
    = COUNTBLANK(Table1_14[Appearance Rate])

  - Non-empty rows
  
    ```excel
    = COUNT(Table1_14[Appearance Rate])

  - Total Counts
  
    ```excel
    = Blank fields + Non-empty rows

  These results show that appearance rates are tightly clustered near perfection. Out of 74 non-empty rows, none fall below 95%, only 2 fall below 98%, and the 25th percentile is 99.34% with a median of 99.70%. Based on this, a conservative threshold was set at 98%, so that Anomaly Flag behaviorally flags rare underperformers without diluting overall credibility.

- **"profiling_volatility_schooltype Pivot table**

  A pivot table named 'volatility_pivot' was created from the 'fact_table' to profile the behavioral consistency of appearance across school types. This profiling was necessary because even when bases are large, appearance rates can fluctuate across regions, and such volatility affects credibility. Stable school types provide consistent evidence of performance.

  - Create a pivot table from fact_table.
  - Rows: School Type
  - Values: Appearance Rate -> Value Field Settings -> Summarize by Max (Max_AppearanceRate)
      Appearance Rate -> Value Field Settings -> Summarize by Min (Min_AppearanceRate)
  - Helper Column: Outside the pivot, compute:
  
    Volatility_Range_SchoolType
  
      ```excel
      = Max_AppearanceRate - Min_AppearanceRate

  Standard Deviation measures how spread out the values are from their average. For example, each school type appears in multiple regions. The pivot aggregates all those rows and computes the SD of appearance rates across all regions. It's good at detecting behavioral consistency.

  Range detacts extremes across regions. A higher range value indicates that the school type has inconsistent behavior across regions. Low range indicates that the school type is uniformly performing.

  Interpretation:
  - KV shows the most stable behavior (lowest StdDev and Range)
  - GOVT shows the most volatile behavior (highest StdDev and Range)
  - INDEPENDENT and GOVT AIDED shows moderate stability.
  - JNV is less consistent than INDEPENDENT and GOVT AIDED but more stable than CTSA and GOVT.
  - CTSA is slightly better than GOVT.

- **"Charts_volatility" sheet**

  Two visualizations were created using data from the profiling_volatility_schooltype pivot table:

  - Clustered Column Chart: [Volatility Column Chart](images/Volatility%20Index%20by%20School%20Type.png)

    A clustered column chart was created to compare StdDev and Range side by side for each school type, highlighting which school types are behaviorally consistent (low StdDev) and which show wide performance swings (high Range). From the chart, we can see that KV schools show the lowest volatility across both metrics, while GOVT schools show the highest.

  - Dual Axis Chart: [Volatility Dual Axis Chart](images/Volatility%20Index%20by%20School%20Type(Dual%20Axis).png)

    A dual-axis chart was created to plot Volatility_StdDev_SchoolType as bars and Volatility_Range_SchoolType as a line on a secondary axis. The chart highlights a clear view of consistency (StdDev) versus extremes (Range) in one visual. It is useful for identifying school types that are tightly clustered but still have outlier regions.

### Data Quality Checks

  1. Absence Flag: Present in dataset (school type not offered in some regions, for example, CTSA has quite a few absence flags).

  2. Error Flag: Implemented, but no cases found in this dataset. (dataset consistent).

  3. Missing Flag: Implemented, but no cases found in this dataset. (dataset complete).

  4. Failure Flag: Implemented, but no cases found (no region had registered students with zero appearance, at least ensuring strong monitoring and accountability across region and school type).

  5. Base Status: Computed in the table. (Flags whether a row has any registered students or not. It includes the Absence and Error Flag case. It simplifies filtering and profiling.)  Used in pivot to exclude absence rows.

  6. Pivot Filtering: Excluded "ABSENCE" base status to ensure threshold analysis.

  7. Base Flag: Identifies fragile or unstable appearance rates. Used for statistical stability and credibility. The Registered field is your denominator.
      "ABSENCE_BASE": No base

      "SMALL_BASE": Unstable rates due to small denominators

      "VALID_BASE": statistically stable rates
    We used Base Status for filtering and Base Flag for analytical annotation.
    Mainly, Base Flag is used to protect against fragile denominators. For example, in a school with only 10 registered students, even a single absence causes a big swing in the attendance rate. So, the base flag protects against these exaggerated fluctuations. Setting the threshold for a small base at 100 registered students, anything below it will be marked as unstable.

  8. Anomaly Flag: Identifies behavioral credibility issues. Even if the base is large, the appearance rates may be unusually low or suspiciously perfect for small bases. So, these flags mark rare behaviors.

      "ABSENT": Zero registered

      "LOW_RATE": Rare Underperformers

      "SUSPICIOUS_PERFECTION": Perfect but fragile

      "NORMAL": Valid or stable rates.
    Mainly, the Anomaly Flag is used to protect against misleading appearance rates. For example, even if a school type has thousands of registered students, the appearance rate may be unusually low compared to the overall distribution, or for a small number of registered students, the appearance rate may appear perfect. These behaviors may mislead analysis, so labeling them clearly can increase the credibility.
  
  9. Integrity Checks: To ensure the most basic and logical consistency of the dataset, three diagnostics columns were added to the fact_table:

      Integrity_RateCheck: Flags appearance rates outside the logical range (0-1).

      Integrity_RegisteredCheck: Flags cases where students appeared> registered students.

      Integrity_MissingCheck: Flags rows with missing or zero values in appeared or registered columns.


### Credibility Dashboard - CBSE Class 12 (2022): dashboard/Dashboard 1.png

This dashboard consolidates integrity checks, base flags, anomaly flags, and volatility profiling.

Slicer Used- Region and School Type.
  The two slicers dynamically filter the first three panels (Integrity, Base, Anomaly).
  The volatility panel is static and does not respond to slicers.

All slicer-connected charts are built from a unified Power Query Data Model.


