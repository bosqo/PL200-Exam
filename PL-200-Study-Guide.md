# Microsoft PL-200 Certification Study Guide
## Power Platform Functional Consultant

---

## Table of Contents
1. [Exam Overview](#exam-overview)
2. [Configure Microsoft Dataverse (25-30%)](#1-configure-microsoft-dataverse-25-30)
3. [Create Apps Using Power Apps (25-30%)](#2-create-apps-using-power-apps-25-30)
4. [Create and Manage Logic and Process Automation (25-30%)](#3-create-and-manage-logic-and-process-automation-25-30)
5. [Manage Environments (15-20%)](#4-manage-environments-15-20)
6. [Special Topics & Exam Tips](#special-topics--exam-tips)

---

## Exam Overview

### About the Exam
- **Exam Code**: PL-200
- **Title**: Microsoft Power Platform Functional Consultant
- **Passing Score**: 700 (out of 1000)
- **Duration**: Approximately 120 minutes
- **Question Types**: Multiple choice, drag-and-drop, case studies, hot spots
- **Last Updated**: December 26, 2024

### Role Description
A Microsoft Power Platform Functional Consultant is responsible for:
- Creating and configuring apps, automations, and solutions
- Acting as a liaison between users and the implementation team
- Promoting utilization of solutions within an organization
- Performing discovery, engaging stakeholders, capturing requirements
- Implementing solution components including custom process automation and visualizations

---

## 1. Configure Microsoft Dataverse (25-30%)

### 1.1 Data Model Management

#### Tables (Entities)
Tables are the foundation of Dataverse. Key concepts:

| Table Type | Description | Use Case |
|------------|-------------|----------|
| **Standard Tables** | Pre-built by Microsoft (Account, Contact, etc.) | Common business scenarios |
| **Custom Tables** | Created by you | Specific business needs |
| **Virtual Tables** | Data from external sources without replication | Integration scenarios |
| **Activity Tables** | Special tables for tracking interactions | Tasks, emails, appointments |

#### Table Ownership Types
**Critical Exam Topic**: This determines security model behavior.

| Ownership Type | Description | Security Impact |
|----------------|-------------|-----------------|
| **User or Team owned** | Records owned by users/teams | Security roles control access by ownership hierarchy |
| **Organization owned** | No owner assigned | All users with table access can see all records |

> **Exam Tip**: When a question asks about configuring security roles for a business unit or restricting access based on ownership, the answer is **User or Team owned**.

#### Columns (Fields)
Types of columns you must know:

| Column Type | Description | Key Points |
|-------------|-------------|------------|
| **Text** | Single/Multi-line text | Max 4000 characters (multi-line) |
| **Choice** | Predefined options (Option Set) | Local or Global choices |
| **Lookup** | Reference to another table | Creates 1:N relationship |
| **Number** | Whole number, Decimal, Currency | Precision settings matter |
| **Date/Time** | Date only or Date and Time | Timezone behavior options |
| **Yes/No** | Two-option field | Boolean logic |
| **File/Image** | Binary data storage | Size limits apply |
| **Autonumber** | Auto-generated sequential number | Cannot be modified |
| **Calculated** | Computed from other fields | Being replaced by Formula columns |
| **Rollup** | Aggregates data from related records | Updates every hour |
| **Formula** | Power Fx based calculations | Real-time, modern replacement for calculated |

#### Local vs Global Choices
**Frequently Asked on Exam**:

| Type | Scope | When to Use |
|------|-------|-------------|
| **Local Choice** | Single table only | Unique to one table |
| **Global Choice** | Reusable across tables | Standard values used in multiple tables |

> **Exam Tip**: If you need the same set of options (like Status, Priority, etc.) across multiple tables, create a **Global Choice**.

#### Calculated vs Rollup vs Formula Columns

| Feature | Calculated | Rollup | Formula |
|---------|------------|--------|---------|
| **Calculation Time** | Real-time | Every hour (async) | Real-time |
| **Data Source** | Same record | Related records | Same record |
| **Technology** | Legacy | Current | Power Fx (Modern) |
| **Null Handling** | null + x = null | Aggregates skip nulls | null treated as 0 |

> **Exam Tip**: Rollup columns update approximately every hour. If real-time aggregation is needed, use Power Automate.

#### Relationships

| Relationship Type | Description | Example |
|-------------------|-------------|---------|
| **One-to-Many (1:N)** | Parent has many children | Account → multiple Contacts |
| **Many-to-One (N:1)** | Child perspective of 1:N | Contact belongs to Account |
| **Many-to-Many (N:N)** | Records can relate to multiple records | Products ↔ Categories |
| **Polymorphic Lookup** | Lookup to multiple table types | Customer (Account OR Contact) |

**Many-to-Many Limitations** (Exam Favorite):
- Cannot add custom columns to the relationship (intersect) table
- Cannot trigger workflows when records are associated
- The relationship table is not visible in the UI

#### Alternate Keys
Used to enforce uniqueness and for integration scenarios:
- Prevent duplicate records at the platform level
- Support upsert operations in integrations
- Can be composite (multiple columns)

> **Exam Tip**: When asked about preventing duplicate rows in a table, **Alternate Key** is often the answer.

### 1.2 Manage Dataverse

#### Data Import/Export
| Tool | Purpose |
|------|---------|
| **Import Data Wizard** | Simple CSV/Excel imports |
| **Configuration Migration Tool** | Copy reference/configuration data between environments |
| **Dataflows** | Advanced data transformation and loading |

> **Exam Tip**: To copy reference data (lookup tables, configuration) between environments, use the **Configuration Migration Tool**.

#### Duplicate Detection
- Create **Duplicate Detection Rules** to identify duplicates
- Rules check when records are created, updated, or imported
- Run **Duplicate Detection Jobs** to find existing duplicates

### 1.3 Configure Security Settings

#### Security Hierarchy

```
Tenant
  └── Environment
        └── Business Unit (Root)
              └── Business Unit (Child)
                    └── Team
                          └── User
```

#### Security Roles
Define CRUD + Assign + Share permissions at different access levels:

| Access Level | Icon | Description |
|--------------|------|-------------|
| **None** | ○ | No access |
| **User (Basic)** | ● | Own records only |
| **Business Unit (Local)** | ◉ | Own business unit |
| **Parent: Child (Deep)** | ◎ | BU and all child BUs |
| **Organization (Global)** | ⊕ | Entire organization |

#### Team Types
**Critical Exam Topic**:

| Team Type | Owns Records? | Security Roles? | Use Case |
|-----------|---------------|-----------------|----------|
| **Owner Team** | Yes | Yes | Shared record ownership |
| **Access Team** | No | No | Dynamic, ad-hoc sharing |
| **AAD Group Team** | Yes | Yes | Sync with Azure AD groups |

> **Exam Tip**: **Access Teams** do NOT own records and do NOT have security roles. Members get access through their individual roles plus team membership privileges.

#### Field-Level Security
- Controls visibility and editability of specific columns
- Requires creating Field Security Profiles
- Users must be added to profiles to access secured fields

#### Hierarchy Security
- Position-based or Manager-based hierarchy
- Provides read/write access to records owned by subordinates

---

## 2. Create Apps Using Power Apps (25-30%)

### 2.1 Model-Driven Apps

#### Form Types
**Frequently Asked on Exam**:

| Form Type | Purpose | Key Characteristics |
|-----------|---------|---------------------|
| **Main** | Primary data entry | Supports timeline, BPF, tabs, sections |
| **Quick Create** | Rapid record creation | Single tab, appears as panel, minimal fields |
| **Quick View** | Display related record data | Read-only, embedded in main forms |
| **Card** | Compact display in views | Mobile-friendly, dashboard use |

> **Exam Tip**: Match form types to scenarios:
> - Timeline or Business Process Flow needed → **Main Form**
> - Display data on interactive dashboard → **Card Form**
> - Mobile-friendly with minimal fields → **Quick Create**
> - Show parent record fields → **Quick View**

#### Views
| View Type | Description |
|-----------|-------------|
| **System Views** | Created by admins, available to all users |
| **Personal Views** | Created by users for themselves |
| **Public Views** | Shared with specific security roles |

**Creating Views**:
- Use the **Maker Portal** (modern way)
- Define filter criteria and column selection
- Set sort order

> **Exam Tip**: When asked where to create a view without custom code, the answer is **Maker Portal**.

#### Business Rules
**Scope Options** (Exam Favorite):

| Scope | Applies To |
|-------|------------|
| **Entity (Table)** | All forms AND canvas apps |
| **All Forms** | All model-driven app forms |
| **Specific Form** | Only the selected form |

> **Exam Tip**: For canvas apps, business rules must have **Entity** scope. Canvas apps don't execute client-side business rules, but Entity-scoped rules execute server-side in Dataverse.

#### Charts and Dashboards
- **System Dashboards**: Shared with all users
- **Personal Dashboards**: User-specific
- **Interactive Dashboards**: Filter across charts and lists

### 2.2 Canvas Apps

#### Variables

| Variable Type | Scope | Function to Create |
|---------------|-------|-------------------|
| **Global Variable** | Entire app | `Set()` |
| **Context Variable** | Current screen only | `UpdateContext()` |
| **Collection** | Entire app (tables) | `Collect()`, `ClearCollect()` |

> **Exam Tip**:
> - To pass data between screens → `Navigate()` with context variables
> - To update table rows independently → Use **Collections**
> - Screen-specific data → **Context Variable**

#### Collections Functions

| Function | Purpose |
|----------|---------|
| `Collect()` | Add records to a collection |
| `ClearCollect()` | Clear and add records |
| `Remove()` | Remove specific records |
| `Clear()` | Remove all records |
| `Patch()` | Create or update records |

#### Delegation
**Critical Concept**: Delegation determines what data processing happens on the server vs. client.

**Delegable Functions** (can process >500 records):
- Filter, Sort, SortByColumns
- Search (SharePoint only)
- Lookup, First (with filtering)

**Non-Delegable Functions** (limited to local data):
- CountRows, Sum, Average (on filtered data)
- First, Last (without delegable filter)
- AddColumns, DropColumns

> **Exam Tip**: Default delegation limit is 500 records (can increase to 2000). Non-delegable operations work on only the first 500 records!

#### Gallery Controls
- Display list of records
- Use `Reset()` function to reset to default values
- Cannot reset controls inside a Gallery from outside

> **Exam Tip**: To clear all selections in a gallery with checkboxes:
> 1. Store selections in a collection (`Collect()` on OnCheck, `Remove()` on OnUncheck)
> 2. Use `Clear(CollectionName)` on button click

### 2.3 Power Pages (Portals)

#### Security Model

```
Web Role
  └── Defines what role a user has
        └── Table Permissions (what data they can access)
              └── CRUD + Append/AppendTo permissions
```

#### Key Concepts

| Component | Purpose |
|-----------|---------|
| **Web Role** | Group users with similar permissions |
| **Table Permissions** | Define CRUD access to Dataverse data |
| **Page Permissions** | Control page visibility |
| **Authenticated Users Role** | Auto-assign role on login |

> **Exam Tip**: To automatically grant access to authenticated users:
> 1. Create a Web Role
> 2. Set "Authenticated Users Role" to **Yes**
> 3. Assign Table Permissions to that role

---

## 3. Create and Manage Logic and Process Automation (25-30%)

### 3.1 Cloud Flows (Power Automate)

#### Trigger Types

| Trigger Type | Description |
|--------------|-------------|
| **Automated** | Event-based (record created, email received) |
| **Instant** | Manual button press |
| **Scheduled** | Time-based (recurring) |

#### Key Actions and Controls

| Control | Purpose | Use When |
|---------|---------|----------|
| **Condition** | If/then logic | Evaluate each item individually |
| **Switch** | Multiple cases | Multiple outcomes based on one value |
| **Apply to each** | Loop through items | Process collections/arrays |
| **Do until** | Loop until condition true | Retry logic, polling |
| **Scope** | Group actions | Error handling, organization |

> **Exam Tip**: Use **Scope** for error handling. Configure "Run After" on a parallel scope to catch failures.

#### Connection References
**Critical for ALM**:
- Store connection information separately from the flow
- Required when deploying flows in solutions
- Must be configured in target environment after import

> **Exam Tip**: To move flows between environments with proper connections:
> - Export as part of a **solution**
> - Use **connection references** (not embedded connections)

#### Error Handling Patterns
1. **Scope with Run After**: Add a scope after your main scope, configure to run after failure
2. **Try-Catch Pattern**: Use parallel branches with different "Run After" settings
3. **Retry Policies**: Built-in for HTTP actions

### 3.2 Business Process Flows (BPF)

#### Components

| Component | Description |
|-----------|-------------|
| **Stage** | Major phase in the process |
| **Step** | Individual field/action within a stage |
| **Branch** | Conditional path based on data |
| **Workflow** | Action that runs on stage entry/exit |

#### Key Features
- Guide users through a standardized process
- Can span multiple tables (max 5)
- Create a hidden BPF table for tracking
- Security controlled by BPF security roles

> **Exam Tip**: BPFs create their own table (`<BPF Name>` table). This table tracks process instances and enables reporting on process metrics.

### 3.3 Classic Dataverse Workflows

#### Types

| Type | Execution | Use Case |
|------|-----------|----------|
| **Background (Async)** | Server-side, delayed | Large operations, no UI blocking |
| **Real-time (Sync)** | Immediate, blocks UI | Immediate data validation/transformation |

#### Triggering Options
- On Create
- On Status Change
- On Assign
- On Field Change
- On Demand
- As a Child Process

> **Exam Tip**: For a workflow that must run **immediately** when triggered, configure it as **Real-time** (synchronous).

### 3.4 Low-Code Logic

#### Power Fx
The programming language for Power Platform:
- Used in Canvas Apps
- Used in Formula columns
- Used in Dataverse calculated columns (new)

#### Common Functions

| Function | Purpose |
|----------|---------|
| `If()` | Conditional logic |
| `Switch()` | Multiple conditions |
| `Filter()` | Filter records |
| `LookUp()` | Find single record |
| `Patch()` | Create/Update records |
| `Collect()` | Add to collections |
| `Navigate()` | Move between screens |

---

## 4. Manage Environments (15-20%)

### 4.1 Environment Types

| Environment Type | Purpose | Key Characteristics |
|------------------|---------|---------------------|
| **Production** | Live business operations | Full features, high availability |
| **Sandbox** | Testing and UAT | Can be reset, copy from production |
| **Developer** | Personal development | One per user, included with dev plan |
| **Trial** | Evaluation | 30-day limit |
| **Default** | Auto-created | Shared by all tenant users |

> **Exam Tip**: Security groups CANNOT be assigned to **Default** and **Developer** environments.

### 4.2 Application Lifecycle Management (ALM)

#### Solution Types
**Most Frequently Asked Topic in Environments Section**:

| Solution Type | Purpose | Can Edit? |
|---------------|---------|-----------|
| **Unmanaged** | Development | Yes - all components editable |
| **Managed** | Deployment | No - locked, cannot edit directly |

#### ALM Best Practices

```
DEV Environment         TEST/UAT/PROD Environments
    │                           │
Unmanaged Solution ──export as managed──► Managed Solution
    │                           │
(Make changes here)      (Cannot edit directly)
```

> **Key Exam Points**:
> 1. **Always develop in unmanaged** solutions
> 2. **Export as managed** for higher environments
> 3. **Cannot convert** unmanaged to managed - must export
> 4. **Deleting unmanaged solution** container does NOT delete components
> 5. **To edit managed components**, add them to an unmanaged solution first

#### Managed Properties
Control what can be customized in managed solutions:
- Can customize
- Can delete
- Can modify display names
- And more...

> **Exam Tip**: Managed properties are set **before exporting** from an unmanaged solution. You configure them in the unmanaged layer.

#### Solution Checker
- Analyzes solutions for issues
- Identifies performance problems
- Finds deprecated features
- Checks for best practices

### 4.3 Environment Variables

| Component | Purpose |
|-----------|---------|
| **Definition** | The variable structure/schema |
| **Current Value** | Environment-specific value |
| **Default Value** | Fallback if no current value |

> **Exam Tip**: Environment variables allow you to have different configuration values per environment without modifying the solution.

---

## Special Topics & Exam Tips

### Power Virtual Agents / Copilot Studio

#### Key Concepts for Exam

| Concept | Description |
|---------|-------------|
| **Topics** | Conversation paths triggered by phrases |
| **Entities** | Data extracted from user input |
| **Skills** | Integration with Bot Framework bots |
| **Fallback Topic** | Handles unrecognized input |

#### Sharing and Publishing

| Action | Method |
|--------|--------|
| **Share for testing** | Demo website |
| **Collaborate on building** | Share with individuals (not groups) |
| **Publish to users** | Channels (Teams, website, etc.) |

> **Exam Tip**:
> - SSO is supported on **Microsoft Teams** and **custom websites**
> - To transfer to another bot → Use **Skills**
> - For unrecognized input → Configure **Fallback topic** or escalate to agent

### AI Builder
- Document processing
- Prediction models
- Object detection
- Text classification

### Common Exam Traps

1. **Business Rules for Canvas Apps**: Must have **Entity** scope, not form-specific
2. **Access Teams vs Owner Teams**: Access teams DON'T own records
3. **Rollup vs Calculated**: Rollup updates hourly, calculated is real-time
4. **Managed vs Unmanaged**: Can't edit managed directly
5. **Delegation Limits**: Non-delegable operations only work on first 500 rows
6. **Reset() Function**: Cannot reset controls inside a Gallery from outside
7. **Environment Variables**: Default value is fallback, Current value is environment-specific

### Integration Topics

#### SharePoint Integration
- Use SharePoint connector in Power Automate
- Canvas apps can connect directly
- Model-driven apps use document management

#### Outlook Integration
- Server-side sync for model-driven apps
- Power Automate for advanced scenarios

#### Teams Integration
- Embed apps in Teams
- Power Virtual Agents chatbots in Teams
- Notifications via Teams connector

### Quick Reference: When to Use What

| Scenario | Solution |
|----------|----------|
| Enforce unique records | Alternate Key |
| Copy reference data | Configuration Migration Tool |
| Real-time calculations | Formula Column |
| Aggregate child records | Rollup Column |
| User guides through process | Business Process Flow |
| Automate approvals | Power Automate Cloud Flow |
| Show related record data in form | Quick View Form |
| Rapid record entry | Quick Create Form |
| Pass data between screens | Context Variable with Navigate() |
| Store table data in app | Collection |
| Dynamic team membership | Access Team |
| Role-based team ownership | Owner Team |
| Personal development | Developer Environment |
| Testing before production | Sandbox Environment |
| Deploy to production | Managed Solution |

---

## Study Resources

### Official Microsoft Resources
- [Microsoft Learn - PL-200 Study Guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/pl-200)
- [Microsoft Learn - Power Platform Training](https://learn.microsoft.com/en-us/training/powerplatform/)
- [Exam Readiness Zone Videos](https://learn.microsoft.com/en-us/shows/exam-readiness-zone/)
- [Microsoft Practice Assessment](https://learn.microsoft.com/en-us/credentials/certifications/exams/pl-200/practice/assessment?assessment-type=practice&assessmentId=75)

### Hands-On Practice
- Power Apps Community Plan (free)
- Microsoft 365 Developer Program
- Microsoft Learn Sandbox environments

### Tips for Exam Day
1. Read questions carefully - look for key words like "minimize effort" or "without code"
2. Eliminate obviously wrong answers first
3. For case studies, read the requirements before the scenario
4. Flag difficult questions and return to them
5. Time management: ~90 seconds per question

---

**Good luck with your PL-200 exam!**
