CBSE Class XII 2022 Appearance Analysis

This project analyzes CBSE Class XII 2022 results across regions and school types.  
It goes beyond simple appearance rates to include:

- **Base-size flags** (credibility thresholds for Registered counts)
- **Anomaly detection** (low rates, suspicious perfection)
- **Coverage share** (school type dominance in regions)
- **Volatility index** (performance spread across school types)
- **Integrity checks** (absence vs missing vs failure vs error)

The goal is to build a transparent, annotated dashboard and dataset pipeline that withstands scrutiny and demonstrates analytical depth.

### Current progress:
- Raw dataset uploaded
- Power Query Transformation Completed
    - Loaded raw dataset into Power Query Editor
    - Promoted headers and set data types
    - Renamed 'Type' column to 'Region' (for clarity and consistency)
    - Unpivoted School Types from CTSA to KV, resulting in 'Attribute' and 'Value' columns.
    - Renamed 'Attribute' column to 'School Type'
    - Pivoted 'Status' into 'Regd.' and 'Appd.' columns.
    - Added computed column: Appearance Rate = if [#"Regd."] > 0 then [#"Appd."]/[#"Regd."] else null
    - Renamed 'Regd.' and 'Appd.' to 'Registered' and 'Appeared'
    - Added computed column: No-show Rate = if [Registered] > 0 then 1 - ([Appeared] / [Registered]) else null
    - Added computed column: Absence Flag = if [Registered] = 0 and [Appeared] = 0 then "ABSENT" else null
    - Added computed column: Error Flag = if [Registered] = 0 and [Appeared] > 0 then "DATA_ERROR" else null
    - Added computed column: Missing Flag = if [Registered] = null or [Appeared] = null then "MISSING" else null
    - Added computed column: Failure Flag = if [Registered] > 0 and [Appeared] = 0 then "FAIL" else null
    - Removed: Error Flag, Missing Flag, and Failure Flag. Since no cases were found in the dataset.
    - Cleaned query output is renamed 'class12-2022 result' worksheet in Excel.
  
### Fact Table Preparation
- Copied the result sheet 'class12-2022 result' into a new sheet named 'fact_table'.
- Added column Base Status, formula = IF([@Registered] = 0, "ABSENT", "NON_ZERO")

### Pivot Table Setup
A pivot table named 'minbase_pivot' was created from the 'fact_table' to profile the size of operating bases across regions and school types. This pivot was necessary because a 100% appearance rate for 2 students and a 100% appearance rate for 1000 students do not carry the same credibility. 

Small bases can produce fragile or misleading rates, while large bases provide stable evidence of performance. 

- Create a pivot table from 'fact-table'
- Rows: Region, School Type
- Values: Registered -> Value Field Settings -> Value Field = Min
- Filter: Base Status -> Dropped-down arrow -> Select only "NON-ZERO" to exclude ABSENT status.
- Slicer: Added for School Type to allow interactive exploration of patterns within each category. For example, CTSA and GOVT AIDED often show small bases, while KV and INDEPENDENT operate at large bases. 

  ### Data Quality Checks
- Absence Flag: Present in dataset (school type not offered in some regions, for example, CTSA has quite a few absence flags).
- Error Flag: Implemented, but no cases found in this dataset. (dataset consistent).
- Missing Flag: Implemented, but no cases found in this dataset. (dataset complete).
- Failure Flag: Implemented, but no cases found (no region had registered students with zero appearance, at least ensuring strong monitoring and accountability across region and school type).
- Base Status: Computed in the table. (Flags whether a row has any registered students or not. It includes the Absence and Error Flag case. It simplifies filtering and profiling.)
- Pivot Filtering: Excluded "NON-ZERO" base status to ensure threshold analysis. 
