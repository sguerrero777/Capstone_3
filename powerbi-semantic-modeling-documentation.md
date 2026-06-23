# EmporiUm Analysis with Power BI: ETL & Semantic Modeling Documentation

- **Project:** Capstone 3: Analysis with Power BI 
- **Datasets:** `Capstone3_Sample_Sales.xlsx` and `book list.txt`
- **Scope of Data:** 4-year sales data (January 1, 2022 – Decemebr 31, 2025)
- **Region:** South
- **Territories:** Florida, South Carolina, & Texas
- **Author:** Sharleen Guerrero  

---

## 1. Project Overview

EmporiUm is a virtual student bookstore operating both in-store and online channels across multiple territories. This document records all data cleaning, transformation, and modeling decisions made during the ETL phase of the Power BI capstone project.

**Audience for this report:** Regional director, territory managers, and store managers. 

**Analysis goal:** Sales trends over time, performance by category, relative sales by state, and top-selling general books by author for region.

---

## 2. Data Sources

| Query Name | Source File | Source Sheet / Type | Role in Model |
|---|---|---|---|
| Store Sales | Capstone3_Sample_Sales.xlsx | Store Sales | Fact table for in-store transactions |
| Online Sales | Capstone3_Sample_Sales.xlsx | Online Sales | Fact table for online orders |
| Products | Capstone3_Sample_Sales.xlsx | Products | Dimension table for product catalog |
| Inventory Categories | Capstone3_Sample_Sales.xlsx | Inventory Categories | Dimension table for category lookup |
| Store Locations | Capstone3_Sample_Sales.xlsx | Store Locations | Dimension table for store/state mapping |
| Region Reference | Manually entered | N/A | Dimension table for region, state, director, and territory manager lookup |
| Shipper List | Capstone3_Sample_Sales.xlsx | Shipper List | Reference for optional bonus challenge |
| General Books Reference | book_list.txt | custom-delimited text (*, . and "by") | Dimension table for General Books category products (book titles and authors) |

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
|---|---|---|---|
| Date split across Year, Month, Day columns | Initial visual inspection after import | Created a combined custom Date column using Date.From(#date([Year],[Month],[Day])) and dropped original three columns | A single date column per fact table is required for clean relationship to future Date Table and time intelligence |
| New merged date column defaulted to DateTime data type | Column inspection after custom column was created | Changed data type from DateTime to Date | Only Date type is required for clean relationship to future Date Table |
| Join key column named Prod Num on import | Reviewing column names for naming conventions | Renamed column to "Product Number" | Normalized to match other tables and to meet naming conventions for clean relationship building |
| Sales Total stored as decimal | Column type inspection | Changed to Fixed Decimal Number | Ensures accurate currency representation for measures and visuals |
| Date column positioned last after merge | Visual inspection | Reordered columns to place Date first | Readability and consistency with Store Sales structure |

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
|---|---|---|---|
| Column headers not promoted during import | Initial visual inspection | Promoted first row as headers | Source data had appropriately named headers that were not auto-detected |
| Some State values appeared as abbreviations (MA, ME, CT, NY, MD) instead of full names | Value distribution review | Replaced abbreviations with full state names (Massachusetts, Maine, Connecticut, New York, Maryland) | Ensures consistency with Online Sales and Management Team tables for clean joins and slicer values |
| Store ID column appeared at the end of the table | Personal preference for modeling clarity | Moved Store ID to first column | Improves navigation and consistency across dimension tables |

### 3.6 Region Reference

| Issue | Discovery Method | Resolution | Reasoning |
|---|---|---|---|
| Source table called "Management Team" had nulls in Region and Director due to Excel structure | Initial visual inspection | Built standalone Region Reference table manually using Enter Data and deleted source table | Fill Down and merge approaches failed due to ungrouped source data; manual entry guaranteed zero nulls and no query dependencies |


### 3.7 Shipper List

| Issue | Discovery Method | Resolution | Reasoning |
|---|---|---|---|
| Column headers not promoted on import | Visual inspection | Promoted first row as headers | Data types correctly auto-identified after promotion |

### 3.8 General Books Reference

| Issue | Discovery Method | Resolution | Reasoning |
|---|---|---|---|
| Issue | Discovery Method | Resolution | Reasoning |
|---|---|---|---|
| Book titles and authors stored in single column in format "* Title * by Author" | Visual inspection of raw text file | Split column three times using custom delimiters (*, ., and "by") to isolate Book Title and Author Name | Required two separate columns for join and display in top-sellers visual |
| Leading/trailing whitespace after splitting | Post-split inspection | Applied Trim transformation to both columns | Prevents invisible whitespace from breaking joins or displaying incorrectly |
| Column names were generic after splitting | Initial visual inspection | Renamed to "Book Title" and "Author Name" | Clear names improve model readability |

**All remaining columns across all tables were reviewed and confirmed to have correct data types, no errors, no duplicate rows, and no additional cleaning requirements.**

---

## 4. Scope Filtering

**Assigned region:** South
**States in scope:** Texas, South Carolina, Florida
**Filter applied:** Merged Store Locations on to Store Sales query using a Left Outer join on primary key Store ID. Expanded the new merged column to bring in Territory names only, filtering to South territories only.
**Rationale:** Loading only in-scope rows keeps the model accurate to the assigned South region  and prevents unrelated store transactions from inflating visuals and measures.
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
