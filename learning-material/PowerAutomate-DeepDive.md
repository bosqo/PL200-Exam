# Power Automate - Complete Deep Dive
## PL-200 Exam Preparation

---

## Table of Contents
1. [What is Power Automate?](#what-is-power-automate)
2. [Flow Types](#flow-types)
3. [Triggers Deep Dive](#triggers-deep-dive)
4. [Actions and Operations](#actions-and-operations)
5. [Control Flow - Conditions and Switches](#control-flow---conditions-and-switches)
6. [Loops - Apply to Each and Do Until](#loops---apply-to-each-and-do-until)
7. [Expressions and Functions](#expressions-and-functions)
8. [Variables](#variables)
9. [Error Handling and Scope](#error-handling-and-scope)
10. [Child Flows and Flow Management](#child-flows-and-flow-management)
11. [Approvals](#approvals)
12. [Integration with Dataverse](#integration-with-dataverse)
13. [Desktop Flows (RPA)](#desktop-flows-rpa)
14. [Solutions and ALM](#solutions-and-alm)
15. [Performance and Best Practices](#performance-and-best-practices)
16. [Monitoring and Troubleshooting](#monitoring-and-troubleshooting)
17. [Common Exam Scenarios](#common-exam-scenarios)

---

## What is Power Automate?

### The Automation Platform

Power Automate (formerly Microsoft Flow) is the **workflow automation** platform in Power Platform:

```
┌─────────────────────────────────────────────────────────────────┐
│                   POWER AUTOMATE ARCHITECTURE                    │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌────────────────┐      ┌────────────────┐                    │
│  │  Cloud Flows   │      │ Desktop Flows  │                    │
│  │  (API-based)   │      │  (RPA/UI)      │                    │
│  └────────┬───────┘      └────────┬───────┘                    │
│           │                       │                             │
│  ┌────────┴───────────────────────┴───────┐                    │
│  │          Connector Platform             │                    │
│  │  (1000+ connectors to apps/services)   │                    │
│  └────────────────────────────────────────┘                    │
│           │                                                      │
│  ┌────────┴───────────────────────────────┐                    │
│  │         Workflow Engine                 │                    │
│  │  • Triggers  • Actions  • Conditions    │                    │
│  │  • Loops     • Variables • Error Logic  │                    │
│  └────────────────────────────────────────┘                    │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Key Benefits

| Feature | Description |
|---------|-------------|
| **No-Code/Low-Code** | Visual designer for building automation |
| **1000+ Connectors** | Connect to Microsoft and third-party services |
| **Triggers** | Start flows automatically based on events |
| **Cross-Platform** | Works across cloud services and on-premises |
| **AI Builder Integration** | Use AI models in flows |
| **Approvals** | Built-in approval workflows |

### Use Cases

- **Notifications**: Send emails/Teams messages when events occur
- **Data Sync**: Copy data between systems
- **Approvals**: Request and track approvals
- **Scheduled Jobs**: Run tasks on a schedule
- **Business Processes**: Automate multi-step workflows
- **RPA**: Automate desktop applications

---

## Flow Types

### Cloud Flows

Cloud flows run in the **cloud** using APIs:

#### 1. Automated Cloud Flows

**Triggered by events** (most common):

```
┌────────────────────────────────────────────────────────────┐
│              AUTOMATED FLOW PATTERN                         │
├────────────────────────────────────────────────────────────┤
│                                                             │
│  Trigger: When a record is created (Dataverse)            │
│     ↓                                                       │
│  Condition: If Priority = "High"                           │
│     ↓                                                       │
│  Action: Send email to manager                             │
│     ↓                                                       │
│  Action: Create task in Planner                            │
│                                                             │
└────────────────────────────────────────────────────────────┘
```

**Example Triggers:**
- When a row is added, modified, or deleted (Dataverse)
- When a file is created (SharePoint, OneDrive)
- When a new email arrives (Outlook)
- When an item is created or modified (SharePoint)

#### 2. Instant Cloud Flows (Manual Triggers)

**Triggered manually** by user:

```
┌────────────────────────────────────────────────────────────┐
│               INSTANT FLOW PATTERN                          │
├────────────────────────────────────────────────────────────┤
│                                                             │
│  Trigger: Manually trigger a flow                          │
│     ↓                                                       │
│  Input: User selects options                               │
│     ↓                                                       │
│  Action: Perform operations based on input                 │
│     ↓                                                       │
│  Output: Return results to user                            │
│                                                             │
└────────────────────────────────────────────────────────────┘
```

**Trigger Types:**
- **For a selected row** (Dataverse) - Run from record
- **For a selected file** (SharePoint) - Run from file
- **Manually trigger a flow** - From Power Automate mobile/web
- **Power Apps** - Called from Power App
- **Power BI** - Triggered from Power BI

#### 3. Scheduled Cloud Flows

**Run on a schedule**:

```
┌────────────────────────────────────────────────────────────┐
│              SCHEDULED FLOW PATTERN                         │
├────────────────────────────────────────────────────────────┤
│                                                             │
│  Trigger: Recurrence (Every day at 8:00 AM)               │
│     ↓                                                       │
│  Action: Get rows from Dataverse                           │
│     ↓                                                       │
│  Action: Process data                                      │
│     ↓                                                       │
│  Action: Send summary email                                │
│                                                             │
└────────────────────────────────────────────────────────────┘
```

**Schedule Options:**
- Minute, Hour, Day, Week, Month
- Specific time (e.g., 8:00 AM)
- Multiple times per day
- Timezone selection

### Desktop Flows (RPA)

Desktop flows automate **desktop applications** using:
- **UI automation** (clicking buttons, entering text)
- **Screen scraping** (reading values from screen)
- **Keyboard/mouse simulation**

**Use Cases:**
- Legacy applications without APIs
- Desktop applications
- Citrix/remote desktop scenarios
- Local file operations

> **Exam Tip**: Cloud flows use APIs/connectors. Desktop flows use UI automation. Choose based on whether system has an API.

### Business Process Flows

Covered in separate [Business Process Flows Deep Dive](./BPF-DeepDive.md).

---

## Triggers Deep Dive

### Understanding Triggers

Triggers determine **when a flow starts**:

```
┌────────────────────────────────────────────────────────────┐
│                     TRIGGER TYPES                           │
├────────────────────────────────────────────────────────────┤
│                                                             │
│  AUTOMATED (Event-Based)                                   │
│  ┌────────────────────────────────────────────────────┐   │
│  │  • When a row is created/updated (Dataverse)       │   │
│  │  • When a file is created (SharePoint)             │   │
│  │  • When an email arrives (Outlook)                 │   │
│  │  • When a form response is submitted (Forms)       │   │
│  └────────────────────────────────────────────────────┘   │
│                                                             │
│  INSTANT (Manual)                                          │
│  ┌────────────────────────────────────────────────────┐   │
│  │  • Manually trigger a flow                         │   │
│  │  • For a selected row (Dataverse)                  │   │
│  │  • Power Apps button                               │   │
│  └────────────────────────────────────────────────────┘   │
│                                                             │
│  SCHEDULED (Time-Based)                                    │
│  ┌────────────────────────────────────────────────────┐   │
│  │  • Recurrence (daily, weekly, monthly)             │   │
│  │  • Specific times and intervals                    │   │
│  └────────────────────────────────────────────────────┘   │
│                                                             │
└────────────────────────────────────────────────────────────┘
```

### Dataverse Triggers (Critical for Exam)

#### When a row is added, modified, or deleted

Most important trigger for PL-200:

**Configuration Options:**

| Setting | Options | Purpose |
|---------|---------|---------|
| **Change type** | Added, Modified, Deleted, Added or Modified, Added or Deleted, Modified or Deleted | What event triggers flow |
| **Table name** | Any Dataverse table | Which table to monitor |
| **Scope** | Organization, Business Unit, User | Which records to monitor |
| **Filter rows** | OData filter | Narrow trigger criteria |
| **Select columns** | Column list | Only trigger when specific columns change |

**Scope Explained:**

| Scope | Monitors | Use Case |
|-------|----------|----------|
| **Organization** | All records | System-wide automation |
| **Business Unit** | Records owned by BU | Department-specific |
| **User** | Records owned by user | User-specific automation |

**Filter Rows (OData):**

```
// Only high-value opportunities
estimatedvalue gt 100000

// Only active accounts in specific city
statuscode eq 1 and address1_city eq 'Seattle'

// Multiple conditions
prioritycode eq 1 and statecode eq 0
```

**Select Columns** (Trigger Optimization):

```
Only trigger when these columns change:
- statuscode
- prioritycode
- estimatedvalue
```

> **Exam Tip**: Use "Select columns" to avoid triggering on every update. Flow only runs when specified columns change. This is a performance optimization.

#### When a row is added

- Simpler version
- Only for new records
- No "Modified" or "Deleted" option

### SharePoint Triggers

#### When an item is created

- New list item only
- No modifications

#### When an item is created or modified

- New OR updated items
- Most common SharePoint trigger

#### When a file is created or modified (properties only)

- File metadata changes
- Doesn't download file content

> **Exam Tip**: For file operations, "properties only" version is faster. Use if you don't need file content immediately.

### Recurrence Trigger

Schedule flows to run periodically:

**Configuration:**

| Setting | Options |
|---------|---------|
| **Interval** | Number (1, 2, 3...) |
| **Frequency** | Second, Minute, Hour, Day, Week, Month |
| **Time zone** | Any timezone |
| **Start time** | Specific date/time |
| **At these hours** | 0-23 (for Day frequency) |
| **At these minutes** | 0-59 |
| **On these days** | Mon-Sun (for Week frequency) |

**Common Patterns:**

```
Every day at 9:00 AM:
- Frequency: Day
- Interval: 1
- At these hours: 9
- At these minutes: 0

Every Monday and Friday at 8:00 AM and 5:00 PM:
- Frequency: Week
- Interval: 1
- On these days: Monday, Friday
- At these hours: 8, 17
- At these minutes: 0
```

> **Exam Tip**: Recurrence is the ONLY scheduled trigger for cloud flows. Desktop flows can use other scheduling options through the gateway.

### Manual Triggers

#### Manually trigger a flow

- Generic manual trigger
- Can add inputs (text, yes/no, file, email, etc.)
- Can return outputs (Respond to Power App or HTTP)

**Input Types:**

| Type | Use Case |
|------|----------|
| **Text** | Short text input |
| **Yes/No** | Boolean choice |
| **File** | Upload file |
| **Email** | Email address |
| **Number** | Numeric input |
| **Date** | Date picker |

#### For a selected row (Dataverse)

- Appears in Dataverse form
- User selects record and runs flow
- Flow receives record context

```
Location in Dataverse:
Record → Flow menu → Run flow → [Your Flow Name]
```

#### Power Apps trigger

- Called from Canvas App
- Can pass parameters from app
- Can return data to app

```powerapps
// In Canvas App button
PowerAppsFlow.Run(TextInput1.Text, Dropdown1.Selected.Value)

// In flow, receive parameters
triggerBody()['text']
triggerBody()['dropdown']

// Return response
Respond to a PowerApp or flow → Output values
```

---

## Actions and Operations

### Common Action Categories

#### Data Operations

| Action | Purpose | Example Use |
|--------|---------|-------------|
| **Add row (Dataverse)** | Create record | Create new account |
| **Get row (Dataverse)** | Retrieve record | Get account details |
| **Update row (Dataverse)** | Modify record | Update account status |
| **Delete row (Dataverse)** | Remove record | Delete old records |
| **List rows (Dataverse)** | Query multiple records | Get all active accounts |
| **Relate rows (Dataverse)** | Create N:N relationship | Associate contact with account |
| **Unrelate rows (Dataverse)** | Remove N:N relationship | Remove contact association |

#### Office 365 Actions

| Action | Purpose |
|--------|---------|
| **Send an email (V2)** | Send email via Outlook |
| **Get user profile (V2)** | Get Azure AD user info |
| **Send an HTTP request to SharePoint** | Custom SharePoint operations |
| **Create item (SharePoint)** | Add list item |
| **Get items (SharePoint)** | Query list items |

#### Notifications

| Action | Purpose |
|--------|---------|
| **Send an email notification (V3)** | Simple email via Power Automate |
| **Post message in chat or channel (Teams)** | Send Teams message |
| **Send a push notification (Power Apps Notification)** | Mobile notification |

#### Data Transformation

| Action | Purpose |
|--------|---------|
| **Compose** | Transform or format data |
| **Parse JSON** | Convert JSON to usable outputs |
| **Select** | Map array properties |
| **Filter array** | Filter array based on condition |
| **Join** | Combine array into string |

### Get Row vs List Rows

Critical distinction for the exam:

| Aspect | Get Row | List Rows |
|--------|---------|-----------|
| **Returns** | Single record | Multiple records (array) |
| **When to use** | You know the ID | Query by criteria |
| **Requires** | Row ID (GUID) | Filter query (optional) |
| **Output** | Direct properties | Array (needs loop) |
| **Performance** | Faster | Slower (query) |

**Get Row Example:**
```
Get row by ID
  Table name: Accounts
  Row ID: <GUID from trigger>

Access properties directly:
  Account Name: {Account Name}
  City: {City}
```

**List Rows Example:**
```
List rows
  Table name: Accounts
  Filter rows: statuscode eq 1
  Select columns: accountid,name,revenue

Returns array - must use Apply to each:
  Apply to each: outputs('List_rows')?['value']
    Action: Update row
```

> **Exam Tip**: If you have the record ID, use "Get row" (faster). If you need to query, use "List rows" but remember it returns an array.

### Row ID in Dataverse

Dataverse record IDs are **GUIDs**:
- Unique identifier for each record
- Format: `12345678-1234-1234-1234-123456789abc`
- Available in trigger outputs
- Required for Get/Update/Delete operations

**Accessing Row ID:**

```
From trigger:
  {Primary Key Column Name}

Example:
  For Accounts table: {accountid}
  For Contacts table: {contactid}
  For Custom table (new_project): {new_projectid}
```

### Filter Rows (OData Queries)

Used in "List rows" and triggers:

**Operators:**

| Operator | Meaning | Example |
|----------|---------|---------|
| `eq` | Equals | `statuscode eq 1` |
| `ne` | Not equals | `statuscode ne 2` |
| `gt` | Greater than | `revenue gt 100000` |
| `ge` | Greater or equal | `revenue ge 100000` |
| `lt` | Less than | `createdon lt 2026-01-01` |
| `le` | Less or equal | `createdon le 2026-01-01` |
| `and` | Logical AND | `statuscode eq 1 and revenue gt 100000` |
| `or` | Logical OR | `statuscode eq 1 or statuscode eq 2` |

**Functions:**

| Function | Purpose | Example |
|----------|---------|---------|
| `startswith` | Starts with text | `startswith(name, 'Contoso')` |
| `endswith` | Ends with text | `endswith(emailaddress1, '@contoso.com')` |
| `contains` | Contains text | `contains(name, 'Corp')` |

**Examples:**

```
// Active accounts with high revenue
statuscode eq 1 and revenue gt 100000

// Created this year
createdon ge 2026-01-01

// Name starts with 'A'
startswith(name, 'A')

// Multiple choice values
statuscode eq 1 or statuscode eq 2

// Date range
createdon ge 2026-01-01 and createdon lt 2026-02-01
```

### Select Columns

Optimize performance by only retrieving needed columns:

```
Select columns: accountid,name,revenue,statuscode

Without: Returns all columns (slower)
With: Returns only specified columns (faster)
```

> **Exam Tip**: Always use "Select columns" and "Filter rows" to optimize performance. This appears in scenario questions about slow flows.

---

## Control Flow - Conditions and Switches

### Condition Actions

Branch logic based on conditions:

```
┌────────────────────────────────────────────────────────────┐
│                    CONDITION STRUCTURE                      │
├────────────────────────────────────────────────────────────┤
│                                                             │
│  Condition: Revenue is greater than 100000                 │
│     ├─ Yes                                                  │
│     │   └─ Send email to VP                                │
│     │                                                       │
│     └─ No                                                   │
│         └─ Send email to Manager                           │
│                                                             │
└────────────────────────────────────────────────────────────┘
```

**Condition Types:**

| Operator | Purpose | Example |
|----------|---------|---------|
| **is equal to** | Exact match | Status is equal to "Active" |
| **is not equal to** | Not match | Status is not equal to "Inactive" |
| **is greater than** | Numeric comparison | Revenue is greater than 100000 |
| **is less than** | Numeric comparison | Days Overdue is less than 30 |
| **contains** | Text contains | Email contains "@contoso.com" |
| **does not contain** | Text doesn't contain | Name does not contain "Test" |
| **is empty** | Null/blank check | Description is empty |
| **is not empty** | Has value | Phone is not empty |

**Advanced Mode (Expression):**

```
@equals(triggerOutputs()?['body/statuscode'], 1)

@and(
  equals(triggerOutputs()?['body/statuscode'], 1),
  greater(triggerOutputs()?['body/revenue'], 100000)
)
```

### Switch Control

Multiple branches based on value:

```
┌────────────────────────────────────────────────────────────┐
│                     SWITCH STRUCTURE                        │
├────────────────────────────────────────────────────────────┤
│                                                             │
│  Switch: Priority                                          │
│     ├─ Case: High                                          │
│     │   └─ Assign to VP                                    │
│     │                                                       │
│     ├─ Case: Medium                                        │
│     │   └─ Assign to Manager                               │
│     │                                                       │
│     ├─ Case: Low                                           │
│     │   └─ Assign to Coordinator                           │
│     │                                                       │
│     └─ Default                                             │
│         └─ Send error notification                         │
│                                                             │
└────────────────────────────────────────────────────────────┘
```

**When to use:**
- Multiple specific values to check
- Cleaner than nested conditions
- Each case is a distinct path

> **Exam Tip**: Use Switch when checking ONE field for multiple values. Use Condition for complex logic with multiple fields.

---

## Loops - Apply to Each and Do Until

### Apply to Each

Iterate through **array of items**:

```
┌────────────────────────────────────────────────────────────┐
│                  APPLY TO EACH PATTERN                      │
├────────────────────────────────────────────────────────────┤
│                                                             │
│  List rows (Accounts) → Returns array of accounts         │
│     ↓                                                       │
│  Apply to each: outputs('List_rows')?['value']            │
│     ├─ Current item: Account 1                            │
│     │   └─ Send email about Account 1                     │
│     │                                                       │
│     ├─ Current item: Account 2                            │
│     │   └─ Send email about Account 2                     │
│     │                                                       │
│     └─ Current item: Account 3                            │
│         └─ Send email about Account 3                     │
│                                                             │
└────────────────────────────────────────────────────────────┘
```

**Common Uses:**
- Process results from "List rows"
- Process files in folder
- Process items from array variable

**Accessing Current Item:**

```
Inside Apply to each:
  items('Apply_to_each')?['name']
  items('Apply_to_each')?['revenue']
  items('Apply_to_each')?['statuscode']
```

**Performance:**
- Default: Processes items sequentially (one by one)
- Can enable **Concurrency Control** to process in parallel
- Max 50 parallel branches

**Concurrency Control:**

```
Settings → Concurrency Control: On
Degree of Parallelism: 10 (process 10 at a time)
```

> **Exam Tip**: Apply to each runs SEQUENTIALLY by default. Enable concurrency for performance. This is a common optimization question.

### Do Until

Repeat until condition is met:

```
┌────────────────────────────────────────────────────────────┐
│                    DO UNTIL PATTERN                         │
├────────────────────────────────────────────────────────────┤
│                                                             │
│  Initialize variable: Counter = 0                          │
│     ↓                                                       │
│  Do until: Counter is equal to 10                          │
│     ├─ Action: Perform operation                           │
│     ├─ Action: Increment Counter                           │
│     └─ Repeat...                                           │
│                                                             │
└────────────────────────────────────────────────────────────┘
```

**Configuration:**

| Setting | Purpose |
|---------|---------|
| **Condition** | When to stop looping |
| **Timeout** | Max duration (default 1 hour) |
| **Count** | Max iterations (default 60) |

**Common Use Cases:**
- Polling API until status changes
- Retry operations
- Wait for external process

**Safety Limits:**
- Always set timeout and max count
- Prevents infinite loops
- Flow will fail if limits exceeded

> **Exam Tip**: Do until requires a condition, timeout, AND count limit. Missing any of these is a common error scenario in exam questions.

---

## Expressions and Functions

### Understanding Expressions

Expressions are **formulas** for data manipulation:

```
Expression syntax: @function(parameters)

Examples:
  @utcNow()                           → Current UTC time
  @addDays(utcNow(), 7)               → 7 days from now
  @concat('Hello ', 'World')          → 'Hello World'
  @length(variables('myArray'))       → Count items
```

### Common Expression Categories

#### String Functions

| Function | Purpose | Example |
|----------|---------|---------|
| `concat()` | Join strings | `@concat('Hello', ' ', 'World')` |
| `substring()` | Extract part | `@substring('Microsoft', 0, 5)` → 'Micro' |
| `replace()` | Replace text | `@replace('Hello', 'H', 'Y')` → 'Yello' |
| `toLower()` | Lowercase | `@toLower('HELLO')` → 'hello' |
| `toUpper()` | Uppercase | `@toUpper('hello')` → 'HELLO' |
| `trim()` | Remove whitespace | `@trim('  hello  ')` → 'hello' |
| `split()` | Split string | `@split('A,B,C', ',')` → ['A','B','C'] |
| `length()` | String length | `@length('Microsoft')` → 9 |
| `startsWith()` | Check start | `@startsWith('Microsoft', 'Micro')` → true |
| `endsWith()` | Check end | `@endsWith('Microsoft', 'soft')` → true |
| `contains()` | Check contains | `@contains('Microsoft', 'cro')` → true |

#### Date Functions

| Function | Purpose | Example |
|----------|---------|---------|
| `utcNow()` | Current UTC time | `@utcNow()` |
| `addDays()` | Add days | `@addDays(utcNow(), 7)` |
| `addHours()` | Add hours | `@addHours(utcNow(), 3)` |
| `addMinutes()` | Add minutes | `@addMinutes(utcNow(), 30)` |
| `addToTime()` | Add interval | `@addToTime(utcNow(), 1, 'Month')` |
| `subtractFromTime()` | Subtract interval | `@subtractFromTime(utcNow(), 1, 'Day')` |
| `formatDateTime()` | Format date | `@formatDateTime(utcNow(), 'yyyy-MM-dd')` |
| `convertTimeZone()` | Convert timezone | `@convertTimeZone(utcNow(), 'UTC', 'Pacific Standard Time')` |
| `dayOfWeek()` | Day number | `@dayOfWeek(utcNow())` → 0-6 |
| `dayOfMonth()` | Day of month | `@dayOfMonth(utcNow())` → 1-31 |

**Date Format Strings:**

```
yyyy-MM-dd              → 2026-01-11
MM/dd/yyyy              → 01/11/2026
dd-MMM-yyyy             → 11-Jan-2026
yyyy-MM-dd HH:mm:ss     → 2026-01-11 14:30:00
```

#### Logical Functions

| Function | Purpose | Example |
|----------|---------|---------|
| `equals()` | Compare equal | `@equals(variables('Status'), 'Active')` |
| `greater()` | Greater than | `@greater(variables('Count'), 100)` |
| `less()` | Less than | `@less(variables('Count'), 10)` |
| `and()` | Logical AND | `@and(equals(A, 1), equals(B, 2))` |
| `or()` | Logical OR | `@or(equals(A, 1), equals(B, 2))` |
| `not()` | Logical NOT | `@not(equals(variables('Status'), 'Active'))` |
| `if()` | Conditional | `@if(equals(A, 1), 'Yes', 'No')` |
| `empty()` | Check if empty | `@empty(variables('myArray'))` → true/false |

#### Collection Functions

| Function | Purpose | Example |
|----------|---------|---------|
| `length()` | Count items | `@length(variables('myArray'))` |
| `first()` | First item | `@first(variables('myArray'))` |
| `last()` | Last item | `@last(variables('myArray'))` |
| `join()` | Array to string | `@join(variables('myArray'), ', ')` |
| `union()` | Combine arrays | `@union(array1, array2)` |
| `intersection()` | Common items | `@intersection(array1, array2)` |

#### Conversion Functions

| Function | Purpose | Example |
|----------|---------|---------|
| `string()` | Convert to string | `@string(123)` → '123' |
| `int()` | Convert to integer | `@int('123')` → 123 |
| `float()` | Convert to decimal | `@float('123.45')` → 123.45 |
| `bool()` | Convert to boolean | `@bool('true')` → true |
| `json()` | Parse JSON | `@json('{"name":"John"}')` |

### Referencing Dynamic Content

**Trigger outputs:**
```
@triggerOutputs()?['body/name']
@triggerOutputs()?['body/statuscode']
```

**Action outputs:**
```
@outputs('Get_account')?['body/name']
@outputs('Get_account')?['body/revenue']
```

**Variables:**
```
@variables('myVariable')
```

**Items in loop:**
```
@items('Apply_to_each')?['name']
```

> **Exam Tip**: Use `?` for safe navigation (null checks). `?['property']` won't error if null. `['property']` will fail on null.

---

## Variables

### Variable Types

Power Automate supports several variable types:

| Type | Use Case | Example |
|------|----------|---------|
| **String** | Text data | "Active", "John Doe" |
| **Integer** | Whole numbers | 10, 100, 1000 |
| **Float** | Decimal numbers | 10.5, 99.99 |
| **Boolean** | True/false | true, false |
| **Array** | List of items | ["A", "B", "C"] |
| **Object** | Complex structure | {"name": "John", "age": 30} |

### Variable Actions

| Action | Purpose | Example |
|--------|---------|---------|
| **Initialize variable** | Create variable | Set Counter to 0 |
| **Set variable** | Change value | Set Counter to 10 |
| **Increment variable** | Add to number | Increment Counter by 1 |
| **Decrement variable** | Subtract from number | Decrement Counter by 1 |
| **Append to string** | Add to string | Append " World" to Message |
| **Append to array** | Add to array | Append item to ItemsList |

### Variable Scope

Variables are **global** within a flow:
- Available in all actions after initialization
- NOT available across flows (use child flows for that)
- Persist throughout flow run

### Common Variable Patterns

**Counter Pattern:**
```
Initialize variable: Counter (Integer) = 0

Do until: Counter equals 10
  ├─ Perform action
  └─ Increment variable: Counter by 1
```

**Accumulator Pattern:**
```
Initialize variable: TotalRevenue (Float) = 0

Apply to each: List of accounts
  └─ Increment variable: TotalRevenue by items('Apply_to_each')?['revenue']
```

**Array Builder Pattern:**
```
Initialize variable: EmailList (Array) = []

Apply to each: List of contacts
  └─ Append to array variable: EmailList
      Value: items('Apply_to_each')?['emailaddress1']
```

**Status Tracking Pattern:**
```
Initialize variable: HasError (Boolean) = false

Condition: If validation fails
  └─ Yes: Set variable: HasError to true

Condition: If HasError equals true
  └─ Yes: Send error notification
```

> **Exam Tip**: Variables must be initialized BEFORE use. Using Set/Increment/Append on uninitialized variable will fail.

---

## Error Handling and Scope

### Understanding Flow Failures

Flows can fail due to:
- Connection issues
- Permission errors
- Null reference errors
- Timeout errors
- API rate limiting
- Invalid data

### Configure Run After

**Every action has "Configure run after"** settings:

```
┌────────────────────────────────────────────────────────────┐
│                  CONFIGURE RUN AFTER                        │
├────────────────────────────────────────────────────────────┤
│                                                             │
│  Run this action after previous action:                    │
│                                                             │
│  ☑ is successful                                           │
│  ☐ has failed                                              │
│  ☐ is skipped                                              │
│  ☐ has timed out                                           │
│                                                             │
└────────────────────────────────────────────────────────────┘
```

| Option | When Action Runs |
|--------|------------------|
| **is successful** | Previous action succeeded (default) |
| **has failed** | Previous action failed |
| **is skipped** | Previous action was skipped |
| **has timed out** | Previous action timed out |

### Try-Catch Pattern

Implement error handling using Scope:

```
┌────────────────────────────────────────────────────────────┐
│                    TRY-CATCH PATTERN                        │
├────────────────────────────────────────────────────────────┤
│                                                             │
│  Scope: Try                                                │
│     ├─ Action 1 (might fail)                               │
│     ├─ Action 2                                            │
│     └─ Action 3                                            │
│                                                             │
│  Scope: Catch                                              │
│     Configure run after: [has failed, has timed out]      │
│     └─ Send error notification                             │
│                                                             │
│  Scope: Finally                                            │
│     Configure run after: [all options checked]            │
│     └─ Clean up / logging                                  │
│                                                             │
└────────────────────────────────────────────────────────────┘
```

**Implementation:**

1. **Try Scope**: Contains actions that might fail
2. **Catch Scope**: Configure run after "has failed" and "has timed out"
   - Handle errors
   - Send notifications
   - Log errors
3. **Finally Scope**: Configure run after ALL options
   - Always runs
   - Clean up
   - Final logging

### Scope Action

Scope is a **container** for actions:

**Benefits:**
- Group related actions
- Error handling
- Parallel branches
- Cleaner organization

**Scope Result:**

```
result('Try_Scope')

Returns:
- Succeeded
- Failed
- Cancelled
- Skipped
```

**Check Scope Status:**

```
Condition: result('Try_Scope') is equal to 'Failed'
  └─ Yes: Handle error
```

### Terminate Action

Explicitly end flow execution:

| Status | Purpose |
|--------|---------|
| **Succeeded** | Mark flow as successful and stop |
| **Failed** | Mark flow as failed and stop |
| **Cancelled** | Mark flow as cancelled and stop |

**Use Cases:**
- Early exit on validation failure
- Stop processing after error
- End flow when condition met

```
Condition: If required field is empty
  └─ Yes: Terminate with status Failed
```

### Retry Policy

Configure automatic retries for actions:

```
Settings → Retry Policy
  ├─ Default
  ├─ None
  ├─ Exponential Interval (4 retries)
  └─ Fixed Interval (4 retries)
```

**Exponential Interval:**
- Retry 1: After 7.5 seconds
- Retry 2: After 15 seconds
- Retry 3: After 30 seconds
- Retry 4: After 60 seconds

**Fixed Interval:**
- All retries: Same interval

> **Exam Tip**: Retries are automatic for transient errors. Use Scope + Configure Run After for permanent errors that need custom handling.

### Advanced Error Handling Patterns

#### Nested Scopes for Complex Error Management

You can nest scopes within scopes for hierarchical error handling, but there are limitations:

**Maximum Nesting Depth:**
- Maximum 8 levels of nesting total (including conditions, loops, and scopes combined)
- Each scope counts as one level

```
┌────────────────────────────────────────────────────────┐
│           NESTED SCOPE ARCHITECTURE                     │
├────────────────────────────────────────────────────────┤
│  Level 1: Try Scope (Main operation)                   │
│  ├─ Level 2: Inner Scope 1 (Optional task)             │
│  │  └─ Level 3: Inner Scope 2 (Specific error catch)   │
│  │                                                      │
│  ├─ Error handling for Level 2                         │
│  │  └─ Log error                                        │
│  │                                                      │
│  └─ Catch Scope (Catch-all)                            │
│     └─ Handle any uncaught errors                      │
└────────────────────────────────────────────────────────┘
```

**Example Use Case:**
```
Scope: ProcessOrder (Try)
  ├─ Scope: ValidateInventory
  │   ├─ Check stock
  │   ├─ Catch: If fails, set flag
  │
  ├─ Scope: ChargePayment
  │   ├─ Process payment
  │   ├─ Catch: If fails, refund attempt
  │
  └─ Scope: SendConfirmation
      ├─ Send order confirmation
      └─ (If fails, it's non-critical)

Scope: CatchErrors (has failed/timed out)
  └─ Send admin alert, log to database
```

**Critical Limitations:**
- Exceeding 8 levels causes the flow to fail to save
- Conditions, loops, and parallel branches also count toward the limit
- Plan your nesting strategy carefully

#### Using Scope Result() for Dynamic Error Handling

The `result()` function returns the status of a scope and enables conditional error handling:

**Possible Results:**
- `Succeeded` - All actions in scope completed successfully
- `Failed` - One or more actions failed
- `Skipped` - Scope was skipped (conditional block evaluated to false)
- `TimedOut` - Scope execution exceeded timeout

**Advanced Pattern - Selective Error Recovery:**

```
Scope: AttemptDataUpdate
  ├─ Action 1: Query database
  ├─ Action 2: Update record
  └─ Action 3: Log update

Condition: result('AttemptDataUpdate') == 'Failed'
  ├─ Yes Branch:
  │   └─ Condition: result('Action2') == 'Failed'
  │       ├─ Payment failed: Retry payment
  │       ├─ Data update failed: Notify admin
  │       └─ Log failed: Continue (non-critical)
  │
  └─ No Branch:
      └─ Success notification
```

#### Multiple Catch Scopes for Different Error Types

Handle different types of errors with separate catch scopes:

```
Scope: MainProcess
  ├─ Action: Query Dataverse (might timeout)
  ├─ Action: Send Email (might fail - permission)
  └─ Action: Update database (might fail - data conflict)

Scope: CatchTimeoutErrors
  Configure run after: [has timed out]
  └─ Retry with longer timeout
     └─ Use Terminate with "Failed" if timeout again

Scope: CatchPermissionErrors
  Configure run after: [has failed]
  └─ Check error message contains "permission"
     └─ If yes: Send alert to admin
     └─ If no: Try alternate action

Scope: CatchDataErrors
  Configure run after: [has failed]
  └─ Log detailed error with context
     └─ Attempt rollback if applicable
```

#### Error Notification and Logging Pattern

Implement comprehensive error tracking:

```
Scope: CatchAllErrors
  Configure run after: [has failed, has timed out]
  ├─ Set variable: errorMessage = body('Failed_action')
  ├─ Set variable: errorTime = utcNow()
  ├─ Compose error details:
  │   {
  │     "Flow": workflow().name,
  │     "Trigger": triggerBody()['ID'],
  │     "Error": @{body('Failed_action')?['error']},
  │     "Timestamp": @{utcNow()},
  │     "User": @{user()}
  │   }
  ├─ Send email to admin
  ├─ Create error log record in Dataverse
  └─ Terminate with "Failed"
```

#### Scope Execution and Performance Implications

**Important Notes:**
- Scopes execute sequentially by default (one after another)
- To run scopes in parallel, use "Parallel branch" action
- Parallel scopes within a Try block share the same error handling
- Each action in a scope can have different "Configure run after" settings

```
Parallel Execution Pattern:
┌──────────────────────────────────────────┐
│  Scope: Parallel Branch 1 (Query data)   │ Runs
├──────────────────────────────────────────┤ at
│  Scope: Parallel Branch 2 (Send email)   │ same
├──────────────────────────────────────────┤ time
│  Scope: Parallel Branch 3 (Update DB)    │
└──────────────────────────────────────────┘
         ↓
    All branches complete
         ↓
  Continue with next action
```

> **Exam Scenario**: "A flow must retry a failed payment, then send a confirmation email regardless of payment success, and log all activities."
→ **Answer**: Use Try Scope with payment logic, Catch Scope for retry, Then use Scope after to send confirmation with Configure run after "all options", and finally log with another Scope.

---

## Child Flows and Flow Management

### Child Flows

Child flows allow **one flow to call another**:

```
┌────────────────────────────────────────────────────────────┐
│                   CHILD FLOW PATTERN                        │
├────────────────────────────────────────────────────────────┤
│                                                             │
│  Parent Flow                                               │
│  ┌─────────────────────────────────────────────────────┐  │
│  │  Trigger                                            │  │
│  │     ↓                                                │  │
│  │  Some actions                                       │  │
│  │     ↓                                                │  │
│  │  Run a Child Flow ──────┐                          │  │
│  │     ↓                    │                          │  │
│  │  Use child flow outputs  │                          │  │
│  └─────────────────────────┼─────────────────────────┘  │
│                             │                             │
│  Child Flow                 │                             │
│  ┌──────────────────────────┼────────────────────────┐  │
│  │  Trigger: When invoked ←─┘                        │  │
│  │     ↓                                               │  │
│  │  Receive inputs                                    │  │
│  │     ↓                                               │  │
│  │  Perform operations                                │  │
│  │     ↓                                               │  │
│  │  Respond to flow                                   │  │
│  │    (Return outputs)                                │  │
│  └────────────────────────────────────────────────────┘  │
│                                                             │
└────────────────────────────────────────────────────────────┘
```

### Creating Child Flows

**Child Flow Requirements:**
1. Trigger: "When a flow is run from another flow" (manual trigger type)
2. Can have input parameters
3. Must use "Respond to a PowerApp or flow" to return data

**Child Flow Structure:**

```
Trigger: When a flow is run from another flow
  Inputs:
    - AccountID (text)
    - Priority (text)
    ↓
Actions: Perform logic
    ↓
Respond to a PowerApp or flow
  Outputs:
    - Result (text)
    - RecordID (text)
```

**Calling from Parent Flow:**

```
Action: Run a child flow
  └─ Select child flow
  └─ Provide inputs
  └─ Use outputs
```

### Benefits of Child Flows

| Benefit | Description |
|---------|-------------|
| **Reusability** | Call same logic from multiple flows |
| **Organization** | Break complex flows into manageable pieces |
| **Maintenance** | Update once, affects all parent flows |
| **Separation of Concerns** | Each flow has single responsibility |
| **Error Isolation** | Child flow error doesn't fail parent |

### Child Flow Limits

- Can call up to **5 levels deep**
- Recommended: Keep to 2-3 levels maximum
- Each call counts toward flow run limits

> **Exam Tip**: Child flows enable reusability. Look for scenarios about "same logic in multiple flows" or "reusable components".

---

## Approvals

### Built-In Approval Actions

Power Automate includes approval workflow actions:

```
┌────────────────────────────────────────────────────────────┐
│                   APPROVAL FLOW PATTERN                     │
├────────────────────────────────────────────────────────────┤
│                                                             │
│  Trigger: When a record is created                         │
│     ↓                                                       │
│  Start and wait for an approval                            │
│     ├─ Approval type: Approve/Reject - First to respond   │
│     ├─ Title: "Approve new account"                       │
│     ├─ Assigned to: manager@contoso.com                   │
│     ├─ Details: Account details                           │
│     └─ Item link: Link to record                          │
│     ↓                                                       │
│  ⏳ WAIT for response                                      │
│     ↓                                                       │
│  Condition: Outcome is Approve                             │
│     ├─ Yes: Activate account                               │
│     └─ No: Set status to rejected                          │
│                                                             │
└────────────────────────────────────────────────────────────┘
```

### Approval Types

| Type | Behavior | Use Case |
|------|----------|----------|
| **Approve/Reject - First to respond** | First person's decision applies | Quick approvals |
| **Approve/Reject - Everyone must approve** | All must approve | Committee decisions |
| **Custom Responses - Wait for all responses** | Custom options, all respond | Survey/voting |
| **Custom Responses - Wait for one response** | Custom options, first responds | Multiple choice |

### Approval Action Configuration

**Start and wait for an approval:**

| Parameter | Purpose |
|-----------|---------|
| **Approval type** | Which approval type |
| **Title** | Subject line in email/app |
| **Assigned to** | Email addresses (semicolon separated) |
| **Details** | Full description/context |
| **Item link** | URL to record |
| **Item link description** | Link text |

**Outputs:**

| Output | Description |
|--------|-------------|
| **Outcome** | "Approve" or "Reject" |
| **Responses** | All responses (array) |
| **Responder** | Email of person who responded |
| **Response comments** | Comments from approver |
| **Response time** | When responded |

### Approval Experience

**Approvers can respond via:**
- Email (Approve/Reject buttons)
- Power Automate mobile app
- Power Automate web portal (Approvals center)
- Teams (Approvals app)

**Approval Email:**
```
Subject: Approval Request - [Title]

[Details]

[Approve Button] [Reject Button]

View: [Item Link]
```

### Common Approval Patterns

**Pattern 1: Manager Approval**

```
Get user's manager:
  Office 365 Users → Get manager (V2)
  User (UPN): triggerOutputs()?['body/_ownerid_value@OData.Community.Display.V1.FormattedValue']

Start and wait for an approval:
  Assigned to: outputs('Get_manager')?['body/mail']
```

**Pattern 2: Sequential Approvals**

```
First approval: Line Manager
  ↓
Condition: If approved
  ↓
Second approval: Department Head
  ↓
Condition: If approved
  ↓
Activate record
```

**Pattern 3: Parallel Approvals**

```
Approval type: Approve/Reject - Everyone must approve

Assigned to: manager1@contoso.com; manager2@contoso.com; manager3@contoso.com

All must approve for "Approve" outcome
```

**Pattern 4: Conditional Approval**

```
Condition: If amount > 10000
  ├─ Yes: Require VP approval
  └─ No: Require Manager approval
```

> **Exam Tip**: "Start and wait" blocks flow execution until response. Flow continues after approval/rejection. This is synchronous, not asynchronous.

---

## Integration with Dataverse

### Dataverse Actions Deep Dive

#### Add Row (Create Record)

```
Action: Add a new row
  Table name: Accounts
  Account Name: "Contoso"
  City: "Seattle"
  Status: Active (from choice field)
```

**Choice Fields:**
- Select from dropdown
- Or use expression: `916230000` (internal value)

**Lookup Fields:**
- Must provide GUID
- Format: `accounts(guid)` or just `guid`

#### Update Row

```
Action: Update a row
  Table name: Accounts
  Row ID: <GUID from trigger/previous action>
  Status: Inactive
  Modified Reason: "Customer request"
```

> **Exam Tip**: Update row REQUIRES the Row ID (GUID). If you don't have it, use "List rows" to find it first.

#### List Rows (Query Records)

```
Action: List rows
  Table name: Accounts
  Filter rows: statuscode eq 1 and revenue gt 100000
  Select columns: accountid,name,revenue
  Sort by: createdon desc
  Row count: 100
```

**Outputs:**
- Returns array: `outputs('List_rows')?['value']`
- Must use "Apply to each" to process results

**Pagination:**
- Default: First 5000 records
- Can increase row count
- Use "Fetch Xml Query" for advanced queries

#### Get Row by ID

```
Action: Get a row by ID
  Table name: Accounts
  Row ID: <GUID>
```

**Outputs:**
- Direct access to fields
- No loop needed
- Faster than List rows

#### Relate Rows (N:N Relationships)

```
Action: Relate rows
  Table name: Contacts (main table)
  Row ID: <Contact GUID>
  Relationship: account_primary_contact (relationship name)
  Related table: Accounts
  Related row ID: <Account GUID>
```

**Use Case:**
- Associate contact with account (N:N)
- Add user to team
- Link records in many-to-many

#### Delete Row

```
Action: Delete a row
  Table name: Accounts
  Row ID: <GUID>
```

**Important:**
- Permanent deletion (can be recovered from Recycle Bin for 30 days)
- Cascade delete rules apply
- Check dependencies first

### Working with Choice Fields

Choice fields (formerly Option Sets) require special handling:

**Reading Choice Value:**

```
// Display label (localized)
triggerOutputs()?['body/statuscode@OData.Community.Display.V1.FormattedValue']

// Internal value (number)
triggerOutputs()?['body/statuscode']
```

**Setting Choice Value:**

In action, select from dropdown OR use expression with internal value:

```
916230000  // For "Active" status (example)
```

### Working with Lookup Fields

Lookup fields reference related records:

**Reading Lookup:**

```
// Related record ID (GUID)
triggerOutputs()?['body/_primarycontactid_value']

// Related record name (formatted)
triggerOutputs()?['body/_primarycontactid_value@OData.Community.Display.V1.FormattedValue']
```

**Setting Lookup:**

Must provide GUID:

```
// In Update row action
Primary Contact: <Contact GUID>

// Or use output from another action
Primary Contact: outputs('Get_contact')?['body/contactid']
```

### OData Query Advanced

**Expand (Get Related Data):**

```
Expand Query: primarycontactid($select=fullname,emailaddress1)

Access:
  outputs('Get_account')?['body/primarycontactid/fullname']
```

**Filter with Related Data:**

```
Filter rows: _primarycontactid_value eq <GUID>
```

> **Exam Tip**: OData filters use internal column names (with underscores). Display names don't work in filters.

---

## Desktop Flows (RPA)

### What are Desktop Flows?

Desktop flows automate **Windows desktop applications** using:
- UI automation (clicking, typing)
- Screen scraping (reading values)
- Keyboard/mouse simulation

```
┌────────────────────────────────────────────────────────────┐
│                  DESKTOP FLOW ARCHITECTURE                  │
├────────────────────────────────────────────────────────────┤
│                                                             │
│  Cloud Flow                                                │
│  ┌─────────────────────────────────────────────────────┐  │
│  │  Trigger                                            │  │
│  │     ↓                                                │  │
│  │  Get data from Dataverse                           │  │
│  │     ↓                                                │  │
│  │  Run desktop flow ──────┐                          │  │
│  │     ↓                    │                          │  │
│  │  Use desktop flow output │                          │  │
│  └─────────────────────────┼─────────────────────────┘  │
│                             │                             │
│  Desktop Flow (on Windows)  │                             │
│  ┌──────────────────────────┼────────────────────────┐  │
│  │  ←──────────────────────┘                         │  │
│  │  Open application                                  │  │
│  │     ↓                                               │  │
│  │  Click buttons, enter text                         │  │
│  │     ↓                                               │  │
│  │  Read values from screen                           │  │
│  │     ↓                                               │  │
│  │  Return outputs                                    │  │
│  └────────────────────────────────────────────────────┘  │
│                                                             │
└────────────────────────────────────────────────────────────┘
```

### When to Use Desktop Flows

**Use desktop flows when:**
- Application has NO API
- Legacy applications
- Desktop-only software
- Citrix/Remote Desktop scenarios
- Local file operations

**Example Use Cases:**
- Automate data entry in legacy ERP
- Extract data from desktop application
- Automate Excel desktop app
- Automate SAP GUI

### Recording Desktop Flows

**Power Automate Desktop (PAD):**
- Free Windows application
- Visual designer for desktop flows
- Recorder for capturing actions

**Recording Process:**
1. Open Power Automate Desktop
2. Click "Recorder"
3. Perform actions in application
4. Recorder captures clicks, keystrokes
5. Stop recording
6. Edit recorded actions

### Attended vs Unattended

| Type | Description | Use Case |
|------|-------------|----------|
| **Attended** | User must be present | User initiates, watches execution |
| **Unattended** | Runs without user | Scheduled automation, background |

**Requirements for Unattended:**
- Unattended RPA add-on license
- Machine registered as unattended
- Dedicated desktop session

### Running Desktop Flows from Cloud Flows

**Action: Run a flow built with Power Automate Desktop**

```
Action: Run a flow built with Power Automate Desktop
  Desktop flow: <Select your desktop flow>
  Run mode: Attended / Unattended
  Connect: <Select machine/gateway>
  Input variables: <Pass values to desktop flow>
```

**Outputs:**
- Desktop flow can return variables
- Use in subsequent cloud flow actions

> **Exam Tip**: Desktop flows require a machine (on-premises data gateway connection). Cloud flows run in cloud. This distinction is commonly tested.

---

## Solutions and ALM

### Flows in Solutions

Flows can be added to **Solutions** for Application Lifecycle Management:

```
┌────────────────────────────────────────────────────────────┐
│                  SOLUTION-BASED FLOWS                       │
├────────────────────────────────────────────────────────────┤
│                                                             │
│  Development Environment                                   │
│  ┌─────────────────────────────────────────────────────┐  │
│  │  Solution: Project ABC (Unmanaged)                  │  │
│  │    ├─ Flow 1: Create account notification           │  │
│  │    ├─ Flow 2: Opportunity approval                  │  │
│  │    ├─ Connection Reference: Dataverse              │  │
│  │    └─ Connection Reference: Office 365              │  │
│  └─────────────────────────────────────────────────────┘  │
│                         ↓ Export as Managed                │
│  Test Environment                                          │
│  ┌─────────────────────────────────────────────────────┐  │
│  │  Solution: Project ABC (Managed)                    │  │
│  │    ├─ Flow 1 (Read-only)                            │  │
│  │    ├─ Flow 2 (Read-only)                            │  │
│  │    ├─ Connection Reference → Reconnect              │  │
│  │    └─ Connection Reference → Reconnect              │  │
│  └─────────────────────────────────────────────────────┘  │
│                         ↓ Export as Managed                │
│  Production Environment                                    │
│  ┌─────────────────────────────────────────────────────┐  │
│  │  Solution: Project ABC (Managed)                    │  │
│  │    └─ All components (Read-only)                    │  │
│  └─────────────────────────────────────────────────────┘  │
│                                                             │
└────────────────────────────────────────────────────────────┘
```

### Connection References

**Problem without Connection References:**
- Flows hard-code connections
- Must edit flow when moving environments
- Breaks during import

**Solution: Connection References**
- Abstract connection from flow
- Reconfigure connection during import
- No flow modifications needed

**Creating Connection References:**
1. Add flow to solution
2. System creates connection references automatically
3. Export solution
4. Import to target environment
5. Map connection references to target connections

### Environment Variables

Store configuration values:

```
Environment Variable: AdminEmail
  - Dev: admin-dev@contoso.com
  - Test: admin-test@contoso.com
  - Prod: admin-prod@contoso.com

Flow uses: environmentVariables('AdminEmail')
```

### Solution Layers

Solutions can layer on top of each other:

```
Base Layer: Microsoft Base Solution
     ↓
Layer 2: Your Managed Solution
     ↓
Layer 3: Customizations (Unmanaged)
```

**Best Practices:**
- Dev: Unmanaged solution
- Test/Prod: Managed solution
- Never mix managed and unmanaged

> **Exam Tip**: Use solutions for flows that need ALM. Connection references are automatic in solutions. This enables environment portability.

---

## Performance and Best Practices

### Performance Optimization

#### 1. Reduce API Calls

**Bad:**
```
Apply to each: Accounts (100 records)
  └─ Get row by ID: Get contact details
      (100 separate API calls)
```

**Good:**
```
List rows: Contacts
  Filter rows: contactid in (list of IDs)
  (1 API call returns all)
```

#### 2. Use Select Columns

**Bad:**
```
List rows: Accounts
  (Returns all columns - slower)
```

**Good:**
```
List rows: Accounts
  Select columns: accountid,name,revenue
  (Returns only needed columns - faster)
```

#### 3. Filter Rows

**Bad:**
```
List rows: Accounts (get all)
Apply to each: Accounts
  Condition: If Status = Active
```

**Good:**
```
List rows: Accounts
  Filter rows: statuscode eq 1
  (Filtered server-side)
```

#### 4. Use Concurrency

**Bad:**
```
Apply to each: 100 accounts
  Sequential processing (one at a time)
  Total time: 100 x 2 seconds = 200 seconds
```

**Good:**
```
Apply to each: 100 accounts
  Concurrency: 10 parallel
  Total time: 10 x 2 seconds = 20 seconds
```

#### 5. Child Flows for Reuse

**Bad:**
```
Flow 1: Contains logic A + logic B
Flow 2: Contains logic A + logic B
Flow 3: Contains logic A + logic B
(Duplicated logic)
```

**Good:**
```
Child Flow: Logic A
Child Flow: Logic B

Flow 1: Calls A + B
Flow 2: Calls A + B
Flow 3: Calls A + B
```

### Flow Limits

Be aware of limits:

| Limit | Value |
|-------|-------|
| **Actions per flow** | 500 |
| **Actions per day** | 100,000 (varies by license) |
| **Concurrent runs** | 50 per flow |
| **Apply to each iterations** | 100,000 |
| **Flow run duration** | 30 days |
| **HTTP request timeout** | 120 seconds |

> **Exam Tip**: If scenario mentions "flow times out" or "flow fails on large data", think about limits and pagination. Use "Row count" in List rows.

### Best Practices Summary

1. **Use solutions** for ALM and portability
2. **Connection references** for environment flexibility
3. **Filter and Select** to reduce data transfer
4. **Concurrency** for performance
5. **Child flows** for reusability
6. **Error handling** with Scope + Configure Run After
7. **Descriptive names** for actions and flows
8. **Comments** for complex logic
9. **Disable unused flows** to reduce clutter
10. **Test in Dev/Test** before Production

---

## Monitoring and Troubleshooting

### Flow Run History

Every flow run is logged:

```
Flow → Run history
  ├─ All runs (last 28 days)
  ├─ Status: Succeeded, Failed, Cancelled, Running
  ├─ Start time and Duration
  └─ Click run to see details
```

### Run Details

Detailed view shows:
- **Inputs** for each action
- **Outputs** for each action
- **Duration** per action
- **Status** per action

```
Trigger: When a row is added
  Status: Succeeded
  Duration: 0.5 seconds
  Outputs: { accountid: "...", name: "Contoso", ... }
     ↓
Get row by ID
  Status: Succeeded
  Duration: 1.2 seconds
  Outputs: { contactid: "...", fullname: "John Doe", ... }
     ↓
Send an email
  Status: Failed
  Error: "Invalid email address"
```

### Common Errors

#### 1. Null Reference Error

**Error**: "InvalidTemplate. Unable to process template language expressions..."

**Cause**: Trying to access property that doesn't exist

**Solution**: Use safe navigation `?` or check for null first

```
// Bad
@outputs('Get_account')['body/revenue']

// Good
@outputs('Get_account')?['body/revenue']

// Or use condition first
Condition: outputs('Get_account')?['body/revenue'] is not empty
```

#### 2. Item Not Found

**Error**: "Resource not found for the segment..."

**Cause**: Record doesn't exist or ID is wrong

**Solution**: Check GUID is correct, use Get row with error handling

#### 3. Insufficient Permissions

**Error**: "Request failed with status code 403"

**Cause**: Connection doesn't have permission

**Solution**: Check security roles, re-authenticate connection

#### 4. Timeout

**Error**: "Action timed out after 120 seconds"

**Cause**: Action took too long

**Solution**:
- Optimize query (filter, select columns)
- Increase timeout in action settings (if available)
- Break into smaller chunks

#### 5. Rate Limiting

**Error**: "Rate limit exceeded"

**Cause**: Too many API calls in short time

**Solution**:
- Add delay between calls
- Use concurrency control
- Batch operations

### Flow Checker

In flow designer:

```
Flow checker (top right)
  ├─ Errors (must fix)
  ├─ Warnings (should review)
  └─ Information
```

**Common warnings:**
- Unused variables
- Missing error handling
- Performance issues

### Analytics

View flow analytics:

```
Analytics tab
  ├─ Run count over time
  ├─ Success rate
  ├─ Average duration
  ├─ Failed runs
  └─ Most common errors
```

> **Exam Tip**: Use run history for troubleshooting. Click on failed run to see exactly which action failed and why. This is the first step in debugging.

---

## Common Exam Scenarios

### Scenario 1: Flow Doesn't Trigger

**Question**: "Dataverse trigger flow doesn't run when record is updated."

**Possible Causes:**
1. **Select columns** configured - flow only triggers when those columns change
2. **Filter rows** doesn't match - record doesn't meet filter criteria
3. **Scope** set to User - but record owned by different user
4. Flow is **turned off**
5. Connection is **broken**

**Solution**: Review trigger configuration, check scope and filters.

### Scenario 2: Can't Access Related Record Data

**Question**: "Need to get Primary Contact name from Account, but not available in trigger."

**Solution**: Use **Get row by ID** or **List rows with Expand**

```
Get row by ID
  Table: Accounts
  Row ID: <trigger accountid>
  Expand Query: primarycontactid($select=fullname)

Access:
  outputs('Get_account')?['body/primarycontactid/fullname']
```

### Scenario 3: Loop Processing Is Slow

**Question**: "Flow processes 500 records, takes 30 minutes."

**Solution**: Enable **Concurrency Control**

```
Apply to each
  Settings → Concurrency Control: On
  Degree of Parallelism: 10-50

Processes multiple items simultaneously
```

### Scenario 4: Flow Fails Sometimes, Works Other Times

**Question**: "Flow intermittently fails with null reference error."

**Solution**: Add null checks or use safe navigation

```
// Check if field exists
Condition: outputs('Get_account')?['body/revenue'] is not empty
  ├─ Yes: Use revenue value
  └─ No: Use default value
```

### Scenario 5: Need to Reuse Logic Across Flows

**Question**: "Same approval logic needed in 5 different flows."

**Solution**: Create **Child Flow**

```
Create child flow: "Standard Approval Process"
  ├─ Input: ApproverEmail, Subject, Details
  ├─ Logic: Approval + notifications
  └─ Output: Outcome

Call from parent flows:
  Run a child flow → Standard Approval Process
```

### Scenario 6: Connections Break After Import

**Question**: "Imported solution, flows don't work."

**Solution**: Use **Connection References**

```
Add flows to solution
  ↓
Export solution
  ↓
Import to target environment
  ↓
Map connection references to target connections
```

### Scenario 7: Choice Field Value Not Setting

**Question**: "Update row action doesn't set status choice field."

**Solution**: Use correct syntax or select from dropdown

```
// Select from dropdown in action
Status: Active (select from dropdown)

// Or use internal value
Status: 1
```

### Scenario 8: Flow Times Out on Large Dataset

**Question**: "List rows times out when retrieving 50,000 records."

**Solution**: Use pagination with **Row count** and multiple calls

```
List rows
  Filter rows: <criteria>
  Row count: 5000

Loop through results:
  Apply to each: outputs('List_rows')?['value']
```

Or use **scheduled flow** to process in batches.

### Scenario 9: Need Approval from Manager

**Question**: "When opportunity created, send to record owner's manager for approval."

**Solution**: Use Office 365 Users connector

```
Get manager (V2)
  User (UPN): outputs('Get_owner')?['body/internalemailaddress']

Start and wait for an approval
  Assigned to: outputs('Get_manager')?['body/mail']
```

### Scenario 10: Desktop Flow vs Cloud Flow

**Question**: "Need to automate legacy application with no API."

**Answer**: Use **Desktop Flow**
- Legacy apps without APIs require UI automation
- Cloud flows cannot automate desktop applications

---

## Key Takeaways for the Exam

1. **Three flow types**: Automated (event), Instant (manual), Scheduled (recurrence)
2. **Dataverse trigger scope**: Organization (all), Business Unit, User (owner)
3. **Select columns optimization**: Only triggers when specified columns change
4. **Get row vs List rows**: Get row for ID, List rows for query (returns array)
5. **OData filters**: Server-side filtering, use internal names with underscores
6. **Apply to each is sequential**: Enable concurrency for parallel processing
7. **Child flows enable reuse**: One flow can call another
8. **Connection references in solutions**: Enable environment portability
9. **Configure run after for error handling**: Create try-catch patterns with Scope
10. **Approvals block execution**: "Start and wait" is synchronous
11. **Choice fields need special syntax**: Use dropdown or internal value
12. **Safe navigation with ?**: Prevents null reference errors
13. **Desktop flows for UI automation**: Legacy apps without APIs
14. **Concurrency improves performance**: Process items in parallel (max 50)
15. **Filter and Select optimize queries**: Reduce data transfer
16. **Variables must be initialized**: Before Set/Increment/Append
17. **Do until needs limits**: Timeout AND count to prevent infinite loops
18. **Expressions start with @**: `@utcNow()`, `@concat()`, etc.
19. **Run history shows 28 days**: Click failed run to see error details
20. **Solution flows use Connection References**: Automatically created in solutions

---

**Note**: Microsoft Copilot Studio (formerly Power Virtual Agents) was removed from the PL-200 exam in January 2024. For integration scenarios, see [Integration Scenarios Deep Dive](./Integration-DeepDive.md).
