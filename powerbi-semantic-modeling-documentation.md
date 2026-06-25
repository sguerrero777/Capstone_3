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

**Analysis goal:** Sales trends over time, performance by category, relative sales by state, and top-selling general books by author for region.

---

## 2. Data Sources

| Source File | Sheets / Queries | Contents |
|---|---|---|
| Capstone3_Sample_Sales.xlsx | Store Sales, Online Sales, Products, Inventory Categories, Store Locations, Shipper List | Transaction records, product catalog, store/state mapping, category lookup, and shipping reference |
| book_list.txt | General Books Reference | Book titles and authors (custom-delimited: *, . and "by") |

**Note:** Region Reference table was manually entered using Power BI's Enter Data feature, sourced from original Management Team sheet data. General Books Reference Author Name column was merged into Products table on Book Title = Product for use in top-sellers visual. Null author values were replaced with blanks.

---

## 3. Data Cleaning Decisions

### 3.1 Store Sales

| Issue | Discovery Method | Resolution | Reasoning |
|---|---|---|---|
| 143 Error values in Category column | Column Quality profiler found 15% errors for this column based on entire dataset | Replaced errors with "Art Supplies" | Cross-referenced Product Number suffix for all errors with Inventory Categories table. Each error ended in "-A" Suffix, confirming match wirth Art Supplies Product Numbers |
| Column headers not promoted during import | Initial visual inspection | Promoted first row as headers | Source data had appropiately named headers that were not auto-detected |
| Sale Amount stored as a decimal with floating-point precision | Column type inspection | Changed data type to Fixed Deciaml number rounded to 2 decimal places | Eliminates hyper-precise floating-point numbers and ensures appropiate currency values for future measures and visualization |
| Join key column named Prod Num on import | Reviewing column names for naming conventions | Renamed column to "Product Number" | Normalized to match other tables and to meet naming conventions for clean relationship building |
| "General" category name inconsistent with Inventory Categories | Cross-table value comparison during DAX measure writing | Replaced "General" with "Books (General)" using Replace Values | Ensures consistency with Inventory Categories table and enables accurate DAX filtering on Books (General) category |
| Store Location names needed for store-level visuals and KPIs | Modeling requirement identified during DAX planning | Merged Store Locations into Store Sales on Store ID using Left Outer join and expanded Store Location column only | Store Sales is later scoped to South region and merging Store Location directly into fact table enables store-level KPIs and slicers without extra tables or relationships |

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
| Author Name not available for top-sellers visual | Realized during DAX planning | Merged General Books Reference and Products queries joining Product on Book Title using Left Outer join and expanded Author Name column only | For Top Books and Top Author measures |

### 3.4 Inventory Categories

| Issue | Discovery Method | Resolution | Reasoning |
|---|---|---|---|
| Column headers not promoted during import | Initial visual inspection | Promoted first row as headers, renamed columns to "Category" and "Description" and data types defaulted correctly | Source data had no headers, clear names improve readability in model |
| "Technology" in Inventory Categories didn't match "Technology & Accessories" in Products | Cross-table value comparison | Renamed to "Technology & Accessories" | An exact match is necessary for possible text-based join key between tables |

### 3.5 Store Locations

| Issue | Discovery Method | Resolution | Reasoning |
|---|---|---|---|
| Column headers not promoted during import | Initial visual inspection | Promoted first row as headers | Source data had appropriately named headers that were not auto-detected |
| Some State values appeared as abbreviations (MA, ME, CT, NY, MD) instead of full names | Initial value distribution review | Replaced abbreviations with full state names (Massachusetts, Maine, Connecticut, New York, Maryland) | Ensures consistency with Online Sales and Management Team tables for clean joins and slicer values |
| Store ID column appeared at the end of the table | Personal preference for modeling clarity | Moved Store ID to first column | Improves navigation and consistency across dimension tables |

### 3.6 Region Reference

| Issue | Discovery Method | Resolution | Reasoning |
|---|---|---|---|
| Source table called "Management Team" had nulls in Region and Director due to Excel structure | Initial visual inspection | Built Region Reference table manually using Enter Data and deleted source table | Guaranteed zero nulls and no query dependencies |

### 3.7 Shipper List

| Issue | Discovery Method | Resolution | Reasoning |
|---|---|---|---|
| Column headers not promoted on import | Initial visual inspection | Promoted first row as headers | Data types correctly auto-identified after promotion |

### 3.8 General Books Reference

| Issue | Discovery Method | Resolution | Reasoning |
|---|---|---|---|
| Book titles and authors stored in single column in format "* Title * by Author" | Initial visual inspection | Split column three times using custom delimiters (*, ., and "by") to create Book Title and Author Name columns| Required two separate columns for join and display in top-sellers visual |
| Leading/trailing whitespace after splitting | After split column created | Applied Trim transformation to both columns | Prevents invisible whitespace from breaking joins or displaying incorrectly |
| Column names were generic after splitting | Initial visual inspection | Renamed to "Book Title" and "Author Name" | Clear names improve model readability |


**All remaining columns across all tables were reviewed and confirmed to have correct data types, no errors, no duplicate rows, and no additional cleaning required.**

---

### 4. Scope Filtering

- **Assigned region:** South
- **States in scope:** Texas, South Carolina, Florida
- **Filter applied:** Merged Store Locations on to Store Sales query using a Left Outer join on primary key Store ID. Expanded the new merged column to bring in Territory names only, filtering to South territories only.
- **Rationale:** Loading only in-scope rows keeps the model accurate to the assigned South region and prevents unrelated store transactions from inflating visuals and measures.

