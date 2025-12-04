
# **Power Query Transformation - CBSE Class XII 2022**

To reshape the raw CBSE Class 12 2022 dataset into a structured format suitable for credibility diagnostics. Power Query was used to clean, normalize, and compute foundational fields. All the diagnostics flags and integreted checks were added later in Excel.

## **Transformation Steps**

- **Load raw dataset into Power Query Editor**
  - Imported `class12-2022 raw_dataset.csv`
  - Promoted headers and set data types.
  - Renamed `Type` column -> `Region` for clarity and consistency.

- **Unpivot School Type**
  - Selected columns from `CSTA` to `KV`.
  - Applied **Unpivot Columns**, resulting in:
    - `Attribute` -> renamed to `School Type`.
    - `Value` -> holds registered/appeared counts.

- **Pivot Status**
  - Pivoted `Status` column into two fields
    - `Regd.` -> later renamed `Registered`.
    - `Appd.` -> later named `Appeared`.

## **Computed Columns**

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

## **Clean Output**

- Removed: Error Flag, Missing Flag, and Failure Flag(no cases found).

- Final query output renamed -> `class12-2022 result`. (Hidded later for convenience.)

- Loaded into Excel as the **fact_table** for downstream diagnostics.

## **Integrity Check Reshaping (Later Step)**

After diagnostic columns were added in Excel(`Integrity_RateCheck`, `Integrity_RegisteredCheck`, `Integrity_MissingCheck`), a separate Power Query step was used to:

- **Unpivot the three integrity check columns**

  - Resulting Fields:

    - `Integrity_CheckType`
    - `Integrity_CheckResult`

- **Load to Data Model**
  
  - Saved as **Connection only**.
  - Used for building the **Integrity Summary Pivot Table**.

## **Analytical Notes**

- Power Query handled the initial reshaping and foundational metrics.
- All diagnostics flags were added in Excel for transparency and formula control.
- Loading to Data Model ensured unified slicer control across dashboard panels.

## **Pipeline Context**

This Power Query transformation corresponds to the **Data Cleaning & Preprocessing stage of the data analysis pipeline.

- Raw dataset -> cleaned, normalized table.
- Headers, data types, and column names standarized.
- School types unpivoted, status pivoted.
- Appearance Rate and No-show Rate computed.
- Invalid flags removed.

Subsequent stages will be built on this:

- **Feature Engineering** -> diagnostic flags in fact_table.
- **Exploratory Analysis** -> pivots and profiling sheets.
- **Modeling** -> statistical tests(future work).
- **Communication** -> analytical README and dashboard storytelling.
