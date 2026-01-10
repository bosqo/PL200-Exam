# Microsoft Dataverse - Complete Deep Dive
## PL-200 Exam Preparation

---

## Table of Contents
1. [What is Dataverse?](#what-is-dataverse)
2. [Dataverse Architecture](#dataverse-architecture)
3. [Tables (Entities) In-Depth](#tables-entities-in-depth)
4. [Columns (Fields) Complete Guide](#columns-fields-complete-guide)
5. [Relationships Deep Dive](#relationships-deep-dive)
6. [Security Model Explained](#security-model-explained)
7. [Data Management](#data-management)
8. [Common Exam Scenarios](#common-exam-scenarios)

---

## What is Dataverse?

### The Foundation of Power Platform

Microsoft Dataverse (formerly Common Data Service) is the **data platform** that underlies the entire Power Platform. Think of it as a cloud-based, enterprise-grade database that's specifically designed to work with Power Apps, Power Automate, Power BI, and Dynamics 365.

### Why Dataverse Matters

Unlike traditional databases where you need to:
- Set up servers
- Configure connections
- Write SQL queries
- Build APIs

Dataverse provides all of this **out of the box**:

```
┌─────────────────────────────────────────────────────────────────┐
│                         DATAVERSE                                │
├─────────────────────────────────────────────────────────────────┤
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐             │
│  │   Storage   │  │   Security  │  │  Business   │             │
│  │  (Tables)   │  │   (Roles)   │  │   Logic     │             │
│  └─────────────┘  └─────────────┘  └─────────────┘             │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐             │
│  │    APIs     │  │   Audit     │  │  Integration│             │
│  │  (Auto)     │  │   Logs      │  │  (Connectors)│            │
│  └─────────────┘  └─────────────┘  └─────────────┘             │
└─────────────────────────────────────────────────────────────────┘
```

### Key Benefits for the Exam

| Feature | What It Means |
|---------|---------------|
| **Automatic REST API** | Every table gets an API endpoint automatically |
| **Built-in Security** | Role-based access control included |
| **Relational** | Supports relationships between tables |
| **Scalable** | Microsoft manages infrastructure |
| **Auditing** | Track who changed what and when |

---

## Dataverse Architecture

### Environment Structure

Every Dataverse database exists within an **Environment**. Understanding the hierarchy is crucial:

```
Microsoft 365 Tenant
    │
    ├── Environment: Development
    │       └── Dataverse Database
    │               ├── Tables
    │               ├── Security Roles
    │               ├── Solutions
    │               └── Business Logic
    │
    ├── Environment: Test
    │       └── Dataverse Database
    │               └── (Same structure)
    │
    └── Environment: Production
            └── Dataverse Database
                    └── (Same structure)
```

### Standard vs Custom Components

Dataverse comes with many pre-built components:

**Standard (System) Tables:**
- Account
- Contact
- Activity (Email, Task, Appointment)
- User
- Team
- Business Unit

**Standard Security Roles:**
- System Administrator
- System Customizer
- Basic User
- Environment Maker

> **Exam Tip**: You CANNOT delete standard tables, but you CAN customize them (add columns, forms, views).

---

## Tables (Entities) In-Depth

### Understanding Table Types

#### 1. Standard Tables
Pre-built by Microsoft, designed for common business scenarios.

| Table | Purpose | Key Columns |
|-------|---------|-------------|
| **Account** | Organizations/companies | Name, Industry, Revenue |
| **Contact** | Individual people | First Name, Last Name, Email |
| **Lead** | Potential customers | Topic, Status, Rating |
| **Opportunity** | Sales deals | Estimated Value, Close Date |
| **Case** | Support tickets | Title, Priority, Status |

#### 2. Custom Tables
Tables you create for your specific business needs.

**When to create a custom table:**
- No standard table fits your requirements
- You need a separate security boundary
- Data has unique relationships

**Example Custom Tables:**
- Projects
- Inventory Items
- Training Courses
- Service Requests

#### 3. Activity Tables
Special tables for tracking interactions. All activities share common properties:

```
                    ┌─────────────────┐
                    │    Activity     │
                    │   (Base Table)  │
                    └────────┬────────┘
           ┌─────────────────┼─────────────────┐
           │                 │                 │
    ┌──────┴──────┐   ┌──────┴──────┐   ┌──────┴──────┐
    │    Email    │   │    Task     │   │ Appointment │
    └─────────────┘   └─────────────┘   └─────────────┘
```

**Common Activity Properties:**
- Subject
- Regarding (links to any record)
- Owner
- Due Date
- Status

> **Exam Tip**: Activities use a special "Regarding" lookup that can point to ANY table. This is a polymorphic relationship.

#### 4. Virtual Tables
Display external data WITHOUT copying it into Dataverse.

**Use Cases:**
- Display data from SQL Server
- Show SharePoint lists
- Connect to external APIs

**Key Characteristics:**
- Data stays in source system
- Read-only or limited write
- No storage cost in Dataverse
- Requires data provider configuration

### Table Ownership - Critical Concept

When you create a table, you must choose ownership type. **This cannot be changed later!**

#### User or Team Owned Tables

```
┌─────────────────────────────────────────────────────────┐
│                    RECORD                                │
│  ┌─────────────┐                                        │
│  │   Owner     │ ← User or Team                         │
│  └─────────────┘                                        │
│  ┌─────────────┐                                        │
│  │ Owning BU   │ ← Business Unit of the owner          │
│  └─────────────┘                                        │
└─────────────────────────────────────────────────────────┘
```

**Security Implications:**
- Security roles control access by ownership level
- Records can be assigned to different users
- Sharing is available
- Audit shows who owns what

**Use When:**
- Different users should see different records
- Records need to be assigned/reassigned
- Business unit security matters

#### Organization Owned Tables

```
┌─────────────────────────────────────────────────────────┐
│                    RECORD                                │
│  ┌─────────────┐                                        │
│  │   No Owner  │ ← Organization owns all records        │
│  └─────────────┘                                        │
└─────────────────────────────────────────────────────────┘
```

**Security Implications:**
- All users with read access see ALL records
- No ownership-based filtering
- Cannot assign records
- Simpler security model

**Use When:**
- Reference data (Countries, Categories)
- Configuration tables
- Everyone should see all records

> **Exam Scenario**: "A table must support security roles for business units and allow records to be assigned." → Answer: **User or Team owned**

### Table Properties Deep Dive

| Property | Description | Exam Relevance |
|----------|-------------|----------------|
| **Display Name** | Shown in UI | Can be changed anytime |
| **Plural Name** | Used in navigation | Displayed in site map |
| **Schema Name** | Technical identifier | Cannot be changed, prefix matters |
| **Primary Column** | Main identifier field | Required, usually Name |
| **Enable Attachments** | Allow file attachments | Creates Notes relationship |
| **Enable Activities** | Allow activities | Creates Regarding relationship |
| **Enable Auditing** | Track changes | Requires org-level auditing enabled |
| **Enable Change Tracking** | For synchronization | Used by sync scenarios |
| **Enable Quick Create** | Allow quick create forms | Must create quick create form |

---

## Columns (Fields) Complete Guide

### Column Data Types Explained

#### Text Columns

| Type | Max Length | Use Case |
|------|------------|----------|
| **Single Line of Text** | 4,000 chars | Names, titles, codes |
| **Multiple Lines of Text** | 1,048,576 chars | Descriptions, notes |
| **Rich Text** | 1,048,576 chars | Formatted content |

**Format Options for Single Line:**
- Text (default)
- Email (validates format)
- URL (clickable link)
- Ticker Symbol
- Phone

#### Number Columns

| Type | Range | Precision |
|------|-------|-----------|
| **Whole Number** | -2,147,483,648 to 2,147,483,647 | None |
| **Decimal** | Up to 10 decimal places | Configurable |
| **Float** | 5 decimal places | Fixed |
| **Currency** | Based on org settings | Currency-aware |

> **Exam Tip**: For financial calculations, ALWAYS use **Currency** type. It handles exchange rates and currency symbols.

#### Date and Time Columns

**Behavior Options** (Critical for Exam):

| Behavior | Storage | Display |
|----------|---------|---------|
| **User Local** | UTC | Converted to user's timezone |
| **Date Only** | No time component | Same everywhere |
| **Time Zone Independent** | Stored as-is | Same everywhere |

**Common Exam Scenario:**
"A global company needs to track when events happen. All users should see the same time regardless of location."
→ Answer: **Time Zone Independent**

"Users in different countries enter appointment times. Each should see times in their local timezone."
→ Answer: **User Local**

#### Choice Columns (Option Sets)

**Local vs Global Choices:**

```
Local Choice                    Global Choice
┌─────────────────┐            ┌─────────────────┐
│ Table: Orders   │            │ Shared Across   │
│ ┌─────────────┐ │            │ Multiple Tables │
│ │Order Status │ │            │ ┌─────────────┐ │
│ │ - New       │ │            │ │  Priority   │ │
│ │ - Processing│ │            │ │  - Low      │ │
│ │ - Shipped   │ │            │ │  - Medium   │ │
│ └─────────────┘ │            │ │  - High     │ │
└─────────────────┘            │ └─────────────┘ │
Only used here                 └─────────────────┘
                               Used by: Cases, Tasks,
                               Projects, etc.
```

> **Exam Tip**: If you need the SAME choices in multiple tables, create a **Global Choice**. Changing it updates all tables.

#### Lookup Columns

When you create a lookup column, you're actually creating a **relationship**.

```
Contact Table                    Account Table
┌─────────────────┐             ┌─────────────────┐
│ First Name      │             │ Name            │
│ Last Name       │             │ Industry        │
│ Email           │             │ Revenue         │
│ ┌─────────────┐ │   points    └─────────────────┘
│ │ Account     │─┼──────────────────────►
│ │ (Lookup)    │ │
│ └─────────────┘ │
└─────────────────┘
```

**Lookup Behavior:**
- Creates 1:N relationship automatically
- Shows related record name
- Can navigate to related record
- Enables filtering by related data

### Calculated, Rollup, and Formula Columns

#### Calculated Columns

**What They Do:** Compute values from other columns in the SAME record.

**Example:**
```
Full Name = First Name + " " + Last Name
Profit = Revenue - Cost
Days Until Due = Due Date - Today
```

**Limitations:**
- Cannot reference other tables
- Cannot use complex logic
- Being deprecated (use Formula instead)

#### Rollup Columns

**What They Do:** Aggregate data from RELATED records.

```
Account                          Contacts (related)
┌─────────────────┐             ┌─────────────────┐
│ Name            │             │ John Smith      │
│ ┌─────────────┐ │  counts     │ Jane Doe        │
│ │ Contact     │ │◄────────────│ Bob Johnson     │
│ │ Count: 3    │ │             └─────────────────┘
│ │ (Rollup)    │ │
│ └─────────────┘ │
└─────────────────┘
```

**Aggregation Types:**
- SUM
- COUNT
- MIN
- MAX
- AVG

**Critical Limitation:**
> **Rollup columns update every ~1 hour (asynchronous).** If you need real-time totals, use Power Automate or a plugin.

#### Formula Columns (Modern)

**What They Do:** Real-time calculations using Power Fx.

**Advantages over Calculated:**
- Power Fx syntax (familiar from Canvas Apps)
- More functions available
- Null handling (null + 5 = 5, not null)
- Better performance

**Example Formulas:**
```powerfx
// Concatenation
FirstName & " " & LastName

// Conditional
If(Status = "Active", "✓", "✗")

// Date calculation
DateDiff(CreatedOn, Now(), Days)
```

**Limitations:**
- 1,000 character max formula length
- Maximum nesting depth of 10
- Cannot create certain data types
- Cannot be used in plugins or rollups

### Autonumber Columns

Automatically generate sequential identifiers.

**Configuration Options:**
- Prefix: "CASE-"
- Seed: Starting number (e.g., 1000)
- Suffix: Optional ending text

**Example Output:**
```
CASE-1001
CASE-1002
CASE-1003
```

> **Exam Scenario**: "A company needs unique, sequential case numbers that cannot be edited."
→ Answer: **Autonumber column**

### Alternate Keys

Define uniqueness constraints beyond the primary key.

**Use Cases:**
1. **Prevent Duplicates**: Ensure no two records have same combination of values
2. **Integration**: Use business keys instead of GUIDs for upsert operations

**Example:**
```
Alternate Key: Employee_ID
Columns: [EmployeeNumber, CompanyCode]

This ensures no two employees can have the same
EmployeeNumber + CompanyCode combination.
```

---

## Relationships Deep Dive

### One-to-Many (1:N) Relationships

The most common relationship type.

```
PARENT (One)                    CHILD (Many)
┌─────────────────┐            ┌─────────────────┐
│    Account      │            │    Contact      │
│                 │ 1      N   │                 │
│  ────────────── │───────────►│  ────────────── │
│                 │            │  Account        │
│                 │            │  (Lookup)       │
└─────────────────┘            └─────────────────┘
```

**Created Components:**
- Lookup column on the child table
- Subgrid/related records view on parent
- Navigation option in parent form

**Relationship Behavior (Cascade Rules):**

| Action | Options | Description |
|--------|---------|-------------|
| **Assign** | Cascade All, Cascade None, etc. | What happens to children when parent is assigned |
| **Share** | Cascade All, Cascade None, etc. | What happens when parent is shared |
| **Delete** | Cascade All, Remove Link, Restrict | What happens when parent is deleted |
| **Reparent** | Cascade All, Cascade None | What happens when child's parent changes |

> **Exam Tip**: "Restrict Delete" prevents parent deletion if children exist. "Cascade All Delete" deletes all children when parent is deleted.

### Many-to-Many (N:N) Relationships

Both tables can have multiple related records.

```
Products                        Categories
┌─────────────────┐            ┌─────────────────┐
│ Laptop          │◄──────────►│ Electronics     │
│ Keyboard        │◄──────────►│ Accessories     │
│ Mouse           │            │ Office          │
└─────────────────┘            └─────────────────┘
     │                              │
     └──────────┬───────────────────┘
                │
        ┌───────┴───────┐
        │  Intersect    │
        │    Table      │
        │ (Hidden)      │
        │ - ProductID   │
        │ - CategoryID  │
        └───────────────┘
```

**Key Limitations (Frequently Tested):**
1. ❌ Cannot add custom columns to the intersect table
2. ❌ Cannot trigger workflows when records are associated
3. ❌ Intersect table not visible in UI
4. ❌ Cannot report directly on the relationship

**Workaround for Limitations:**
Create a custom "junction" table with two 1:N relationships instead.

### Polymorphic Lookups

A lookup that can point to multiple table types.

**Built-in Polymorphic Lookups:**

| Lookup | Points To |
|--------|-----------|
| **Customer** | Account OR Contact |
| **Regarding** | Many activity-enabled tables |
| **Owner** | User OR Team |

**Custom Polymorphic Lookups:**
Can be created via code/API (not in UI maker).

```
Case                           Can point to either:
┌─────────────────┐            ┌─────────────────┐
│ Title           │            │    Account      │
│ ┌─────────────┐ │───────────►└─────────────────┘
│ │ Customer    │ │            OR
│ │(Polymorphic)│ │───────────►┌─────────────────┐
│ └─────────────┘ │            │    Contact      │
└─────────────────┘            └─────────────────┘
```

---

## Security Model Explained

### The Security Hierarchy

Understanding the hierarchy is essential for the exam:

```
┌─────────────────────────────────────────────────────────────┐
│                         TENANT                               │
│  ┌───────────────────────────────────────────────────────┐  │
│  │                    ENVIRONMENT                         │  │
│  │  ┌─────────────────────────────────────────────────┐  │  │
│  │  │              ROOT BUSINESS UNIT                  │  │  │
│  │  │  ┌─────────────────┐  ┌─────────────────┐       │  │  │
│  │  │  │  Child BU: USA  │  │ Child BU: Europe│       │  │  │
│  │  │  │  ┌───────────┐  │  │  ┌───────────┐  │       │  │  │
│  │  │  │  │ Team: Sales│  │  │  │Team: Sales│  │       │  │  │
│  │  │  │  │ ┌───────┐  │  │  │  │ ┌───────┐ │  │       │  │  │
│  │  │  │  │ │ Users │  │  │  │  │ │ Users │ │  │       │  │  │
│  │  │  │  │ └───────┘  │  │  │  │ └───────┘ │  │       │  │  │
│  │  │  │  └───────────┘  │  │  └───────────┘  │       │  │  │
│  │  │  └─────────────────┘  └─────────────────┘       │  │  │
│  │  └─────────────────────────────────────────────────┘  │  │
│  └───────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
```

### Business Units

**What They Are:** Organizational containers for users, teams, and security.

**Key Points:**
- Every environment has ONE root business unit
- Business units can have child business units
- Users belong to exactly ONE business unit
- Records have an "Owning Business Unit" field

**Use Cases:**
- Separate divisions (Sales, Marketing, Support)
- Geographic regions (Americas, EMEA, APAC)
- Subsidiaries or departments

### Security Roles In Detail

Security roles define WHAT a user can do with WHICH tables.

**Permission Types:**

| Permission | Description |
|------------|-------------|
| **Create** | Add new records |
| **Read** | View records |
| **Write** | Edit records |
| **Delete** | Remove records |
| **Append** | Attach related records |
| **Append To** | Be attached to by other records |
| **Assign** | Change record owner |
| **Share** | Give access to others |

**Access Levels Visualized:**

```
                    ORGANIZATION
                    ┌─────────┐
                    │  ⊕ All  │
                    └────┬────┘
                         │
              PARENT:CHILD (Deep)
              ┌──────────┴──────────┐
              │ ◎ My BU + Children  │
              └──────────┬──────────┘
                         │
                  BUSINESS UNIT
                  ┌──────┴──────┐
                  │ ◉ My BU Only │
                  └──────┬──────┘
                         │
                      USER
                    ┌──┴──┐
                    │ ● Me│
                    └─────┘
```

**Exam Scenario Examples:**

| Requirement | Access Level |
|-------------|--------------|
| "See only their own records" | User (Basic) |
| "See all records in their division" | Business Unit (Local) |
| "Managers see their team's records" | Parent: Child (Deep) |
| "Everyone sees everything" | Organization (Global) |

### Team Types Comparison

#### Owner Teams

```
┌─────────────────────────────────────────────┐
│              OWNER TEAM                      │
├─────────────────────────────────────────────┤
│ ✓ Can own records                           │
│ ✓ Has security roles assigned               │
│ ✓ Members inherit team's security roles     │
│ ✓ Has a business unit                       │
│ ✓ Permanent membership                      │
└─────────────────────────────────────────────┘
```

**Use When:**
- Team needs to collectively own records
- Team needs specific security role
- Membership is relatively stable

#### Access Teams

```
┌─────────────────────────────────────────────┐
│              ACCESS TEAM                     │
├─────────────────────────────────────────────┤
│ ✗ Cannot own records                        │
│ ✗ No security roles assigned                │
│ ✓ Provides record-level access              │
│ ✓ Dynamic membership per record             │
│ ✓ Created automatically via access team     │
│   templates                                 │
└─────────────────────────────────────────────┘
```

**Use When:**
- Ad-hoc collaboration needed
- Different people need access to different records
- Team membership changes frequently

> **Exam Trap**: Access teams DO NOT own records and DO NOT have security roles. This is the most common mistake!

#### Azure AD Group Teams

```
┌─────────────────────────────────────────────┐
│          AAD GROUP TEAM                      │
├─────────────────────────────────────────────┤
│ ✓ Can own records                           │
│ ✓ Has security roles assigned               │
│ ✓ Membership synced from Azure AD           │
│ ✓ Automatic user provisioning               │
└─────────────────────────────────────────────┘
```

**Types:**
- **Security Group Team**: Syncs with AAD Security Group
- **Office Group Team**: Syncs with Microsoft 365 Group

### Field-Level Security

Security roles control TABLE access. Field-level security controls COLUMN access.

```
User has Read on Contact table
┌─────────────────────────────────────────────┐
│ Contact Record                               │
├─────────────────────────────────────────────┤
│ Name: John Smith          ← Can see         │
│ Email: john@company.com   ← Can see         │
│ Phone: 555-1234           ← Can see         │
│ SSN: ***-**-****          ← HIDDEN          │
│ Salary: *****             ← HIDDEN          │
└─────────────────────────────────────────────┘
         Field Security Profile controls
         which users can see SSN and Salary
```

**Setup Steps:**
1. Enable field-level security on the column
2. Create a Field Security Profile
3. Add users/teams to the profile
4. Configure Read/Update/Create permissions

### Hierarchy Security

Automatically grant access based on management hierarchy.

**Manager Hierarchy:**
```
        CEO (Sarah)
            │
    ┌───────┴───────┐
    │               │
VP Sales (Tom)  VP Marketing (Amy)
    │
Sales Rep (Bob)

Sarah can see: Tom's, Amy's, Bob's records
Tom can see: Bob's records
Bob can see: Only his own records
```

**Position Hierarchy:**
Based on assigned positions, not manager relationship.

---

## Data Management

### Import Options

| Method | Best For | Data Size |
|--------|----------|-----------|
| **Import Data Wizard** | Simple Excel/CSV | Small to Medium |
| **Dataflows** | Complex transformations | Medium to Large |
| **Azure Data Factory** | Enterprise integration | Large |
| **Power Automate** | Ongoing sync | Small batches |

### Configuration Migration Tool

**Purpose:** Copy REFERENCE data between environments.

**What It Copies:**
- Lookup table values (Countries, Categories)
- Configuration settings
- System configuration records

**What It Does NOT Copy:**
- Transactional data
- User records
- Security role assignments

> **Exam Tip**: For moving configuration/reference data between DEV → TEST → PROD, use **Configuration Migration Tool**.

### Duplicate Detection

**Components:**
1. **Duplicate Detection Rules**: Define what makes a duplicate
2. **Duplicate Detection Jobs**: Find existing duplicates
3. **Real-time Detection**: Check on create/update

**Rule Configuration:**
- Base table (where to look)
- Match criteria (which columns)
- Case sensitivity
- Matching algorithm

---

## Common Exam Scenarios

### Scenario 1: Table Ownership Decision
**Question:** "Records must be assigned to specific users, and managers should only see their team's records."

**Answer:** User or Team owned + Parent:Child security role access level

### Scenario 2: Preventing Duplicates
**Question:** "Ensure no two contacts have the same email address and phone number combination."

**Answer:** Create an Alternate Key on Email + Phone columns

### Scenario 3: Aggregating Related Data
**Question:** "Display the total invoice amount on the Account form from all related invoices."

**Answer:** Rollup column (but note: updates hourly)

### Scenario 4: Real-time Calculations
**Question:** "Calculate full name from first and last name, updating immediately."

**Answer:** Formula column

### Scenario 5: External Data Display
**Question:** "Display data from SQL Server without copying it into Dataverse."

**Answer:** Virtual Table

### Scenario 6: Sensitive Field Protection
**Question:** "Only HR staff should see the Salary field on the Employee table."

**Answer:** Enable field-level security, create Field Security Profile for HR

### Scenario 7: Dynamic Access Sharing
**Question:** "Different people need access to different project records based on their assignment."

**Answer:** Access Teams with Access Team Template

---

## Key Takeaways for the Exam

1. **Table Ownership is permanent** - cannot be changed after creation
2. **Rollup columns update hourly** - not real-time
3. **Access Teams don't own records** - most common trick question
4. **Many-to-Many can't have custom columns** - use junction table if needed
5. **Formula columns use Power Fx** - modern replacement for calculated
6. **Alternate Keys enforce uniqueness** - not just for integration
7. **Business Units are hierarchical** - affects security inheritance
8. **Field Security is separate from Table Security** - both must be configured

---

*Continue to the next section: [Model-Driven Apps Deep Dive](./PL-200-ModelDrivenApps-DeepDive.md)*