**Online Sales scope note:** Online Sales is classified as its own territory under the West regionand and is therefore excluded from South region analysis and not included in any South region measures.

---

## 5. Data Model Design

### 5.1 Date Table

| Table | Role | Reasoning |
|---|---|---|
| DateTable | Dimension | Created and marked a date table using CALENDAR(DATE(2022,1,1), DATE(2025,12,31)) covering entire data scope for time intelligence. Includes Year, Month Number, Month Name, Year-Month, Year-Month Number, Quarter, Weekday Name, and Weekday Number. Month Name sorted by Month Number for correct calendar order in visuals. Year-Month sorted by Year-Month Number for correct chronological order on x-axis. |

### 5.2 Table Classifications

| Table | Role | Reasoning |
|---|---|---|
| Store Sales | Fact | Each row is an event, one in-store transaction with date, product, and sale amount |
| Online Sales | Fact | Each row is an event, one online order. However, it is excluded from South region analysis because online sales belongs to West region |
| DateTable | Dimension | One row per calendar date; DAX-generated |
| Products | Dimension | One row per unique product with category and subcategory |
| Store Locations | Dimension | One row per store with city, state, and Store ID |
| Region Reference | Dimension | One row per state/territory with region, director, and manager |
| Inventory Categories | Dimension | One row per category with description |
| General Books Reference | Dimension | One row per book title with author name |
| Shipper List | Reference | Used for online sales |
| Report Measures | Measures Table | Contains all explicit DAX measures for easy visuals. No data columns, the placeholder column is hidden in report view. |

### 5.3 Main Relationships

| From (many side) | To (one side) | Join Column | Notes |
|---|---|---|---|
| Store Sales | DateTable | Transaction Date to Date | Single filter direction |
| Online Sales | DateTable | Date to Date | Single filter direction |
| Store Sales | Products | Product Number | Single filter direction |
| Online Sales | Products | Product Number | Single filter direction |
| Store Sales | Store Locations | Store ID | Single filter direction |
| Store Sales | Region Reference | Territory | Single filter direction |
| Store Sales | Inventory Categories | Category | Single filter direction |
| Products | General Books Reference | Product to Book Title | Single filter direction |

---

## 6. DAX Measures

All measures are stored in the **Report Measures** table. All measures referencing Total Region Sales are automatically scoped to the South region (Florida, South Carolina, Texas) because Store Sales is filtered to South region stores in Power Query.

| Measure Name | Formula | Purpose |
|---|---|---|
| Total Region Sales | `Total Region Sales = SUM('Store Sales'[Sale Amount])` | Primary revenue measure used across all visuals for South region only |
| Transaction Count | `Transaction Count = COUNTROWS('Store Sales')` | Total number of in-store transactions in South region |
| Average Transaction Value | `Average Transaction Value = DIVIDE([Total Region Sales],[Transaction Count])` | Average sale amount per transaction |
| Product Rank | `Product Rank = RANKX(ALL('Products'[Product Number]),[Total Region Sales])` | Ranks products by revenue |
| Total Stores | `Total Stores = DISTINCTCOUNT('Store Sales'[Store Location])` | Count of unique South region store locations |
| Top Territory | `Top Territory = CALCULATE(SELECTEDVALUE('Store Sales'[Territory]), TOPN(1, ALL('Store Sales'[Territory]), [Total Region Sales]))` | Returns name of highest-revenue territory |
| Prior Year Sales | `Prior Year Sales = CALCULATE([Total Region Sales], SAMEPERIODLASTYEAR('DateTable'[Date]))` | Same period prior year revenue for year-over-year comparison |
| Region YTD | `Region YTD = TOTALYTD([Total Region Sales], 'DateTable'[Date])` | Cumulative year-to-date revenue |
| General Books Sales | `General Books Sales = CALCULATE([Total Region Sales], 'Store Sales'[Category] = "Books (General)")` | Revenue from Books (General) category only |
| Books % of Total Revenue | `Books % of Total Revenue = DIVIDE([General Books Sales],[Total Region Sales])` | General books as a share of total South region revenue |
| Average Book Value | `Average Book Value = DIVIDE([General Books Sales], CALCULATE(COUNTROWS('Store Sales'), 'Store Sales'[Category] = "Books (General)"))` | Average transaction value for book sales |
| Top Book | `Top Book = CALCULATE(SELECTEDVALUE('Products'[Product]), TOPN(1, FILTER(ALL('Products'[Product], 'Products'[Author Name]), 'Products'[Author Name] <> BLANK()), [Total Region Sales], DESC))` | Title of the best-selling general book by sales |
| YoY Growth % | `YoY Growth % = DIVIDE([Total Region Sales] - [Prior Year Sales], [Prior Year Sales])` | Year-over-year revenue growth percentage |
| Best Quarter | `Best Quarter = CALCULATE(SELECTEDVALUE('DateTable'[Quarter]), TOPN(1, ALL('DateTable'[Quarter]), [Total Region Sales]))` | Quarter with highest total revenue |
| Highest Revenue Month | `Highest Revenue Month = CALCULATE(SELECTEDVALUE('DateTable'[Month Name]), TOPN(1, ALL('DateTable'[Month Name]), [Total Region Sales]))` | Month with highest total revenue |
