
# **Power Query Transformation - CBSE Class XII 2022**

To reshape the raw CBSE Class 12 2022 dataset into a structured format suitable for credibility diagnostics. Power Query was used to clean, normalize, and compute foundational fields. Diagnostic flags were initially added in Excel for formula control. They have since been migrated into Power Query as custom columns to ensure reproducibility and transparency within the Data Model.

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

### **Appearance Rate**

    = if [#"Regd."] > 0 then [#"Appd."]/[#"Regd."] else null

- Renamed `Regd.` and `Appd.` to `Registered` and `Appeared`

### **No-show Rate**

    = if [Registered] > 0 then 1 - ([Appeared] / [Registered]) else null

### **Absence Flag**

    = if [Registered] = 0 and [Appeared] = 0 then "ABSENT" else null
      
### **Error Flag**

    = if [Registered] = 0 and [Appeared] > 0 then "DATA_ERROR" else null

### **Missing Flag**
  
    = if [Registered] = null or [Appeared] = null then "MISSING" else null
      
### **Failure Flag**

    = if [Registered] > 0 and [Appeared] = 0 then "FAIL" else null

## **Diagnostic Flag Migration**

To ensure reproducibility and element dependency on worksheet formulas, the following flags were migrated into Power Query:

- **BaseStatus**:

      = if [Registered] = 0 then "ABSENT" else "NON_ZERO"

- **BaseFlag**:

      = if [Registered] = 0 then "ABSENT_BASE" else if [Registered] < 100 then "SMALL_BASE" else "VALID_BASE"

- **AnomalyFlag**:

      = if [Registered] = 0 then "ABSENT" else if [Appearance Rate] < 0.98 then "LOW_RATE" else if [Appearance Rate] = 1 and [Registered] < 100 then "SUSPICIOUS_PERFECTION" else "NORMAL"

These columns are now part of the unified Data Model (`Table1_14 2`) and are used across all dashboard pivots and slicers.

## **Integrity Check Reshaping (Later Step)**

After diagnostic columns were added in Excel(`Integrity_RateCheck`, `Integrity_RegisteredCheck`, `Integrity_MissingCheck`), a separate Power Query step was used to:

- **Unpivot the three integrity check columns**

  - Resulting Fields:

    - `Integrity_CheckType`
    - `Integrity_CheckResult`
  
  - Row Expansion Notes:

    - Unpivoting the three integrity check columns expanded the row count from 96 to 288.
    - Each original row now contributes three records (one per check type).
    - This transformation enables consistent pivoting and flag aggregation across check types.

- **Load to Data Model**
  
  - Saved as **Connection only**.
  - Used for building the **Integrity Summary Pivot Table**.

## **Clean Output**

- Removed: Error Flag, Missing Flag, and Failure Flag(no cases found).

- Final query output renamed -> `class12-2022 result`. (Hidden later for convenience.)

- Loaded into Excel as the staging query (`fact_table`). This table is no longer used for pivot analysis. All dashboards now source from the unified Data Model.

## **Reproducibility Commitment**
  
- All transformations, flags, and metrices are now calculated within Power Query or DAX.
- No worksheet formulas are used in the analysis layer.
- This ensures that the pipeline is fully refreshable, auditable, and transparent.

## **Analytical Notes**

- Power Query handled the initial reshaping and foundational metrics (headed, unpivot/pivot, computed rates).
- Diagnostic flags (BaseStatus, BaseFlag, AnomalyFlag) were initially added in Excel for formula control, but have since been migrated into Power Query as custom columns.
- Integrity checks were unpivoted and loaded into the Data Model, expanding row count but enabling consitent pivoting across check types.
- Loading to the Data Model ensures unified slicer control across all dashboard panels, with both categorical flags (from Power Query).

## **Pipeline Context**

This Power Query transformation corresponds to the **Data Cleaning & Preprocessing stage** of the data analysis pipeline.

- **Data Cleaning & Preprocessing**  
  Raw dataset reshaped in Power Query: headers standardized, school types unpivoted, status pivoted, rates computed.

- **Feature Engineering**  
  Diagnostic flags (BaseStatus, BaseFlag, AnomalyFlag) migrated into Power Query as custom columns.  
  Integrity checks unpivoted into long format for consistent pivoting.  
  All flags now live in the Data Model, ensuring reproducibility.

- **Exploratory Analysis**  
  Distribution profiling (percentiles, medians, counts) implemented as DAX measures.  
  Pivot tables and slicers built from the unified Data Model.  
  Profiling sheets (base flag, anomaly flag, volatility) are slicer-aware and refreshable.

- **Modeling (future work)**  
  Statistical tests and advanced diagnostics to be added as extensions to the Data Model.

- **Communication**  
  Analytical README and dashboard storytelling.  
  Documentation in `/docs/` folder explains each transformation and design choice for transparency.
