# Power Query and Data Integration - Complete Deep Dive
## PL-200 Exam Preparation

---

## Table of Contents
1. [What is Power Query?](#what-is-power-query)
2. [Power Query vs Other Data Integration Methods](#power-query-vs-other-data-integration-methods)
3. [Connectors Deep Dive](#connectors-deep-dive)
4. [Query Transformations](#query-transformations)
5. [Power Query Functions and Expressions](#power-query-functions-and-expressions)
6. [Data Refresh Strategies](#data-refresh-strategies)
7. [Dataflows](#dataflows)
8. [Power Query in Power Automate](#power-query-in-power-automate)
9. [Query Folding and Performance](#query-folding-and-performance)
10. [Error Handling in Data Transformations](#error-handling-in-data-transformations)
11. [Data Integration Scenarios](#data-integration-scenarios)
12. [Common Exam Scenarios](#common-exam-scenarios)

---

## What is Power Query?

### The Data Transformation Engine

Power Query is the **ETL (Extract, Transform, Load)** component of the Microsoft Power Platform:

```
┌─────────────────────────────────────────────────────────────┐
│                   POWER QUERY PROCESS                        │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌────────────────┐      ┌────────────────┐                │
│  │    EXTRACT     │      │   TRANSFORM    │                │
│  │                │  →   │                │  →             │
│  │ Connect to     │      │ Clean, filter, │                │
│  │ data sources   │      │ aggregate,     │                │
│  │ (400+ options) │      │ merge, append  │                │
│  └────────────────┘      └────────────────┘                │
│                                │                             │
│                                ▼                             │
│                         ┌────────────────┐                │
│                         │     LOAD       │                │
│                         │                │                │
│                         │ Push to:       │                │
│                         │ • Dataverse    │                │
│                         │ • Power BI     │                │
│                         │ • Excel        │                │
│                         │ • Analytics    │                │
│                         └────────────────┘                │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Key Characteristics

| Feature | Description |
|---------|-------------|
| **Visual Designer** | Drag-and-drop transformations |
| **Formula Language** | M language for advanced transformations |
| **Connectors** | 400+ data sources |
| **Transformations** | Filter, sort, merge, pivot, unpivot, etc. |
| **Query Folding** | Optimized queries push to source database |
| **Refresh Options** | Scheduled, on-demand, incremental |

### When to Use Power Query

| Scenario | Use Power Query | Why |
|----------|-----------------|-----|
| Import Excel to Dataverse | ✓ Yes | Easy transformations |
| Real-time Dataverse sync | ✗ No | Use Power Automate instead |
| CSV/Excel data cleanup | ✓ Yes | Excel connector + transformations |
| Database import with transforms | ✓ Yes | Query folding for performance |
| Quick API data fetch | ✗ Maybe | Power Automate if simple |
| Complex data mapping | ✓ Yes | Merge/Append capabilities |

---

## Power Query vs Other Data Integration Methods

### Power Query vs Power Automate vs Plugins

```
┌───────────────────────────────────────────────────────────────────┐
│            DATA INTEGRATION METHODS COMPARISON                     │
├───────────────────────────────────────────────────────────────────┤
│                                                                    │
│ POWER QUERY                                                        │
│ ├─ Best for: Batch ETL, scheduled imports                        │
│ ├─ Data size: Large datasets (millions of rows)                  │
│ ├─ Frequency: Scheduled or on-demand                             │
│ ├─ Complexity: Medium (visual + M formulas)                      │
│ └─ Cost: Part of Power Platform / Excel                          │
│                                                                    │
│ POWER AUTOMATE                                                     │
│ ├─ Best for: Real-time workflows, approvals                      │
│ ├─ Data size: Small to medium (thousands of rows)                │
│ ├─ Frequency: Event-driven or scheduled                          │
│ ├─ Complexity: Low (visual only, no code)                        │
│ └─ Cost: Per-flow licensing                                       │
│                                                                    │
│ PLUGINS / WEBHOOKS                                                │
│ ├─ Best for: Synchronous server-side logic                       │
│ ├─ Data size: Small payloads (2MB limit)                         │
│ ├─ Frequency: Real-time (on record create/update)                │
│ ├─ Complexity: High (requires .NET development)                  │
│ └─ Cost: Development + deployment overhead                        │
│                                                                    │
└───────────────────────────────────────────────────────────────────┘
```

### Decision Matrix

| Requirement | Solution |
|-------------|----------|
| Import 100K CSV rows into Dataverse | **Power Query** (batch) |
| Sync new Dataverse records to API in real-time | **Power Automate** (trigger) |
| Transform data from 3 sources into 1 table | **Power Query** (merge/append) |
| Complex calculation on every record save | **Plugin** (synchronous) |
| Send email when record is created | **Power Automate** (simple trigger) |
| Incremental sync daily | **Power Query** (dataflow) |

---

## Connectors Deep Dive

### Connector Categories

#### 1. **Cloud Data Sources**

| Connector | Type | Best For |
|-----------|------|----------|
| **SharePoint** | Cloud | Importing lists, documents |
| **OneDrive** | Cloud | Excel files, documents |
| **Google Sheets** | Cloud | Cross-platform collaboration |
| **Salesforce** | Cloud | CRM data import |
| **Dynamics 365** | Cloud | D365 data extraction |

#### 2. **Database Connectors**

| Connector | Type | Query Folding |
|-----------|------|---------------|
| **SQL Server** | On-premises | ✓ Full support |
| **Oracle** | On-premises | ✓ Full support |
| **PostgreSQL** | On-premises | ✓ Full support |
| **MySQL** | On-premises | ✓ Full support |

**Gateway Requirement**: On-premises databases require **Data Gateway** for connection.

#### 3. **Text File Connectors**

| Format | Connector | Use Case |
|--------|-----------|----------|
| **CSV** | Text/CSV | Bulk imports |
| **Excel** | Excel Workbook | Spreadsheet data |
| **JSON** | Web | API responses |
| **XML** | Web | Structured data |

#### 4. **API Connectors**

- **HTTP Request**: Generic REST APIs
- **Custom Connectors**: Build your own connector
- **Web Scraping**: Limited support

### Connection Credentials

**Important Security Considerations:**
- Credentials stored securely in Microsoft cloud
- Shared connections appear in "Shared data sources"
- Connection strings should NEVER include plaintext passwords
- Use **Connection References** in solutions for portability

```
Connection Reference in Solution:
├─ Stores connection configuration
├─ Allows reconfiguration in target environment
└─ No need to re-enter credentials after import
```

---

## Query Transformations

### Common Transformations

#### 1. Filter Rows

```
Input: 1000 rows (all statuses)
  ↓
Filter: Status = "Active"
  ↓
Output: 500 rows (Active only)
```

**When NOT to filter:**
- Filtering is non-foldable for that source
- You need all data later in the query

#### 2. Select Columns

**Remove unnecessary columns:**
```
Input: 20 columns
  ↓
Select columns: Name, Email, Status, Created
  ↓
Output: 4 columns (reduced payload)
```

**Benefits:**
- Reduces data transfer
- Improves performance
- Makes queries more readable

#### 3. Rename Columns

```
Old Names              →  New Names
- FirstName           →  - First Name
- AccountNumber       →  - Account #
- CreatedDateTime     →  - Created On
```

**Naming Best Practices:**
- Match Dataverse column display names
- Use spaces for readability
- Avoid special characters

#### 4. Sort Data

```
Sort by: Created Date (Descending)
  ↓
Newest records first
```

#### 5. Remove Duplicates

```
Input: 1000 rows (including 200 duplicates)
  ↓
Remove Duplicates: Based on [Account ID, Email]
  ↓
Output: 800 rows (unique records)
```

#### 6. Append Queries

Combine data from multiple sources:

```
Query 1: Customers from Database
  ├─ Name, Email, Status
  └─ 500 rows

Query 2: Customers from Excel
  ├─ Name, Email, Status
  └─ 200 rows

Append: Combine both
  └─ 700 rows (combined)
```

**Matching Requirements:**
- Column names MUST match exactly
- Data types should be compatible

#### 7. Merge Queries

Join data from multiple tables:

```
Customers Table                 Orders Table
├─ CustomerID                   ├─ OrderID
├─ Name                         ├─ CustomerID
└─ Email                        ├─ Amount
                                └─ Date

Merge: Inner Join on CustomerID
  └─ Result: Customers with their orders
```

**Join Types:**
- **Inner Join**: Only matching records
- **Left Outer**: All from left table + matches
- **Right Outer**: All from right table + matches
- **Full Outer**: All records from both tables
- **Left Anti**: Records from left NOT in right
- **Right Anti**: Records from right NOT in left

#### 8. Pivot/Unpivot

**Pivot (Wide format):**
```
Before:          After:
Month  Sales     2024-01  2024-02  2024-03
01     100       100      200      150
02     200
03     150
```

**Unpivot (Long format):**
```
Before:          After:
Product  Q1  Q2  Product  Quarter  Amount
A        10  20  A        Q1       10
B        30  40  A        Q2       20
                 B        Q1       30
                 B        Q2       40
```

#### 9. Fill Down / Fill Up

Propagate values down or up:

```
Input:           Fill Down:
Name    Status   Name    Status
John    Active   John    Active
        Active   John    Active
        Active   John    Active
```

#### 10. Replace Values

Simple find and replace:

```
Find: "N/A"
Replace: "(blank)"
```

---

## Power Query Functions and Expressions

### M Language Basics

Power Query uses the **M language** for advanced transformations:

```m
// Function syntax
let
    Source = Excel.Workbook(File.Contents("C:\data.xlsx")),
    FirstSheet = Source{0}[Data],
    FilteredRows = Table.SelectRows(FirstSheet, each [Status] = "Active"),
    RenamedCols = Table.RenameColumns(FilteredRows, {{"Col1", "Name"}, {"Col2", "Email"}})
in
    RenamedCols
```

### Common M Functions

| Function | Purpose | Example |
|----------|---------|---------|
| `Table.SelectRows()` | Filter rows | `Table.SelectRows(table, each [Age] > 18)` |
| `Table.SelectColumns()` | Select specific columns | `Table.SelectColumns(table, {"Name", "Email"})` |
| `Table.AddColumn()` | Add calculated column | `Table.AddColumn(table, "Full Name", each [First] & " " & [Last])` |
| `Table.TransformColumnTypes()` | Change data types | `Table.TransformColumnTypes(table, {{"Age", Int64.Type}})` |
| `Text.Upper()` | Uppercase text | `Text.Upper([Name])` |
| `Text.Trim()` | Remove spaces | `Text.Trim([Name])` |
| `Date.FromText()` | Convert to date | `Date.FromText([DateText])` |
| `Number.FromText()` | Convert to number | `Number.FromText([AmountText])` |

### Custom Columns with M Expressions

```
New Column: Full Name
Expression: [First Name] & " " & [Last Name]

New Column: Status Flag
Expression: if [Status] = "Active" then "OK" else "CHECK"

New Column: Age Group
Expression:
    if [Age] < 18 then "Minor"
    else if [Age] < 65 then "Adult"
    else "Senior"
```

---

## Data Refresh Strategies

### Refresh Types

#### 1. **Full Refresh**

Reloads ALL data from source:

```
Source Table: 100K rows
  ↓
Delete all rows in destination
  ↓
Reload all 100K rows
  ↓
Update complete
```

**Use Cases:**
- Initial data load
- Data source completely changed
- When incremental doesn't work

**Time**: Longer (minutes to hours for large datasets)

#### 2. **Incremental Refresh**

Only loads NEW or CHANGED data:

```
Source Table: New 1K rows since last refresh
  ↓
Check "Created" column for rows > [Last Refresh Date]
  ↓
Load only new 1K rows
  ↓
Append to existing data
```

**Requirements:**
- Must have a "Created Date" or "Modified Date" column
- Source must support filtering

**Time**: Much faster (minutes)

**Example Setup:**
```
Filter: [Modified Date] > [Previous Refresh Date]
Store: Previous Refresh Date as parameter
Update: Parameter after each refresh
```

### Refresh Schedules

#### Cloud Refresh (Power BI / Dataflows)

- **Frequency**: Up to 8 times per day
- **Duration**: Usually 15 minutes - 2 hours
- **Alerts**: Can trigger Power Automate if refresh fails

#### Excel Refresh

- **Frequency**: Manual or limited scheduling
- **Duration**: Depends on file size and source

### Handling Refresh Failures

```
Refresh Failure
  ↓
Common Causes:
  ├─ Gateway offline (on-premises databases)
  ├─ Source system unavailable
  ├─ Credentials expired
  ├─ Data source format changed
  └─ Query timeout (too much data)

Recovery:
  ├─ Check gateway status
  ├─ Verify credentials
  ├─ Optimize query (add filters, smaller date ranges)
  └─ Review data source for changes
```

---

## Dataflows

### What are Dataflows?

Dataflows are **reusable Power Query transformations** stored in the cloud:

```
┌─────────────────────────────────────────────────────────────┐
│                      DATAFLOW ARCHITECTURE                   │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  Data Source (Excel, SQL, API)                             │
│         ↓                                                    │
│  ┌──────────────────────────────────┐                      │
│  │  Dataflow (Cloud-based)          │                      │
│  │  ├─ Extract                      │                      │
│  │  ├─ Transform (Power Query)       │                      │
│  │  └─ Load to Dataverse            │                      │
│  └──────────────────────────────────┘                      │
│         ↓                                                    │
│  Dataverse (Destination)                                    │
│         ↓                                                    │
│  Used by:                                                    │
│  ├─ Power Apps                                              │
│  ├─ Power Automate                                          │
│  ├─ Power BI                                                │
│  └─ Other dataflows                                         │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Benefits of Dataflows

| Benefit | Explanation |
|---------|-------------|
| **Reusability** | Create once, use in multiple apps/reports |
| **Cloud-based** | No local refresh required |
| **Scheduled Refresh** | Automatic updates on schedule |
| **Incremental Load** | Only new/changed data |
| **Linked Entities** | Share data between dataflows |

### Creating Dataflows

**Steps:**
1. Power BI Workspace > "Create" > "Dataflow"
2. Add data source (Excel, SQL, SharePoint, etc.)
3. Transform data using Power Query
4. Load to Dataverse table (or Power BI)
5. Schedule refresh

### Dataflows vs Direct Connection

| Aspect | Dataflow | Direct Connection |
|--------|----------|-------------------|
| **Data Storage** | Cached in Dataverse | Real-time from source |
| **Performance** | Faster (local data) | Slower (network calls) |
| **Refresh** | Scheduled | On every query |
| **Size Limits** | Dataverse storage quota | Source limits |
| **Cost** | Storage capacity | Connector calls |

---

## Power Query in Power Automate

### When Power Query is Used in Power Automate

Power Query transformations can be applied within Power Automate flows:

```
Flow: Import and Transform CSV
├─ Trigger: File created in SharePoint
├─ Action: Read file
├─ Apply Power Query transformations
│   ├─ Filter inactive records
│   ├─ Remove duplicates
│   ├─ Add calculated columns
│   └─ Rename columns
├─ Action: Create records in Dataverse (ForEach)
└─ End
```

### Limitations in Power Automate

- No visual Power Query editor
- Use expressions for transformations
- Less efficient than dedicated dataflows
- Better for small datasets or simple transforms

---

## Query Folding and Performance

### What is Query Folding?

**Query Folding** = Pushing transformations to the data source (server-side).

```
WITHOUT Query Folding (Client-side):
  ├─ SQL Server retrieves ALL 1M rows
  ├─ Power Query filters 1M rows locally
  └─ Returns 50K active rows

WITH Query Folding (Server-side):
  ├─ SQL Server filters: WHERE Status = 'Active'
  ├─ Returns 50K active rows only
  └─ Much faster! (50K vs 1M rows transferred)
```

### Query Folding Support

| Source | Supports | Notes |
|--------|----------|-------|
| **SQL Server** | ✓ Full | All standard transformations |
| **Oracle** | ✓ Full | Excellent performance |
| **SharePoint** | ⚠ Partial | Filters work, complex transforms don't |
| **Excel** | ✗ None | All transforms client-side |
| **CSV** | ✗ None | All transforms client-side |
| **JSON** | ✗ Partial | Limited to some transforms |

### Non-Foldable Transformations

These cannot be folded to server:

```
❌ Text.Upper() - custom functions
❌ Text.Trim() - text operations
❌ DateTime.Time() - time extraction
❌ Custom M functions - user-defined logic
❌ Combines with foldable operations - breaks folding
```

### Checking Query Folding

**In Power BI / Power Query:**
1. Right-click query step
2. "View Native Query"
3. If shows SQL: Folding works ✓
4. If blank: Folding doesn't work ✗

---

## Error Handling in Data Transformations

### Common Data Quality Issues

```
Source Data Problems:
├─ Null/Blank values
├─ Duplicate records
├─ Inconsistent formats (YYYY-MM-DD vs MM/DD/YYYY)
├─ Extra spaces (  John  vs John)
├─ Mixed data types ("100" text vs 100 number)
├─ Out-of-range values
└─ Invalid characters
```

### Handling Solutions

#### Null/Blank Values

```m
// Replace blanks with default
= Table.ReplaceValue(data, null, "Unknown", Replacer.ReplaceValue, {"Name"})

// Or in GUI:
// Replace Value: null → "Unknown"
```

#### Inconsistent Formatting

```m
// Standardize dates
= Table.TransformColumns(data, {{"Date", each Date.FromText(_)}})

// Trim whitespace
= Table.TransformColumns(data, {{"Name", each Text.Trim(_)}})

// Uppercase all text
= Table.TransformColumns(data, {{"Status", each Text.Upper(_)}})
```

#### Error Rows

```m
// Skip rows with errors
= Table.SelectRows(data, each [Error] = null)

// Or create error log
ErrorLog = Table.SelectRows(data, each [Error] <> null)
```

---

## Data Integration Scenarios

### Scenario 1: Consolidate Multiple Excel Files

```
Input:
├─ Sales_2024-Q1.xlsx
├─ Sales_2024-Q2.xlsx
├─ Sales_2024-Q3.xlsx
└─ Sales_2024-Q4.xlsx

Steps:
1. Load each file separately
2. Append all queries
3. Add column: Quarter (extracted from filename)
4. Remove duplicates
5. Load to Dataverse
```

### Scenario 2: Sync Database to Dataverse

```
Input: SQL Server with 500K customer records

Steps:
1. Filter: Status = "Active" (100K)
2. Select required columns (10 of 50)
3. Rename columns to match Dataverse
4. Transform data types
5. Incremental refresh daily
6. Load to Dataverse table

Result: Dataverse table stays in sync with database
```

### Scenario 3: Data Enrichment from Multiple Sources

```
Input:
├─ Customers from Salesforce
├─ Orders from SQL Server
└─ Contacts from SharePoint

Steps:
1. Load Customers (100K)
2. Merge with Orders (join on Customer ID)
3. Group by: Count orders per customer
4. Add column: Last Order Date
5. Merge with Contacts (additional info)
6. Load to Dataverse enriched table
```

---

## Common Exam Scenarios

### Scenario 1: Large Dataset Import Performance

**Question**: "Importing 2M customer records from SQL Server takes 3 hours. How to optimize?"

**Answer:**
- Check query folding: Ensure filters pushed to SQL
- Add date filter: Only import last 30 days
- Remove unnecessary columns early in query
- Increase refresh parallelism
- Consider splitting into multiple dataflows by region

### Scenario 2: Inconsistent Data Format

**Question**: "Phone numbers are in multiple formats: 555-1234, (555) 1234, 5551234. How to standardize?"

**Answer:**
- Add custom column to remove non-numeric characters
- Use Text.Remove() to strip spaces, dashes, parentheses
- Then validate length = 10 digits
- Apply Text.Format to consistent format

### Scenario 3: Duplicate Records

**Question**: "Data source has 500 duplicate records by Customer ID. Remove them before importing to Dataverse."

**Answer:**
- Use "Remove Duplicates" based on Customer ID column
- Or use "Keep First" option if duplicates exist
- Verify count before/after
- Consider logging duplicates for audit

### Scenario 4: Incremental vs Full Refresh

**Question**: "100K record import daily. Should use incremental or full refresh?"

**Answer**: **Incremental refresh**
- Requires: Modified Date column
- Filter: Modified Date > Previous Refresh Date
- Benefits: 10-20x faster, less resource usage
- Only use Full if data structure changes

### Scenario 5: Connection Reference in Solution

**Question**: "Power Query dataflow needs to connect to different SQL Server in different environments. How to deploy?"

**Answer:**
- Add data source as **Connection Reference**
- Export dataflow in managed solution
- On import: Reconfigure connection string
- Users enter credentials for target SQL Server
- Dataflow automatically uses new connection

---

## Key Takeaways for the Exam

1. **Power Query is ETL** - Extract, Transform, Load data
2. **400+ connectors** - But only some support query folding
3. **Query Folding** - Pushes transforms to server for performance
4. **Dataflows** - Reusable cloud-based Power Query transformations
5. **Incremental refresh** - Only load new/changed data for speed
6. **M language** - Advanced transformations use M expressions
7. **Merge vs Append** - Merge = join tables, Append = combine rows
8. **Data quality first** - Handle nulls, duplicates, format issues
9. **Connection References** - For portability across environments
10. **On-prem databases need Gateway** - SQL Server, Oracle require data gateway

---

*Created for PL-200 Exam Preparation*
