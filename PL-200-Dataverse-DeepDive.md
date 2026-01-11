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
7. [Business Rules](#business-rules)
8. [Solutions and Application Lifecycle Management](#solutions-and-application-lifecycle-management)
9. [Data Management](#data-management)
10. [Common Exam Scenarios](#common-exam-scenarios)

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

## Business Rules

### What Are Business Rules?

Business rules provide a **no-code way** to apply logic and validation to data in Dataverse. They run on the server and work across all interfaces (model-driven apps, canvas apps, Power Automate, API).

```
┌─────────────────────────────────────────────────────────────┐
│                    BUSINESS RULE                             │
├─────────────────────────────────────────────────────────────┤
│  IF Condition → THEN Action                                 │
│  ┌─────────────┐         ┌─────────────┐                   │
│  │  Condition  │ ──────► │   Action    │                   │
│  │  Annual     │         │  Set Credit │                   │
│  │  Revenue    │         │  Limit to   │                   │
│  │  > $1M      │         │  $50K       │                   │
│  └─────────────┘         └─────────────┘                   │
└─────────────────────────────────────────────────────────────┘
```

### When to Use Business Rules

| Use Case | Example |
|----------|---------|
| **Field Validation** | Discount cannot exceed 50% |
| **Set Field Values** | Set Status to "Approved" when Score > 80 |
| **Show/Hide Fields** | Hide shipping fields if product is digital |
| **Show Recommendations** | Show warning if expiration date is near |
| **Lock/Unlock Fields** | Lock price after order is submitted |

### Business Rule Scope

**Critical for Exam:** Business rules have different scopes that determine WHERE they execute.

| Scope | Where It Runs | Use When |
|-------|---------------|----------|
| **Entity (Table)** | Server-side, all interfaces | Universal logic needed everywhere |
| **All Forms** | Client-side, all forms | Form-specific UI behavior |
| **Specific Form** | Client-side, one form only | Form-specific requirements |

```
┌─────────────────────────────────────────────────────────────┐
│                  SCOPE COMPARISON                            │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  Entity (Table) Scope                                       │
│  ├─► Model-Driven App Forms     ✓                          │
│  ├─► Canvas Apps                ✓                          │
│  ├─► Power Automate             ✓                          │
│  ├─► Web API                    ✓                          │
│  └─► Mobile Apps                ✓                          │
│                                                              │
│  Form Scope                                                 │
│  ├─► Model-Driven App Forms     ✓                          │
│  ├─► Canvas Apps                ✗                          │
│  ├─► Power Automate             ✗                          │
│  ├─► Web API                    ✗                          │
│  └─► Mobile Apps                ✗                          │
└─────────────────────────────────────────────────────────────┘
```

> **Exam Tip**: If logic must work in Canvas Apps, Power Automate, or via API, use **Entity (Table) scope**, NOT form scope.

### Available Actions

Business rules can perform these actions:

| Action | Description | Example |
|--------|-------------|---------|
| **Set Field Value** | Populate a field automatically | Set Priority = "High" when Amount > $10,000 |
| **Show Error Message** | Display validation error | "End Date cannot be before Start Date" |
| **Show Recommendation** | Display non-blocking message | "Consider adding a phone number" |
| **Set Business Required** | Make field mandatory | Require approval when discount > 20% |
| **Lock or Unlock Field** | Enable/disable editing | Lock price after approval |
| **Set Visibility** | Show/hide field | Hide shipping address for digital products |
| **Set Default Value** | Set initial value | Default Country = "USA" |

### Conditions

Business rules support logical conditions:

**Operators Available:**
- Equals / Does not equal
- Contains / Does not contain
- Begins with / Does not begin with
- Ends with / Does not end with
- Contains data / Does not contain data
- Greater than / Less than
- On or after / On or before

**Logical Grouping:**
```
IF (Revenue > $1M AND Industry = "Technology")
   OR (Customer Type = "Enterprise")
THEN
   Set Credit Limit = $100,000
```

### Business Rule Limitations

**Critical Limitations (Frequently Tested):**

| Limitation | Description | Workaround |
|------------|-------------|------------|
| **No Cross-Table Logic** | Cannot reference related table fields | Use JavaScript or Power Automate |
| **No Complex Calculations** | Limited mathematical operations | Use Formula columns or JavaScript |
| **No Real-Time Field Updates** | Won't trigger on field changes from other rules | Use JavaScript for cascading logic |
| **No Async Operations** | Cannot wait for external services | Use Power Automate |
| **Limited to 10 Conditions** | Maximum 10 condition groups per rule | Split into multiple rules or use code |
| **No Workflow Triggering** | Cannot start flows or processes | Use Power Automate trigger instead |
| **Form Scope = Client Only** | Not enforced via API if form-scoped | Use Entity scope for server-side |
| **No Create/Update Operations** | Cannot create new records | Use Power Automate or plugins |

**What Business Rules CANNOT Do:**

❌ Access data from related tables (lookups)
❌ Call external web services
❌ Create or update related records
❌ Execute based on time/schedule
❌ Send emails or notifications
❌ Run JavaScript functions
❌ Access user information (current user)
❌ Perform complex string manipulations

> **Exam Scenario**: "A business rule must validate that a contact's city matches their account's city."
→ **Answer**: This is NOT possible with business rules (cross-table logic). Use JavaScript or Power Automate instead.

### Business Rules vs. Other Options

| Requirement | Solution |
|-------------|----------|
| Simple field validation on one table | **Business Rule** |
| Show/hide fields based on values | **Business Rule** |
| Validate against related table data | **JavaScript** |
| Create related records | **Power Automate** |
| Complex calculations across tables | **Power Automate** or **Plugins** |
| Send notifications | **Power Automate** |
| Real-time cascading updates | **JavaScript** |
| Server-side logic for all interfaces | **Business Rule (Entity scope)** or **Plugins** |

### Creating Business Rules - Best Practices

**Design Considerations:**

1. **Choose Correct Scope**
   - Need it in Canvas Apps/API? → Entity scope
   - UI-only validation? → Form scope acceptable

2. **Keep It Simple**
   - One rule per logical requirement
   - Avoid complex nested conditions
   - Clear, descriptive names

3. **Test Thoroughly**
   - Test in all interfaces if Entity-scoped
   - Test with different user security roles
   - Verify error messages are user-friendly

4. **Performance**
   - Too many rules can slow form loads
   - Consider combining related logic
   - Entity-scoped rules add server processing

### Execution Order

When multiple business rules exist:

```
1. Business Rules (Entity Scope) - Server-side
2. Synchronous Plugins (Pre-Operation)
3. Database Operation
4. Synchronous Plugins (Post-Operation)
5. Asynchronous Plugins
6. Business Rules (Form Scope) - Client-side
7. JavaScript (OnChange, OnLoad, OnSave)
```

> **Exam Tip**: Entity-scoped business rules execute BEFORE form-scoped rules and JavaScript.

### Common Exam Scenarios

**Scenario 1:**
"Users should see a warning if the discount exceeds 30%, but still allow save."
→ **Answer**: Show Recommendation action (non-blocking)

**Scenario 2:**
"Prevent saving if End Date is before Start Date."
→ **Answer**: Show Error Message action (blocking)

**Scenario 3:**
"Logic must work in model-driven apps, canvas apps, and Power Automate."
→ **Answer**: Entity (Table) scope

**Scenario 4:**
"When opportunity amount exceeds $100K, automatically set the approval field to required."
→ **Answer**: Business rule with "Set Business Required" action

**Scenario 5:**
"Validate that a project's budget doesn't exceed the account's annual revenue."
→ **Answer**: NOT possible with business rules (cross-table). Use JavaScript or Power Automate.

---

## Solutions and Application Lifecycle Management

### What Are Solutions?

Solutions are **containers** that package your customizations and configurations for transport between environments. Think of them as deployment packages for Power Platform.

```
┌─────────────────────────────────────────────────────────────┐
│                      SOLUTION                                │
├─────────────────────────────────────────────────────────────┤
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │   Tables    │  │    Forms    │  │   Views     │        │
│  └─────────────┘  └─────────────┘  └─────────────┘        │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │   Flows     │  │  Plugins    │  │ Security    │        │
│  │             │  │             │  │  Roles      │        │
│  └─────────────┘  └─────────────┘  └─────────────┘        │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │   Canvas    │  │  Business   │  │   Charts    │        │
│  │    Apps     │  │   Rules     │  │             │        │
│  └─────────────┘  └─────────────┘  └─────────────┘        │
└─────────────────────────────────────────────────────────────┘
```

### Solution Types: Managed vs. Unmanaged

**This is one of the MOST TESTED topics on the exam!**

#### Unmanaged Solutions

```
┌─────────────────────────────────────────────────────────────┐
│              UNMANAGED SOLUTION                              │
├─────────────────────────────────────────────────────────────┤
│  ✓ Fully editable                                           │
│  ✓ Components can be modified directly                      │
│  ✓ Components can be removed individually                   │
│  ✓ Used for DEVELOPMENT                                     │
│  ✓ Components become part of Default Solution               │
│  ✗ No update path                                           │
│  ✗ Cannot be uninstalled cleanly                            │
└─────────────────────────────────────────────────────────────┘
```

**Use Unmanaged Solutions When:**
- Working in DEV environment
- Building and testing customizations
- Need to modify components directly
- Iterating and making changes

**Key Characteristics:**
- All components are unlocked
- Can add/remove components freely
- Components merge with Default Solution
- No dependency protection
- Cannot track "what came from where"

#### Managed Solutions

```
┌─────────────────────────────────────────────────────────────┐
│               MANAGED SOLUTION                               │
├─────────────────────────────────────────────────────────────┤
│  ✓ Locked and protected                                     │
│  ✓ Can be uninstalled cleanly                               │
│  ✓ Can be updated/upgraded                                  │
│  ✓ Used for TEST and PRODUCTION                             │
│  ✓ Maintains dependencies                                   │
│  ✗ Cannot modify components directly                        │
│  ✗ Cannot delete individual components                      │
└─────────────────────────────────────────────────────────────┘
```

**Use Managed Solutions When:**
- Deploying to TEST/PROD environments
- Distributing to customers/other organizations
- Need clean uninstall capability
- Want to protect intellectual property
- Need version control and updates

**Key Characteristics:**
- Components are locked (cannot edit)
- Solution tracks all its components
- Can be uninstalled (removes all components)
- Supports versioning (1.0, 1.1, 2.0)
- Dependency tracking enforced
- Appears separate from Default Solution

### Managed vs. Unmanaged: Side-by-Side Comparison

| Feature | Unmanaged | Managed |
|---------|-----------|---------|
| **Editable** | ✅ Yes | ❌ No (locked) |
| **Can Uninstall** | ❌ No | ✅ Yes (clean removal) |
| **Can Update** | N/A | ✅ Yes (version upgrades) |
| **Environment** | Development | Test, Production |
| **Components Visible in Default** | ✅ Yes | ❌ No (separate) |
| **Can Remove Single Component** | ✅ Yes | ❌ No (must uninstall solution) |
| **Dependency Protection** | ❌ No | ✅ Yes |
| **IP Protection** | ❌ No | ✅ Yes |
| **Customization Allowed** | ✅ Full | ⚠️ Limited (managed properties) |

### Solution Layers

When both managed and unmanaged customizations exist, **layers** are created:

```
┌─────────────────────────────────────────────────────────────┐
│                     LAYERING                                 │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  Active Layer (What user sees)                              │
│  ┌────────────────────────────────────────────────────┐    │
│  │  Unmanaged Customizations (Default Solution)       │    │
│  │  Priority: HIGHEST                                  │    │
│  └────────────────────────────────────────────────────┘    │
│                         ↓                                    │
│  ┌────────────────────────────────────────────────────┐    │
│  │  Managed Solution: Latest Version                   │    │
│  │  Priority: HIGH                                     │    │
│  └────────────────────────────────────────────────────┘    │
│                         ↓                                    │
│  ┌────────────────────────────────────────────────────┐    │
│  │  Managed Solution: Older Version                    │    │
│  │  Priority: MEDIUM                                   │    │
│  └────────────────────────────────────────────────────┘    │
│                         ↓                                    │
│  ┌────────────────────────────────────────────────────┐    │
│  │  System Base Layer                                  │    │
│  │  Priority: LOWEST                                   │    │
│  └────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────┘
```

**Layer Priority Rules:**
1. **Unmanaged customizations ALWAYS win** (top layer)
2. Newest managed solution overrides older ones
3. System base layer is the foundation

> **Exam Tip**: Unmanaged changes override managed solutions. To apply managed solution updates, remove unmanaged customizations first.

### Managed Properties

Control what can be customized in a managed solution.

**Common Managed Properties:**

| Property | What It Controls |
|----------|------------------|
| **Can be customized** | Whether component can be modified |
| **Can be deleted** | Whether component can be removed |
| **Can be renamed** | Display name changes |
| **Can change additional properties** | Specific attribute modifications |
| **Can create new forms** | Add forms to entity |
| **Can create new charts** | Add visualizations |

**Example Configuration:**
```
Table: "Project"
├─ Can be customized: ✓ Yes (allow minor changes)
│   ├─ Display name can be changed: ✓ Yes
│   ├─ Can create forms: ✓ Yes
│   └─ Can add fields: ✗ No (locked)
└─ Can be deleted: ✗ No (protected)
```

> **Exam Scenario**: "Allow customers to rename tables but not delete them."
→ **Answer**: Set "Can be customized" = Yes, "Can be deleted" = No in managed properties.

### Application Lifecycle Management (ALM)

ALM is the process of managing solutions across environments.

#### Standard ALM Flow

```
┌─────────────┐      ┌─────────────┐      ┌─────────────┐
│     DEV     │      │    TEST     │      │    PROD     │
│             │      │             │      │             │
│ Unmanaged   │──────►  Managed    │──────►  Managed    │
│ Solution    │Export│  Solution   │Export│  Solution   │
│             │Import│             │Import│             │
│ ✎ Edit      │      │ ✓ Validate  │      │ ✓ Live      │
│ ✎ Build     │      │ ✓ Test      │      │ ⚠ Protected │
└─────────────┘      └─────────────┘      └─────────────┘
     Make                 Verify               Deploy
   Changes               Quality              to Users
```

**Process Steps:**

1. **Development (DEV)**
   - Create unmanaged solution
   - Add components (tables, forms, flows)
   - Test and iterate
   - Export as **MANAGED** solution

2. **Testing (TEST)**
   - Import managed solution
   - Perform UAT (User Acceptance Testing)
   - Validate functionality
   - Do NOT make direct changes

3. **Production (PROD)**
   - Import managed solution
   - Deploy to end users
   - Monitor and support
   - Do NOT make direct changes

> **Critical Rule**: Changes go DEV → TEST → PROD. NEVER edit directly in TEST or PROD.

### Solution Import Types

When importing a solution, you have options:

| Import Type | What Happens | Use When |
|-------------|--------------|----------|
| **Update** | Adds new components, updates existing | Normal deployments |
| **Upgrade** | Replaces previous version, removes deleted items | Major version changes |
| **Stage for Upgrade** | Imports but doesn't apply yet | Want to review before applying |

**Update vs. Upgrade:**

```
UPDATE                           UPGRADE
┌─────────────────┐             ┌─────────────────┐
│ Old: Table A    │             │ Old: Table A    │
│ Old: Table B    │             │ Old: Table B    │
│ Old: Form X     │             │ Old: Form X     │
└─────────────────┘             └─────────────────┘
        ↓                                ↓
Import Solution                 Import Solution
        ↓                                ↓
┌─────────────────┐             ┌─────────────────┐
│ Old: Table A    │             │ New: Table A    │
│ New: Table B    │ ← Updated   │ New: Table C    │ ← Replaced
│ Old: Form X     │ ← Unchanged │ New: Form Y     │ ← Old removed
│ New: Table C    │ ← Added     └─────────────────┘
└─────────────────┘
Keeps old components            Removes old components
```

> **Exam Tip**: Use **Upgrade** when you've removed components in DEV and want them removed from target environment.

### Solution Dependencies

Dependencies prevent breaking changes.

**Example:**
```
Solution A                    Solution B
┌─────────────────┐          ┌─────────────────┐
│ Account Table   │◄─────────│ Custom Form     │
│                 │ depends  │ (uses Account)  │
└─────────────────┘   on     └─────────────────┘

❌ Cannot uninstall Solution A while Solution B exists
✓ Must uninstall Solution B first
```

**Dependency Types:**

| Type | Description | Can Break |
|------|-------------|-----------|
| **Published** | Component references another | Yes - blocks uninstall |
| **Unpublished** | Draft references | No - warning only |
| **Internal** | Within same solution | No - managed together |

### Common ALM Scenarios (Exam Focused)

**Scenario 1:**
"You need to deploy customizations to production and ensure they can be cleanly removed later if needed."
→ **Answer**: Export as **Managed** solution and import to production.

**Scenario 2:**
"Developers need to make changes to tables and test them iteratively."
→ **Answer**: Use **Unmanaged** solution in DEV environment.

**Scenario 3:**
"A managed solution was imported to production, but users need a custom field. How should this be added?"
→ **Answer**: Add field in DEV, export managed solution, and upgrade in production. (Do NOT add directly in PROD)

**Scenario 4:**
"After importing a solution update, some components show 'unmanaged' customizations."
→ **Answer**: Someone made direct changes in the target environment. Remove unmanaged layer or recreate in managed solution.

**Scenario 5:**
"You need to allow customers to modify table display names but protect form configurations."
→ **Answer**: Configure managed properties: Allow customization of display names, but lock forms.

**Scenario 6:**
"How to completely remove all components of a solution from an environment?"
→ **Answer**: Only possible with **Managed** solutions. Unmanaged solutions cannot be cleanly uninstalled.

### Solution Publisher

Every solution has a publisher that defines:

- **Prefix**: Used for schema names (e.g., `abc_CustomTable`)
- **Display Name**: Organization name
- **Option Value Prefix**: Number prefix for choices (e.g., 10000)

**Why It Matters:**
- Prevents naming conflicts
- Identifies component ownership
- Required for AppSource submissions

```
Publisher: Contoso
├─ Prefix: contoso_
└─ Tables created:
    ├─ contoso_project
    ├─ contoso_task
    └─ contoso_invoice
```

> **Exam Tip**: Custom publishers should be created BEFORE building solutions. Default publisher is "Default Publisher" with prefix "new_".

### Environment Strategy

**Typical Environment Setup:**

```
┌─────────────────────────────────────────────────────────────┐
│  DEV Environment(s)                                          │
│  • Individual developer sandboxes                            │
│  • Unmanaged solutions                                       │
│  • Frequent changes                                          │
└──────────────────────┬───────────────────────────────────────┘
                       │ Export Managed
                       ↓
┌─────────────────────────────────────────────────────────────┐
│  BUILD Environment (Optional)                                │
│  • Integration of multiple solutions                         │
│  • Automated builds                                          │
│  • Testing integrations                                      │
└──────────────────────┬───────────────────────────────────────┘
                       │ Export Managed
                       ↓
┌─────────────────────────────────────────────────────────────┐
│  TEST/UAT Environment                                        │
│  • User acceptance testing                                   │
│  • Managed solutions only                                    │
│  • Mirrors production configuration                          │
└──────────────────────┬───────────────────────────────────────┘
                       │ Export Managed
                       ↓
┌─────────────────────────────────────────────────────────────┐
│  PRODUCTION Environment                                      │
│  • Live user data                                            │
│  • Managed solutions only                                    │
│  • Locked down security                                      │
│  • Change control process                                    │
└─────────────────────────────────────────────────────────────┘
```

### Best Practices for Solutions

1. **Use Version Numbers Properly**
   - Semantic versioning: Major.Minor.Build.Revision
   - Example: 1.0.0.0 → 1.0.1.0 (patch) → 1.1.0.0 (minor) → 2.0.0.0 (major)

2. **Keep Solutions Focused**
   - One solution per business capability
   - Avoid "mega solutions" with everything

3. **Never Mix Managed and Unmanaged**
   - DEV = Unmanaged only
   - TEST/PROD = Managed only

4. **Use Source Control**
   - Export solutions regularly
   - Store in Git/Azure DevOps
   - Track changes over time

5. **Document Dependencies**
   - Know what depends on what
   - Plan uninstall order
   - Test in non-prod first

6. **Test Solution Imports**
   - Always test in lower environment first
   - Validate dependencies are met
   - Check for naming conflicts

7. **Handle Connection References**
   - Solutions may contain flows with connections
   - Connections must be recreated in target environment
   - Use environment variables for configuration

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

## Environment Management and Power Platform Admin Center

### Understanding Environments

**Environments** are containers that provide:
- Isolation between development, test, and production
- Security boundaries (separate users and permissions)
- Data storage (each environment has its own Dataverse database)
- Regional deployment options

```
┌─────────────────────────────────────────────────────────────┐
│                    ENVIRONMENT ANATOMY                       │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  Environment: Sales Production                              │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  • Region: United States                             │  │
│  │  • Type: Production                                   │  │
│  │  • Dataverse Database: Yes                           │  │
│  │  • Security Groups: Sales Team                       │  │
│  │                                                       │  │
│  │  Resources:                                           │  │
│  │    ├─ Canvas Apps                                    │  │
│  │    ├─ Model-Driven Apps                              │  │
│  │    ├─ Flows                                          │  │
│  │    ├─ Dataverse Tables                               │  │
│  │    ├─ Solutions                                      │  │
│  │    ├─ Connections                                    │  │
│  │    └─ Data Policies (DLP)                            │  │
│  └──────────────────────────────────────────────────────┘  │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Environment Types

| Type | Purpose | Dataverse | Production Use |
|------|---------|-----------|----------------|
| **Production** | Live applications | Optional | Yes |
| **Sandbox** | Testing and staging | Optional | No |
| **Trial** | 30-day evaluation | Yes | No |
| **Developer** | Individual development | Yes | No |
| **Default** | Auto-created for tenant | Optional | Limited |
| **Teams** | Microsoft Teams integration | Yes | Limited |

#### Production Environments

- **Purpose**: Live applications with real users
- **Backup**: Automatic daily backups (retained 28 days)
- **Support**: Full Microsoft support
- **DLP Policies**: Can apply restrictive policies
- **Licensing**: Requires proper licensing

#### Sandbox Environments

- **Purpose**: Testing, UAT, staging
- **Backup**: Manual backups only (unless upgraded)
- **Copy**: Can copy from Production
- **Reset**: Can be reset to fresh state
- **Licensing**: Included with licenses, limits apply

#### Developer Environments

- **Purpose**: Individual developer use
- **Dataverse**: Always included
- **Limit**: One per user (free)
- **Subscription**: Requires Power Apps Developer Plan or license
- **Expiration**: Non-commercial use only

### Power Platform Admin Center

The **Power Platform Admin Center** (https://admin.powerplatform.microsoft.com) is the central hub for:

```
┌─────────────────────────────────────────────────────────────┐
│          POWER PLATFORM ADMIN CENTER OVERVIEW                │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  Environments                                               │
│    ├─ Create/delete environments                           │
│    ├─ Manage environment settings                          │
│    ├─ Backup and restore                                   │
│    ├─ Copy environments                                    │
│    └─ View environment details                             │
│                                                              │
│  Data Policies (DLP)                                        │
│    ├─ Create data loss prevention policies                 │
│    ├─ Block/allow connectors                               │
│    ├─ Apply to environments                                │
│    └─ Connector classification                             │
│                                                              │
│  Resources                                                  │
│    ├─ Capacity (Database, File, Log)                       │
│    ├─ Power Apps                                           │
│    ├─ Power Automate                                       │
│    └─ Power Pages                                          │
│                                                              │
│  Analytics                                                  │
│    ├─ Usage reports                                        │
│    ├─ Inventory                                            │
│    └─ Capacity trends                                      │
│                                                              │
│  Help + Support                                             │
│    └─ Create support tickets                               │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Creating Environments

**Requirements:**
- Global Admin, Power Platform Admin, or Dynamics 365 Admin role
- Available environment quota
- Proper licensing

**Steps:**
1. Navigate to Power Platform Admin Center
2. **Environments** → **New**
3. Configure:
   - **Name**: Environment name (user-facing)
   - **Region**: Geographic location (cannot be changed later)
   - **Type**: Production, Sandbox, Trial, Developer
   - **Purpose**: Description
   - **Create database**: Yes/No (if yes, configure):
     - **Language**: Default language
     - **Currency**: Base currency (cannot be changed)
     - **Security group**: Restrict access (optional)

> **Exam Tip**: Region and Currency **cannot be changed** after environment creation. Choose carefully!

### Environment Settings

Each environment has detailed settings:

#### General

- **Display name**: Rename environment
- **URL**: Environment URL (e.g., orgname.crm.dynamics.com)
- **Environment type**: View type (cannot change)
- **Security group**: Limit who can access
- **State**: Enable/disable environment

#### Users + Permissions

- **Users**: View/manage users
- **Security roles**: Assign roles
- **Teams**: Manage teams
- **Business units**: Configure BU hierarchy
- **Column security profiles**: Field-level security

#### Resources

- **Dataverse**: Manage database settings
- **Dynamics 365 apps**: Install D365 apps
- **All legacy settings**: Classic settings

#### Audit and Logs

- **Audit settings**: Enable/configure auditing
- **Audit summary view**: View audit logs
- **Plugin trace log**: Enable plugin debugging

#### Data Management

- **Duplicate detection**: Configure rules
- **Bulk delete**: Schedule bulk operations
- **Sample data**: Add/remove sample data

#### Integrations

- **Email settings**: Configure email
- **SharePoint sites**: Connect SharePoint
- **Yammer**: Social integration (deprecated)
- **Activity feeds**: Configure activities

### Backup and Restore

#### Automatic Backups (Production Only)

```
System Backups:
  • Frequency: Daily (automatic)
  • Retention: 28 days
  • Type: Full backup
  • Restore: Self-service or support ticket
```

**Restore Process:**
1. **Environments** → Select environment
2. **Backups** → **System** tab
3. Select backup
4. **Restore** or **Restore to different environment**
5. Confirm (overwrites target)

#### Manual Backups

```
Manual Backups:
  • Frequency: On-demand
  • Retention: Up to 3 backups (unless storage purchased)
  • Type: Full backup
  • Available for: Production and Sandbox
```

**Creating Manual Backup:**
1. **Environments** → Select environment
2. **Backups** → **Create** (Manual tab)
3. Enter label
4. Create

> **Exam Tip**: **Production** environments get automatic daily backups for 28 days. **Sandbox** environments require manual backups. This is frequently tested.

### Copy Environment

Copy entire environment (structure + data):

```
┌─────────────────────────────────────────────────────────────┐
│                    COPY ENVIRONMENT                          │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  Source: Production                                         │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  • All tables and data                               │  │
│  │  • All customizations                                │  │
│  │  • All solutions                                     │  │
│  │  • Security roles (structure)                        │  │
│  └──────────────────────────────────────────────────────┘  │
│                         ↓ COPY                              │
│  Target: Sandbox                                            │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  • Everything copied                                 │  │
│  │  • Connections recreated (need reconfiguration)      │  │
│  │  • Flows disabled (must enable manually)             │  │
│  │  • Users NOT copied (must add separately)            │  │
│  └──────────────────────────────────────────────────────┘  │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

**Copy Options:**

| Option | Includes |
|--------|----------|
| **Everything** | All data + customizations |
| **Customizations and schemas only** | No data, only structure |

**Copy Process:**
1. **Environments** → Select source
2. **Copy**
3. Select target (new or existing sandbox)
4. Choose copy type
5. Confirm (overwrites target if existing)

**After Copy:**
- Flows are **disabled** (must enable manually)
- Connections must be **reconfigured**
- Users must be **added** to environment
- Test thoroughly before using

> **Exam Tip**: After copying, flows are disabled and connections need reconfiguration. This is a safety measure and commonly tested.

### Reset Environment

**Reset** = Delete all data and customizations, start fresh:

- Only available for **Sandbox** environments
- Cannot reset Production
- Retains environment URL and name
- All data permanently deleted
- Customizations removed

**Use Cases:**
- Start over after testing
- Reclaim storage space
- Prepare environment for new project

### Delete Environment

Permanently remove environment:

- Cannot be undone (unless recent backup)
- Releases capacity back to pool
- Users lose access immediately
- 7-day recovery period (soft delete)

**Recovery:**
Within 7 days, can recover deleted environment:
1. **Environments** → **Recover deleted environments**
2. Select environment
3. **Recover**

### Environment Capacity

Each tenant has capacity limits:

```
┌─────────────────────────────────────────────────────────────┐
│                    CAPACITY TYPES                            │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  Database Capacity                                          │
│    • Dataverse table data                                  │
│    • Base capacity: 10 GB (tenant)                         │
│    • Additional: Per user/app licenses                      │
│    • Purchase: Add-on capacity available                    │
│                                                              │
│  File Capacity                                              │
│    • Attachments, images, files                            │
│    • Base capacity: 20 GB (tenant)                         │
│    • Additional: Per user/app licenses                      │
│    • Purchase: Add-on capacity available                    │
│                                                              │
│  Log Capacity                                               │
│    • Audit logs, plugin traces                             │
│    • Base capacity: 2 GB (tenant)                          │
│    • Additional: Per user/app licenses                      │
│    • Purchase: Add-on capacity available                    │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

**Viewing Capacity:**
1. **Resources** → **Capacity**
2. View summary or detailed reports
3. Drill into environment usage

**Managing Capacity:**
- Delete unused environments
- Remove old data
- Reduce audit log retention
- Purchase additional capacity

### Data Policies (DLP)

**Data Loss Prevention Policies** control connector usage:

```
┌─────────────────────────────────────────────────────────────┐
│               DATA LOSS PREVENTION POLICY                    │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  Policy: Sales Production DLP                               │
│                                                              │
│  Business Data Group (Allowed Together)                     │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  • Dataverse                                         │  │
│  │  • SharePoint                                        │  │
│  │  • Office 365 Outlook                                │  │
│  │  • Office 365 Users                                  │  │
│  │  • SQL Server                                        │  │
│  └──────────────────────────────────────────────────────┘  │
│                                                              │
│  Non-Business Data Group (Separate)                        │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  • Twitter                                           │  │
│  │  • Facebook                                          │  │
│  │  • Gmail                                             │  │
│  └──────────────────────────────────────────────────────┘  │
│                                                              │
│  Blocked Group (Cannot Use)                                │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  • Dropbox                                           │  │
│  │  • Google Drive                                      │  │
│  └──────────────────────────────────────────────────────┘  │
│                                                              │
│  Applied to: Sales Production Environment                   │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

**Rules:**
- Apps/flows can use connectors from **same group** together
- Apps/flows **cannot** use connectors from **different groups**
- **Blocked** connectors cannot be used at all

**Creating DLP Policy:**
1. **Data policies** → **New policy**
2. Name and assign environments
3. Classify connectors:
   - **Business** data group
   - **Non-Business** data group
   - **Blocked**
4. Configure connector patterns (optional)
5. Save and apply

> **Exam Tip**: DLP policies prevent data mixing between business and non-business connectors. If Canvas App can't use Twitter + Dataverse together, there's a DLP policy. Commonly tested scenario.

### Analytics

Track environment usage:

**Available Reports:**
- **Power Apps**: Usage by app, user, location
- **Power Automate**: Flow runs, success rates
- **Dataverse**: Storage usage, API calls
- **Connectors**: Connector usage
- **Error logs**: Failed operations

**Accessing Analytics:**
1. **Analytics** section
2. Select report type
3. Filter by environment, date range
4. Export data if needed

### Admin Roles

Three primary admin roles:

| Role | Permissions |
|------|-------------|
| **Global Administrator** | Full control of tenant and all environments |
| **Power Platform Administrator** | Manage environments, DLP policies, resources |
| **Dynamics 365 Administrator** | Manage Dynamics 365 environments only |

**Assigning Admin Roles:**
- Assigned in **Microsoft 365 Admin Center**
- Not in Power Platform Admin Center

### Environment Security Groups

Restrict environment access to specific Azure AD security groups:

**Configuration:**
1. Create Azure AD security group
2. Add users to group
3. In environment settings:
   - **Edit** → **Security group**
   - Select Azure AD group
4. Save

**Effect:**
- Only members can access environment
- Others see "You don't have permission" error
- Overrides individual environment access

**Use Cases:**
- Production environment: Only specific team
- Development environment: Only developers
- Testing environment: QA team only

> **Exam Tip**: Security groups are the ONLY way to restrict environment access. Individual user blocking is not possible. If question asks "prevent specific users from accessing environment", answer is Security Group.

### Common Admin Tasks

#### Moving Apps Between Environments

1. **Export as solution** from source environment
2. **Import solution** to target environment
3. Reconfigure connections
4. Test functionality

#### Managing User Access

1. **Users + permissions** in environment settings
2. Add users manually or via security group
3. Assign security roles
4. Test access

#### Monitoring Environment Health

1. **Analytics** for usage trends
2. **Capacity** for storage limits
3. **Resources** for installed apps/flows
4. **Audit logs** for changes

#### Troubleshooting

| Issue | Solution |
|-------|----------|
| Users can't access | Check security group, verify licenses |
| Out of capacity | Delete old data, purchase capacity |
| Flows disabled after copy | Manually enable and reconfigure connections |
| DLP blocking connector | Adjust policy or request exception |
| Slow performance | Check API call limits, optimize queries |

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
9. **Business Rules cannot reference related tables** - use JavaScript or Power Automate for cross-table logic
10. **Entity-scoped business rules run server-side** - work in all interfaces including Canvas Apps and API
11. **Form-scoped business rules are client-only** - don't work via API or in Canvas Apps
12. **Managed solutions can be uninstalled** - unmanaged solutions cannot
13. **DEV uses unmanaged, TEST/PROD use managed** - never mix them in the same environment
14. **Unmanaged customizations override managed** - creates solution layers
15. **Managed properties control customization** - set before exporting as managed
16. **Solution upgrade removes old components** - update keeps them

---

*Continue to the next section: [Model-Driven Apps Deep Dive](./PL-200-ModelDrivenApps-DeepDive.md)*
