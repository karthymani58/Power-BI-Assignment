# Power BI Assignment 1 – Data Transformation & Data Modeling

An end-to-end Power BI project analyzing e-commerce sales performance, focusing on data import, transformation via Power Query, relational data modeling, missing/duplicate data handling, and custom DAX calculations.

---

## 📌 Project Overview

This repository contains the complete implementation for **Power BI Assignment 1 (E-Commerce Sales Analysis)**. The goal of this assignment is to demonstrate best practices in Power BI for importing multi-source e-commerce data, applying business transformation rules, building an optimized star/snowflake schema model, and preparing data for analytical reporting.

---

## 📁 Source Datasets

The project processes three key CSV datasets:

1. **`List of Orders.csv`** — Contains transaction headers including Order ID, Order Date, Customer Name, State, and City.
2. **`Order Details.csv`** — Contains granular itemized order data including Order ID, Amount, Profit, Quantity, Category, and Sub-Category.
3. **`Sales target.csv`** — Contains monthly target benchmarks categorized by product category.

---

## 🛠️ Step-by-Step Implementation & Workflow

### 1. Data Import & Row Restructuring
* **Multi-Source Import**: Imported all three CSV files into Power BI Desktop via Power Query Editor.
* **Row Restriction**: Restricted the `List of Orders` table to the **first 500 rows** (`Table.FirstN(#"List of Orders", 500)`) as specified in assignment requirements.

### 2. Data Cleaning & Type Transformations
* **Date Parsing**: Explicitly cast the `Order Date` column in `List of Orders` to the `Date` data type.
* **Currency Formatting**: Updated `Amount` (in `Order Details`) and `Target` (in `Sales Target`) columns to **Fixed Decimal Number** (`Currency`) to ensure accurate financial aggregation.
* **Text Normalization**: Formatted `CustomerName` into Proper Case (`Text.Proper`) for consistent capitalization across all records.

### 3. Custom Columns & Enrichment
* **Merged Location**: Created a composite `Location` column in `List of Orders` combining `City` and `State` in the format:  
  `"City, State"` (e.g., `"Chennai, Tamil Nadu"`).
* **Profit Margin (%)**: Calculated a custom column for profit percentage:  
  $$	ext{Profit Margin} = rac{	ext{Profit}}{	ext{Amount}}$$
* **Profit Status (Conditional Column)**: Applied logic based on the `Profit` metric:
  * $	ext{Profit} < 0 
ightarrow$ `"Loss"`
  * $	ext{Profit} = 0 
ightarrow$ `"Break-Even"`
  * $	ext{Profit} > 0 
ightarrow$ `"Profit"`

### 4. Merging, Missing & Duplicate Data Management
* **Table Merge**: Merged `List of Orders` and `Order Details` on `Order ID` to generate a unified flat table named **`Orders Data`**.
* **Missing Data Strategy**: Checked for missing values across critical key fields (`Order ID`, `Category`, `Amount`). Nulls in descriptive attributes were replaced with `"Unknown"`, and incomplete rows were evaluated prior to table joins.
* **Duplicate Resolution**: Executed duplicate checks on unique key combinations (`Order ID` + `Category`) using Power Query's `Remove Duplicates` function.

### 5. Sorting, Aggregation & Grouping
* **Temporal Analysis**: Sorted orders by `Order Date` in **Descending Order** to highlight recent sales trends.
* **Regional Filtering**: Applied filter contexts (e.g., `State = "Tamil Nadu"`) for regional drill-down analysis.
* **Aggregated Details**:
  * Duplicated `Order Details` to compute count of Order IDs, average profit by Category, and total sales amount by Sub-Category.
  * Duplicated `Sales Target` to aggregate monthly sales target amounts.

---

## 📐 Data Modeling & Relationships

The relational data model in `Assignment1.pbix` was configured in Model View as follows:

```
+-------------------+             +--------------------+             +------------------+
|  List of Orders   |  1       *  |   Order Details    |  *       1  |   Sales Target   |
| ----------------- | <---------> | ------------------ | <---------- | ---------------- |
|  Order ID (PK)    |             |  Order ID (FK)     |             |  Category (PK)   |
|  Order Date       |             |  Category (FK)     |             |  Target Amount   |
|  Location         |             |  Amount / Profit   |             |  Month of Order  |
+-------------------+             +--------------------+             +------------------+
```

### Relationship Details:
| Left Table | Right Table | Foreign/Primary Key | Cardinality | Cross Filter Direction | Status |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `List of Orders` | `Order Details` | `Order ID` | 1-to-Many ($1:*$) | Single (`List of Orders` filters `Order Details`) | Active |
| `Sales Target` | `Order Details` | `Category` | 1-to-Many ($1:*$) | Single (`Sales Target` filters `Order Details`) | Active |

> **Note on Model Optimization**: The relationship between `Sales Target` and `Order Details` was configured to **Single Direction** (`Sales Target` $
ightarrow$ `Order Details`) to avoid ambiguous bi-directional filter propagation and maintain clean DAX measure calculations.

---


## 📋 Assignment Compliance Checklist

- [x] Restrict `List of Orders` to first 500 rows.
- [x] Set `Order Date` data type to `Date`.
- [x] Set `Amount` and `Target` to `Fixed Decimal Number`.
- [x] Format `CustomerName` to Proper Case.
- [x] Create merged `Location` column (`City, State`).
- [x] Create custom `Profit Margin` percentage column.
- [x] Add conditional `Profit Status` column (`Loss`, `Break-Even`, `Profit`).
- [x] Merge orders and order details into `Orders Data`.
- [x] Handle missing values and remove duplicate records.
- [x] Sort and filter data (Date descending, regional state filtering).
- [x] Create aggregated summary query tables.
- [x] Establish active $1:*$ relationships (`Order ID` & `Category`).
