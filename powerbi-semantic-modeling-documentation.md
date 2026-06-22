# EmporiUm Capstone 3 with Power BI — ETL & Semantic Modeling Documentation

**Project:** Capstone 3: Analysis with Power BI  
**Datasets:** `Capstone3_Sample_Sales.xlsx` and 'book list.txt`
**Scope of Data:** 4-year sales data (2022–2025) 
**Region:** South
**Author:** Sharleen Guerrero  

---

## 1. Project Overview

EmporiUm is a virtual student bookstore operating both in-store and online channels across multiple states. This document records all data cleaning, transformation, and modeling decisions made during the ETL phase of the Power BI capstone project.

**Audience for this report:** Regional director, territory managers, and store managers.  
**Analysis goal:** Sales trends over time, performance by category, relative sales by state, and top-selling general books by author.

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
| Error values in Category column | Column Quality profiler — 14% errors | Replaced errors with "Art Supplies" | Cross-referenced Prod Num suffix (-A) against Inventory Categories and Products tables; confirmed match |
| [next issue] | | | |

### 3.2 Online Sales

| Issue | Discovery Method | Resolution | Reasoning |
|---|---|---|---|
| [document issues found] | | | |

### 3.3 Products

| Issue | Discovery Method | Resolution | Reasoning |
|---|---|---|---|
| [document issues found] | | | |

### 3.4 Store Locations

| Issue | Discovery Method | Resolution | Reasoning |
|---|---|---|---|
| [document issues found] | | | |

### 3.5 Management Team

| Issue | Discovery Method | Resolution | Reasoning |
|---|---|---|---|
| [document issues found] | | | |

### 3.6 General Books Reference

| Issue | Discovery Method | Resolution | Reasoning |
|---|---|---|---|
| [document issues found] | | | |

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
