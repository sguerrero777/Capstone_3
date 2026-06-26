# Capstone 3: EmporiUm South Region Sales Analysis with Power BI

## Overview and Objectives
EmporiUm is a virtual student bookstore operating both in-store and online channels 
across multiple territories. I was assigned to conduct a comprehensive analysis of store sales and product insights for the South Region across four fiscal years. This report highlights key revenue trends, key influencers, category performance, territory comparisons, and top-selling general books, all designed to support data-driven decision making for territory managers and store managers at the regional level.

## Sales Territory Details
- **Assigned Region:** South
- **Region Director:** Andy Gisselquist
- **Region Territories:** Florida, South Carolina, Texas
- **Territory Store Count:** 24 stores (FL: 11, TX: 11, SC: 2)
- **Scope of Analysis:** In-Store Sales | January 1, 2022 – December 31, 2025

## Executive Summary
| Metric | Value |
|---|---|
| Total Region Sales | $8.00M (4 calendar years) |
| Total Transactions | 57,300 |
| Total Stores | 24 (FL: 11, TX: 11, SC: 2) |
| Top Territory | Florida — $3.93M (49.15%) |
| Top Store | Beaumont, TX — $637,819 |
| Top Category | Technology & Accessories — $5.68M (71% of revenue) |
| Top General Book | Harry Potter and the Sorcerer's Stone — J.K. Rowling ($4,366, 151 transactions) |
| Best Quarter | Q4 — $2.18M (combined across all years) |
| Peak Month | October — $819K (combined across all years) |
| Largest YoY Growth 2025 | +58% ($1.88M → $2.96M) |
| Revenue Trend | Overall +122% growth from 2022 to 2025. 2024 flat (+0.3%), 2025 surged (+58%) |

## Table of Contents
- `Guerrero_Capstone3.pbix` — Final Power BI report with all visuals, 
data model, DAX measures, florida bookmark, and mobile layout for Sales Performance Overview page
- `data-imports/` — Folder containing aource data files:
  - `Capstone3_Sample_Sales.xlsx` — Main dataset (7 sheets)
  - `book list.txt` — General books reference (90 titles, custom-delimited)
- `etl-and-modeling/` — Folder containing step-by-step .pbix saves showing ETL and modeling 
progress
- `presentations/` — Folder containing PowerPoint slide decks for recorded and live presentations
- `powerbi-semantic-modeling-documentation.md` — Full ETL documentation 
including cleaning decisions, modeling reasoning, and DAX measure definition
  
## Tools Used
- **Power BI Desktop** — data cleaning, modeling, DAX measures, and report visualization
- **Power Query (M)** — ETL transformations across 8 tables
- **DAX** — 16 explicit measures including time intelligence and ranking
- **Git & GitHub** — version control and project management
- **PowerPoint** — presentation and recording

## Power BI Report
Published to the Year Up United class workspace in Power BI Service.

**Live Report:** [EmporiUm South Region Sales Analysis](https://app.powerbi.com/links/NfKbc1Sd9f?ctid=bbce5c37-f181-4d0c-9310-7f877336e1cf&pbi_source=linkShare)

The report consists of three pages:
- **Page 1 — Sales Performance Overview:** Line chart (fiscal trend), pie chart 
(revenue by territory), bar chart (revenue by category), waterfall chart (2025 revenue 
surge by category), KPI cards, and interactive slicers for Year, Territory, and Store Location
- **Page 2 — Product & Book Insights:** Top-selling general books table with author names, 
book sales trend line chart, decomposition tree, and KPI cards for Books (General) category
- **Page 3 — Executive Summary:** Territory revenue by year bar chart, Key Influencers 
visual, KPI cards (YoY Growth, Best Quarter, Highest Revenue Month, Region YTD), 
and strategic recommendations text box
- **You may need to request access**

## Video Demo
[Link to be added after recording]

## Author
Sharleen Guerrero | Year Up United Data Analytics Academy
