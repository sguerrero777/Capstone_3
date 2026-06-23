# EmporiUm Capstone 3 with Power BI: ETL & Semantic Modeling Documentation

**Project:** Capstone 3: Analysis with Power BI  
**Datasets:** `Capstone3_Sample_Sales.xlsx` and `book list.txt`
**Scope of Data:** 4-year sales data (2022–2025) 
**Region:** South
**Territories:** Florida, South Carolina, & Texas
**Author:** Sharleen Guerrero  

---

## 1. Project Overview

EmporiUm is a virtual student bookstore operating both in-store and online channels across multiple territories. This document records all data cleaning, transformation, and modeling decisions made during the ETL phase of the Power BI capstone project.

**Audience for this report:** Regional director, territory managers, and store managers.  
**Analysis goal:** Sales trends over time, performance by category, relative sales by state, and top-selling general books by author for region.

---

## 2. Data Sources

| Query Name | Source File | Sheet / Type | Role in Model |
|---|---|---|---|
| Store Sales | Capstone3_Sample_Sales.xlsx | [sheet name] | Fact table — in-store transactions |
| Online Sales | Capstone3_Sample_Sales.xlsx | [sheet name] | Fact table — online orders |
| Products | Capstone3_Sample_Sales.xlsx | [sheet name] | Dimension — product catalog |
| Inventory Categories | Capstone3_Sample_Sales.xlsx | [sheet name] | Dimension — category lookup |
| Store Locations | Capstone3_Sample_Sales.xlsx | [sheet name] | Dimension — store/state mapping |
| Management Team | Capstone3_Sample_Sales.xlsx | [sheet name] | Source for derived dimension tables |
| Shipper List | Capstone3_Sample_Sales.xlsx | [sheet name] | Reference — bonus challenge only |
| General Books Reference | book_list.txt | pipe-delimited text | Dimension — book titles and authors |

---

## 3. Data Cleaning Decisions

### 3.1 Store Sales

| Issue | Discovery Method | Resolution | Reasoning |
|---|---|---|---|
| 143 Error values in Category column | Column Quality profiler found 15% errors for this column based on entire dataset | Replaced errors with "Art Supplies" | Cross-referenced Product Number suffix for all errors with Inventory Categories table. Each error ended in "-A" Suffix, confirming match wirth Art Supplies Product Numbers |
| Column headers not promoted during import | Initial visual inspection | Promoted first row as headers | Source data had appropiately named headers that were not auto-detected |
| Sale Amount stored as a decimal with floating-point precision | Column type inspection | Changed data type to Fixed Deciaml number rounded to 2 decimal places | Eliminates hyper-precise floating-point numbers and ensures appropiate currency values for future measures and visualization |
| Join key column named Prod Num on import | Reviewing column names for naming conventions | Renamed column to "Product Number" | Normalized to match other tables and to meet naming conventions for clean relationship building |

### 3.2 Online Sales

| Issue | Discovery Method | Resolution | Reasoning |
| Date was split across Year, Month and Day columns| Initial visual inspection after import| Created a combined custom Date column using Date.From(#date([Year],[Month],[Day])) and dropped original three columns | A single date column per fact table is cleaner and required as the basis for time intelligence|
| New merged date column defaulted to DateTime data type | Column inspection after custom column was created | Changed data type from DateTime to Date | Only date type is required for clean relationship to furture Date Table|
| Join key column named Prod Num on import | Reviewing column names for naming conventions | Renamed column to "Product Number" | Normalized to match other tables and to meet naming conventions for clean relationship building |

### 3.3 Products

| Issue | Discovery Method | Resolution | Reasoning |
|---|---|---|---|
| Column headers not promoted during import | Initial visual inspection | Promoted first row as headers which automatically changed the columns to propert data types | Source data had named headers that were not auto-detected |
| Join key column named Prod Num on import | Reviewing column names for naming conventions | Renamed column to "Product Number" | Established as standard join key name across all tables |

### 3.4 Inventory Categories

| Issue | Discovery Method | Resolution | Reasoning |
|---|---|---|---|
| Column headers not promoted during import | Initial visual inspection | Promoted first row as headers, renamed columns to "Category" and "Description" and data types defaulted correctly | Source data had no headers, clear names improve readability in model |
| "Technology" in Inventory Categories didn't match "Technology & Accessories" in Products | Cross-table value comparison | Renamed to "Technology & Accessories" | Exact match required for possible text-based join key between tables |


### 3.5 Store Locations

| Issue | Discovery Method | Resolution | Reasoning |
| Column headers not promoted during import | Column headers not promoted during import | Promoted first row as headers | Source data had appropiately named headers that were not auto-detected |
| Some State values appeared as abbreviations (MA, ME, CT, NY, MD) instead of full names | Column Quality + value distribution review | Replaced abbreviations with full state names (Massachusetts, Maine, Connecticut, New York, Maryland) | Ensures consistency with other tables (Online Sales, Management Team) and prevents mismatched joins or inconsistent slicer values |
| Store ID column appeared at the end of the table | Personal preference for modeling clarity | Store ID column appeared at the end of the table | Personal preference for modeling clarity | Ensures consistency with other tables (Online Sales, Management Team) and prevents mismatched joins or inconsistent slicer values | Improves navigation and consistency across dimension tables (personal preference to have all keys at the beginning of table) |

### 3.6 Management Team

| Issue | Discovery Method | Resolution | Reasoning |
|---|---|---|---|
| [document issues found] | | | |

### 3.7 General Books Reference

| Issue | Discovery Method | Resolution | Reasoning |
|---|---|---|---|
| [document issues found] | | | |

**All remaining columns across all tables were reviewed and confirmed to have correct data types, no errors, no duplicate rows, and no additional cleaning requirements.**

---

## 4. Scope Filtering

**Assigned region:** [your region per the brief]  
**States in scope:** [list]  
**Filter applied:** [describe how you filtered fact tables to scope]  
**Rationale:** Loading only in-scope rows keeps the model accurate and performant.

---

## 5. Data Model Design

### 5.1 Star Schema Diagram

[embed schema_diagram.png here once built in Excalidraw]

### 5.2 Table Classifications

| Table | Role | Reasoning |
|---|---|---|
| Store Sales | Fact | Each row = one in-store transaction |
| Online Sales | Fact | Each row = one online order |
| DateTable | Dimension | One row per calendar date; built via CALENDAR() |
| Products | Dimension | One row per unique product |
| Store Locations | Dimension | One row per store with state mapping |
| [others] | | |

### 5.3 Relationships

| From (many side) | To (one side) | Join Column | Notes |
|---|---|---|---|
| Store Sales | DateTable | Date | |
| Online Sales | DateTable | Date | |
| Store Sales | Products | Prod Num | |
| Online Sales | Products | Prod Num | |
| Store Sales | Store Locations | Store ID | |
| [others] | | | |

### 5.4 Tables Left Unconnected

| Table | Reason |
|---|---|
| Shipper List | No join key in fact tables for core model; used for bonus challenge only |
| [others] | |

---

## 6. DAX Measures

| Measure Name | Formula | Purpose |
|---|---|---|
| Total Sales | `Total Sales = SUM('All Sales'[Sales])` | Primary revenue measure used across all visuals |
| [others] | | |
