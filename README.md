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

### Data Quality Checks
- Absence Flag: Present in dataset (school type not offered in some regions, for example, CTSA has quite a few absence flags.).
- Error Flag: Implemented, but no cases found in this dataset. (dataset consistent).
- Missing Flag: Implemented, but no cases found in this dataset. (dataset complete).
- Failure Flag: Implemented, but no cases found (no region had registered students with zero appearance, at least ensuring strong monitoring and accountability across region and school type).
- 
