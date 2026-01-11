# Canvas Apps - Complete Deep Dive
## PL-200 Exam Preparation

---

## Table of Contents
1. [What are Canvas Apps?](#what-are-canvas-apps)
2. [Canvas vs Model-Driven Apps](#canvas-vs-model-driven-apps)
3. [Canvas App Types and Form Factors](#canvas-app-types-and-form-factors)
4. [Data Sources and Connectors](#data-sources-and-connectors)
5. [Delegation - Critical Exam Topic](#delegation---critical-exam-topic)
6. [Formulas and Functions](#formulas-and-functions)
7. [Variables and State Management](#variables-and-state-management)
8. [Controls Deep Dive](#controls-deep-dive)
9. [Navigation and Context](#navigation-and-context)
10. [Forms - Edit, Display, and New](#forms---edit-display-and-new)
11. [Gallery Controls](#gallery-controls)
12. [Components and Reusability](#components-and-reusability)
13. [Integration with Dataverse](#integration-with-dataverse)
14. [Performance and Optimization](#performance-and-optimization)
15. [Publishing, Sharing, and Versioning](#publishing-sharing-and-versioning)
16. [Common Exam Scenarios](#common-exam-scenarios)

---

## What are Canvas Apps?

### The Power of Pixel-Perfect Design

Canvas Apps provide a **blank canvas** where you have complete control over the user interface. Unlike model-driven apps which generate UI from metadata, Canvas Apps let you design every pixel.

```
┌─────────────────────────────────────────────────────────────────┐
│                      CANVAS APP ARCHITECTURE                     │
├─────────────────────────────────────────────────────────────────┤
│  ┌─────────────────────────────────────────────────────────┐   │
│  │                    UI Layer (You Design)                 │   │
│  │  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐   │   │
│  │  │ Button  │  │ Gallery │  │  Form   │  │  Label  │   │   │
│  │  └─────────┘  └─────────┘  └─────────┘  └─────────┘   │   │
│  └─────────────────────────────────────────────────────────┘   │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │               Formula Layer (Power Fx)                   │   │
│  │              OnSelect, OnChange, OnVisible              │   │
│  └─────────────────────────────────────────────────────────┘   │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │                    Data Layer                            │   │
│  │  Dataverse | SharePoint | SQL | Excel | API            │   │
│  └─────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
```

### Key Characteristics

| Aspect | Description |
|--------|-------------|
| **Design Approach** | Start with a blank canvas, drag-and-drop controls |
| **Target Users** | Internal or external, broad audiences |
| **Formula Language** | Power Fx (similar to Excel formulas) |
| **Responsiveness** | Can be responsive or fixed layout |
| **Data Sources** | Connect to 1000+ connectors |
| **Use Cases** | Custom UIs, mobile apps, tablet apps, kiosks |

---

## Canvas vs Model-Driven Apps

### The Critical Comparison for the Exam

This comparison appears frequently in exam questions:

| Feature | Canvas Apps | Model-Driven Apps |
|---------|-------------|-------------------|
| **Starting Point** | Blank canvas | Data model (tables) |
| **Designer Control** | Complete pixel control | Limited customization |
| **Design Time** | Longer (custom design) | Faster (auto-generated) |
| **Data Source** | 1000+ connectors | Primarily Dataverse |
| **Best For** | Task-focused apps | Data-driven processes |
| **Mobile** | Optimized for mobile | Responsive but desktop-first |
| **Offline** | Limited offline | Better offline with syncing |
| **Security** | App-level sharing | Record-level security |
| **Business Logic** | Formulas in controls | Business rules, workflows |
| **Forms** | Custom designed | Auto-generated from metadata |
| **Licensing** | Premium connector = Premium | Dynamics 365/standalone license |

### When to Use Canvas Apps

**Choose Canvas Apps when you need:**
- Custom user interface design
- Support for multiple data sources
- Mobile-first experience
- Task or process-specific app
- External user access (with sharing)
- Integration with non-Dataverse data

**Example Scenarios:**
- Expense approval app (read Excel, photos)
- Field inspection app (offline mode, camera)
- Onboarding checklist app (simple workflow)
- Dashboard pulling from multiple sources

> **Exam Tip**: If the question mentions "multiple data sources" or "custom design", think Canvas Apps. If it mentions "complex security" or "Dynamics 365 integration", think Model-Driven.

---

## Canvas App Types and Form Factors

### Three Primary Form Factors

#### 1. Tablet Apps
- **Resolution**: 1366 x 768 (16:9 landscape)
- **Best For**: Desktop browsers, large tablets
- **Common Uses**: Dashboard apps, data entry apps

#### 2. Phone Apps
- **Resolution**: 640 x 1136 (portrait)
- **Best For**: Mobile devices
- **Common Uses**: Field apps, inspection apps, quick capture

#### 3. Responsive Apps (Modern)
- **Resolution**: Adapts to screen size
- **Best For**: All devices
- **Common Uses**: Universal apps that work everywhere

```
┌────────────────────────────────────────────────────────────┐
│                    FORM FACTOR SELECTION                    │
├────────────────────────────────────────────────────────────┤
│                                                             │
│  Tablet (1366x768)        Phone (640x1136)    Responsive   │
│  ┌──────────────┐         ┌────────┐         ┌─────────┐  │
│  │              │         │        │         │ Flexible │  │
│  │   Fixed      │         │ Fixed  │         │  Width   │  │
│  │   Width      │         │ Height │         │  Height  │  │
│  │              │         │        │         │ Adapts   │  │
│  └──────────────┘         └────────┘         └─────────┘  │
│                                                             │
│  Desktop-first           Mobile-only        Universal      │
└────────────────────────────────────────────────────────────┘
```

> **Exam Tip**: Responsive apps are the modern standard. Tablet/Phone apps use fixed layouts. You CANNOT change form factor after creation (must recreate app).

### Creating from Templates vs Blank

**From Blank:**
- Complete control
- Start with empty screen
- More time to build

**From Template:**
- Pre-built screens and logic
- Faster starting point
- Can customize after creation

**From Data:**
- Auto-generates three screens (Browse, Detail, Edit)
- Connected to your data source
- Good for CRUD operations

---

## Data Sources and Connectors

### Understanding Data Connections

Canvas Apps can connect to over 1000 data sources through **connectors**:

```
┌────────────────────────────────────────────────────────────┐
│                   CANVAS APP DATA SOURCES                   │
├────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐    │
│  │  Microsoft   │  │    Cloud     │  │    Custom    │    │
│  │              │  │              │  │              │    │
│  │ • Dataverse  │  │ • Salesforce │  │ • HTTP/API   │    │
│  │ • SharePoint │  │ • Google     │  │ • SQL Server │    │
│  │ • OneDrive   │  │ • Dropbox    │  │ • On-Prem    │    │
│  │ • Excel      │  │ • Twitter    │  │ • Gateway    │    │
│  │ • Teams      │  │ • Dynamics   │  │              │    │
│  └──────────────┘  └──────────────┘  └──────────────┘    │
│                                                             │
│      Standard           Premium          Premium/Custom    │
└────────────────────────────────────────────────────────────┘
```

### Connector Types

| Type | Examples | Licensing |
|------|----------|-----------|
| **Standard** | SharePoint, Office 365, OneDrive | Included in base license |
| **Premium** | Dataverse, SQL Server, HTTP | Requires premium license |
| **Custom** | Your own APIs via Custom Connector | Premium license required |

### Adding Data Sources

1. **Data** pane > **Add data**
2. Search for connector
3. Create connection (credentials)
4. Select items (tables, lists, etc.)

### Multiple Data Sources in One App

Canvas Apps can have **multiple data sources**:

```powerapps
// Using Dataverse
Filter(Accounts, 'Account Name' = "Contoso")

// Using SharePoint
Filter('Project List', Status = "Active")

// Using SQL Server
Filter('[dbo].[Customers]', City = "Seattle")
```

> **Exam Tip**: Model-driven apps primarily use ONE Dataverse database. Canvas apps can use MANY sources. This is a key differentiator.

### Connection References (Solutions)

When you add a Canvas App to a solution, connectors become **Connection References**:
- Allows connections to be reconfigured during deployment
- No need to edit app when moving between environments
- Best practice for ALM

---

## Delegation - Critical Exam Topic

### What is Delegation?

**Delegation** = Processing data on the **server** instead of **client**

This is one of the MOST IMPORTANT Canvas App concepts for the exam.

```
┌────────────────────────────────────────────────────────────┐
│                  DELEGABLE vs NON-DELEGABLE                 │
├────────────────────────────────────────────────────────────┤
│                                                             │
│  DELEGABLE (Good - Scalable)                               │
│  ┌─────────────────────────────────────────────────────┐  │
│  │  1. Canvas App sends query to data source           │  │
│  │  2. Data source filters 1,000,000 records           │  │
│  │  3. Returns only matching 50 records                │  │
│  │  4. App displays 50 records                         │  │
│  └─────────────────────────────────────────────────────┘  │
│         Fast, efficient, scales to millions               │
│                                                             │
│  NON-DELEGABLE (Bad - Limited)                             │
│  ┌─────────────────────────────────────────────────────┐  │
│  │  1. Canvas App requests data from source            │  │
│  │  2. Data source returns 2,000 records (limit!)      │  │
│  │  3. App filters 2,000 records locally               │  │
│  │  4. App displays matching records from 2,000        │  │
│  └─────────────────────────────────────────────────────┘  │
│       Slow, limited to 2,000 records, incomplete data    │
│                                                             │
└────────────────────────────────────────────────────────────┘
```

### The 2,000 Record Limit

By default, Canvas Apps retrieve a maximum of **2,000 records** from non-delegable queries.

- Can be changed in app settings (max 2,000)
- If your data has 10,000 records but query is non-delegable, you only see 2,000
- **This is a trap in exam questions!**

### Delegable Data Sources

| Data Source | Delegation Support |
|-------------|-------------------|
| **Dataverse** | Full delegation support (best) |
| **SharePoint** | Partial delegation support |
| **SQL Server** | Full delegation support |
| **Excel** | NO delegation (client-side only) |
| **Collections** | NO delegation (client-side only) |
| **OneDrive** | NO delegation |

### Delegable Functions

**Fully Delegable (Dataverse):**
- `Filter()`
- `Search()`
- `LookUp()`
- `Sort()`
- `SortByColumns()`
- `Sum()`, `Average()`, `Min()`, `Max()`, `CountRows()`

**NOT Delegable:**
- `First()` - only returns first from delegable query
- `Last()` - NOT delegable
- `CountIf()` - NOT delegable (use CountRows(Filter()))
- `StartsWith()` in some data sources
- Complex formulas with multiple conditions

### Delegation Warnings

Canvas Apps show a **blue underline warning** when a formula is non-delegable:

```powerapps
// Non-delegable - warning appears
Filter(Accounts, Left('Account Name', 3) = "Con")

// Delegable - no warning
Filter(Accounts, StartsWith('Account Name', "Con"))
```

> **Exam Tip**: If a question says "app must work with tens of thousands of records", the answer MUST use delegable formulas. Non-delegable formulas = limited to 2,000 records.

### Exam Scenarios with Delegation

**Scenario 1**: "App shows only 500 records but table has 10,000. What's wrong?"
- **Answer**: Non-delegable formula, app limited to 2,000 records (or less if modified)

**Scenario 2**: "App must display all products, including 50,000 in database. Which data source?"
- **Answer**: Dataverse or SQL Server (full delegation), NOT Excel or Collections

**Scenario 3**: "Filter isn't working correctly in SharePoint list with 3,000 items"
- **Answer**: Formula might be non-delegable, check for blue delegation warning

---

## Formulas and Functions

### Power Fx - The Formula Language

Canvas Apps use **Power Fx**, a low-code language similar to Excel formulas.

### Formula Categories

#### 1. Behavior Functions (Side Effects)

These functions **perform actions**:

| Function | Purpose | Example |
|----------|---------|---------|
| `Navigate()` | Go to another screen | `Navigate(DetailsScreen, ScreenTransition.Fade)` |
| `Patch()` | Create or update records | `Patch(Accounts, Record, {Name: "Contoso"})` |
| `Remove()` | Delete record | `Remove(Accounts, Gallery1.Selected)` |
| `Collect()` | Add to collection | `Collect(MyCollection, {Name: "Item1"})` |
| `ClearCollect()` | Replace collection | `ClearCollect(MyCollection, Accounts)` |
| `Set()` | Set global variable | `Set(varUserName, User().FullName)` |
| `UpdateContext()` | Set context variable | `UpdateContext({locLoading: true})` |
| `Notify()` | Show notification | `Notify("Saved!", NotificationType.Success)` |
| `Reset()` | Reset control to default | `Reset(Form1)` |
| `SubmitForm()` | Submit form | `SubmitForm(Form1)` |

#### 2. Data Functions

These functions **retrieve or transform data**:

| Function | Purpose | Example |
|----------|---------|---------|
| `Filter()` | Filter records | `Filter(Accounts, Status = "Active")` |
| `Search()` | Search text fields | `Search(Accounts, SearchInput.Text, "Account Name")` |
| `LookUp()` | Find single record | `LookUp(Accounts, 'Account ID' = GUID("..."))` |
| `Sort()` | Sort records | `Sort(Accounts, 'Created On', Descending)` |
| `SortByColumns()` | Sort by multiple | `SortByColumns(Accounts, "Name", Ascending)` |
| `AddColumns()` | Add calculated column | `AddColumns(Accounts, "FullName", 'First Name' & " " & 'Last Name')` |
| `ShowColumns()` | Select specific columns | `ShowColumns(Accounts, "Account Name", "City")` |
| `DropColumns()` | Exclude columns | `DropColumns(Accounts, "CreatedBy", "ModifiedBy")` |
| `GroupBy()` | Group records | `GroupBy(Accounts, "City", "CityGroup")` |
| `CountRows()` | Count records | `CountRows(Filter(Accounts, Status = "Active"))` |
| `Sum()` | Sum values | `Sum(Opportunities, 'Estimated Revenue')` |
| `Average()` | Average values | `Average(Opportunities, 'Estimated Revenue')` |

#### 3. Text Functions

| Function | Purpose | Example |
|----------|---------|---------|
| `Concatenate()` or `&` | Join text | `"Hello " & User().FullName` |
| `Left()` | First characters | `Left("Microsoft", 5)` → "Micro" |
| `Right()` | Last characters | `Right("Microsoft", 4)` → "soft" |
| `Mid()` | Middle characters | `Mid("Microsoft", 2, 4)` → "icro" |
| `Upper()` | Convert to uppercase | `Upper("hello")` → "HELLO" |
| `Lower()` | Convert to lowercase | `Lower("HELLO")` → "hello" |
| `Trim()` | Remove extra spaces | `Trim("  hello  ")` → "hello" |
| `Len()` | Length of text | `Len("Microsoft")` → 9 |
| `Replace()` | Replace text | `Replace("Hello", "H", "Y")` → "Yello" |
| `Split()` | Split into table | `Split("A,B,C", ",")` → ["A", "B", "C"] |

#### 4. Date/Time Functions

| Function | Purpose | Example |
|----------|---------|---------|
| `Now()` | Current date/time | `Now()` |
| `Today()` | Current date (no time) | `Today()` |
| `DateAdd()` | Add to date | `DateAdd(Today(), 7, Days)` |
| `DateDiff()` | Difference between dates | `DateDiff(StartDate, EndDate, Days)` |
| `Year()`, `Month()`, `Day()` | Extract parts | `Year(Now())` → 2026 |
| `Hour()`, `Minute()`, `Second()` | Extract time parts | `Hour(Now())` |
| `Date()` | Create date | `Date(2026, 1, 15)` |
| `Time()` | Create time | `Time(14, 30, 0)` |
| `Text()` | Format date | `Text(Now(), "mm/dd/yyyy")` |

#### 5. Logical Functions

| Function | Purpose | Example |
|----------|---------|---------|
| `If()` | Conditional logic | `If(Status = "Active", "Green", "Red")` |
| `Switch()` | Multiple conditions | `Switch(Status, "Active", "Green", "Inactive", "Red", "Gray")` |
| `And()` | Logical AND | `And(Status = "Active", Revenue > 1000)` |
| `Or()` | Logical OR | `Or(Status = "Active", Priority = "High")` |
| `Not()` | Logical NOT | `Not(IsBlank(TextInput1.Text))` |
| `IsBlank()` | Check if empty | `IsBlank(TextInput1.Text)` |
| `IsEmpty()` | Check if no records | `IsEmpty(Filter(Accounts, Status = "Active"))` |
| `IsError()` | Check for errors | `IsError(DataSource)` |

### Formula Context

Formulas run in different contexts:

| Context | When Used | Example |
|---------|-----------|---------|
| **App OnStart** | When app launches | Load lookup data |
| **Screen OnVisible** | When screen appears | Refresh data for screen |
| **Control Property** | Continuous evaluation | `Gallery1.AllItems` |
| **Event Handler** | User interaction | `Button1.OnSelect` |

### Chaining Formulas with Semicolon

Use `;` to run multiple formulas in sequence:

```powerapps
// Button OnSelect - multiple actions
Patch(Accounts, Record, {Status: "Active"});
Notify("Account activated!", NotificationType.Success);
Navigate(HomeScreen, ScreenTransition.Fade)
```

> **Exam Tip**: The semicolon (`;`) is critical for running multiple actions in sequence. This is commonly tested.

---

## Variables and State Management

### Three Types of Variables

Understanding variable scope is essential:

```
┌────────────────────────────────────────────────────────────┐
│                      VARIABLE TYPES                         │
├────────────────────────────────────────────────────────────┤
│                                                             │
│  GLOBAL VARIABLE (Set)                                     │
│  ┌─────────────────────────────────────────────────────┐  │
│  │  Set(gblUserName, User().FullName)                  │  │
│  │                                                      │  │
│  │  • Available in ALL screens                         │  │
│  │  • Prefix: gbl (convention)                         │  │
│  │  • Use: App-wide data                               │  │
│  └─────────────────────────────────────────────────────┘  │
│                                                             │
│  CONTEXT VARIABLE (UpdateContext)                          │
│  ┌─────────────────────────────────────────────────────┐  │
│  │  UpdateContext({locLoading: true})                  │  │
│  │                                                      │  │
│  │  • Available in CURRENT screen only                 │  │
│  │  • Prefix: loc (convention)                         │  │
│  │  • Use: Screen-specific data                        │  │
│  └─────────────────────────────────────────────────────┘  │
│                                                             │
│  COLLECTION (Collect)                                      │
│  ┌─────────────────────────────────────────────────────┐  │
│  │  Collect(colItems, {Name: "Item1", Price: 100})    │  │
│  │                                                      │  │
│  │  • Available in ALL screens                         │  │
│  │  • Prefix: col (convention)                         │  │
│  │  • Use: Table data (multiple records)               │  │
│  └─────────────────────────────────────────────────────┘  │
│                                                             │
└────────────────────────────────────────────────────────────┘
```

### Global Variables

**Created with**: `Set()`

```powerapps
// Create or update global variable
Set(gblCurrentUser, User().FullName)
Set(gblTheme, "Dark")
Set(gblSelectedAccount, LookUp(Accounts, ID = 123))

// Use anywhere in the app
Label1.Text = gblCurrentUser
```

**Use Cases:**
- User information
- App settings
- Selected records to pass between screens
- Theme/configuration

### Context Variables

**Created with**: `UpdateContext()`

```powerapps
// Create or update context variable (current screen only)
UpdateContext({locLoading: true})
UpdateContext({locErrorMessage: "Failed to save"})
UpdateContext({
    locSortColumn: "Name",
    locSortOrder: SortOrder.Ascending
})

// Use only in current screen
LoadingSpinner.Visible = locLoading
```

**Use Cases:**
- Loading indicators
- Screen-specific filters
- Temporary UI state
- Error messages for screen

> **Exam Tip**: Use `UpdateContext()` for screen-specific data, `Set()` for app-wide data. Context variables are NOT accessible in other screens.

### Collections

**Created with**: `Collect()` or `ClearCollect()`

```powerapps
// Create collection
ClearCollect(colProducts,
    {Name: "Product A", Price: 100},
    {Name: "Product B", Price: 200}
)

// Add to collection
Collect(colProducts, {Name: "Product C", Price: 150})

// Use collection
Gallery1.Items = colProducts

// Filter collection (non-delegable!)
Filter(colProducts, Price > 100)

// Count
CountRows(colProducts)

// Clear
Clear(colProducts)
```

**Use Cases:**
- Temporary data storage
- Offline data
- Combining data from multiple sources
- Building data for display

**Important**: Collections are **NOT delegable**. Filtering collections is client-side only.

### Variable Naming Conventions

| Type | Prefix | Example |
|------|--------|---------|
| Global | `gbl` | `gblUserName` |
| Context | `loc` | `locLoading` |
| Collection | `col` | `colProducts` |

These are conventions, not requirements, but following them makes code readable.

---

## Controls Deep Dive

### Essential Control Types

#### Input Controls

| Control | Purpose | Key Properties |
|---------|---------|----------------|
| **Text Input** | Single-line text | `Text`, `Default`, `HintText`, `Mode` |
| **Text Box** | Multi-line text | `Text`, `Default`, `HintText` |
| **Date Picker** | Date selection | `SelectedDate`, `DefaultDate`, `Format` |
| **Dropdown** | Single selection from list | `Items`, `Selected`, `Default` |
| **Combo Box** | Search and select | `Items`, `SelectedItems`, `DefaultSelectedItems` |
| **Checkbox** | True/false | `Value`, `Default` |
| **Toggle** | On/off switch | `Value`, `Default` |
| **Slider** | Numeric range | `Value`, `Min`, `Max`, `Default` |
| **Radio** | Mutually exclusive options | `Items`, `Selected`, `Default` |

#### Display Controls

| Control | Purpose | Key Properties |
|---------|---------|----------------|
| **Label** | Display text | `Text`, `Color`, `Font` |
| **HTML Text** | Rich formatted text | `HtmlText` (supports HTML) |
| **Image** | Display images | `Image` (URL or base64) |
| **Icon** | Display icons | `Icon` (from library) |
| **Video** | Play videos | `Media` (URL) |
| **PDF Viewer** | Display PDFs | `Document` (URL or base64) |

#### Action Controls

| Control | Purpose | Key Properties |
|---------|---------|----------------|
| **Button** | Trigger actions | `OnSelect`, `Text`, `DisplayMode` |
| **Timer** | Scheduled actions | `Duration`, `OnTimerEnd`, `Start` |

#### Container Controls

| Control | Purpose | Key Properties |
|---------|---------|----------------|
| **Gallery** | Display lists | `Items`, `TemplateSize`, `Layout` |
| **Form** | Edit/display records | `DataSource`, `Item`, `Mode` |
| **Container** | Group controls | `Fill`, `BorderColor` |
| **Group** | Logical grouping | Position controls together |

### Control Properties

Every control has properties accessible in formulas:

```powerapps
// Common properties
TextInput1.Text          // Current text
Dropdown1.Selected       // Selected item (record)
Dropdown1.SelectedText   // Selected text value
Gallery1.Selected        // Selected item in gallery
Button1.DisplayMode      // Edit, View, or Disabled
Form1.Valid              // Is form valid?
Form1.Error              // Form error message
Form1.Unsaved            // Has unsaved changes?
```

### Display Modes

Controls have three display modes:

| Mode | Description | Use Case |
|------|-------------|----------|
| `DisplayMode.Edit` | User can interact | Normal operation |
| `DisplayMode.View` | Display only (no interaction) | Read-only display |
| `DisplayMode.Disabled` | Grayed out, no interaction | Not available |

```powerapps
// Button is only enabled when form is valid
Button1.DisplayMode = If(Form1.Valid, DisplayMode.Edit, DisplayMode.Disabled)
```

### Visibility

Control visibility with `Visible` property:

```powerapps
// Show loading spinner while data loads
LoadingSpinner.Visible = IsEmpty(Gallery1.AllItems)

// Show error message only when there's an error
ErrorLabel.Visible = Not(IsBlank(locErrorMessage))

// Toggle section visibility
DetailsSection.Visible = gblShowDetails
```

---

## Navigation and Context

### Screen Navigation

Use `Navigate()` to move between screens:

```powerapps
// Basic navigation
Navigate(DetailsScreen)

// With transition effect
Navigate(DetailsScreen, ScreenTransition.Fade)
Navigate(HomeScreen, ScreenTransition.Cover)

// Navigate back
Back()
```

### Screen Transitions

| Transition | Effect |
|------------|--------|
| `ScreenTransition.None` | No animation |
| `ScreenTransition.Fade` | Fade in/out |
| `ScreenTransition.Cover` | Slide over from right |
| `ScreenTransition.CoverRight` | Slide over from left |
| `ScreenTransition.UnCover` | Slide away to right |
| `ScreenTransition.UnCoverRight` | Slide away to left |

### Passing Data Between Screens

**Method 1: Global Variables**

```powerapps
// Screen 1 - Set variable and navigate
Set(gblSelectedAccount, Gallery1.Selected);
Navigate(DetailsScreen)

// Screen 2 - Use variable
Label1.Text = gblSelectedAccount.'Account Name'
```

**Method 2: Context Variables**

```powerapps
// Screen 1 - Set context and navigate
Navigate(DetailsScreen, ScreenTransition.Fade,
    {locAccount: Gallery1.Selected})

// Screen 2 - Use context variable
Label1.Text = locAccount.'Account Name'
```

> **Exam Tip**: Method 2 (context in Navigate) is cleaner and more modern. But both methods work and can be tested.

### Screen OnVisible

The `OnVisible` property runs when screen appears:

```powerapps
// Refresh data when screen appears
Screen1.OnVisible = ClearCollect(colAccounts, Accounts)

// Set default values
Screen1.OnVisible = UpdateContext({locSortOrder: "Name"})

// Multiple actions
Screen1.OnVisible =
    ClearCollect(colAccounts, Accounts);
    UpdateContext({locLoading: false})
```

### App OnStart

The `App.OnStart` property runs once when app launches:

```powerapps
// Load lookup data
ClearCollect(colCountries, Countries);
ClearCollect(colIndustries, Industries);

// Set global variables
Set(gblCurrentUser, User());
Set(gblTheme, "Light")
```

**Triggering OnStart during development:**
- Click "..." menu → Run OnStart
- Or use F5 to restart app

---

## Forms - Edit, Display, and New

### Form Control Deep Dive

The **Form** control is one of the most powerful controls in Canvas Apps:

```
┌────────────────────────────────────────────────────────────┐
│                      FORM ARCHITECTURE                      │
├────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌─────────────────────────────────────────────────────┐  │
│  │                    Form Control                      │  │
│  │  ┌───────────────────────────────────────────────┐  │  │
│  │  │  DataSource: Accounts                         │  │  │
│  │  │  Item: Gallery1.Selected                      │  │  │
│  │  │  Mode: FormMode.Edit                          │  │  │
│  │  └───────────────────────────────────────────────┘  │  │
│  │                                                      │  │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  │  │
│  │  │ Data Card 1 │  │ Data Card 2 │  │ Data Card 3 │  │  │
│  │  │             │  │             │  │             │  │  │
│  │  │ Account Name│  │    City     │  │   Status    │  │  │
│  │  │  (Required) │  │  (Optional) │  │  (Locked)   │  │  │
│  │  └─────────────┘  └─────────────┘  └─────────────┘  │  │
│  └─────────────────────────────────────────────────────┘  │
│                                                             │
└────────────────────────────────────────────────────────────┘
```

### Form Modes

| Mode | Purpose | When Used |
|------|---------|-----------|
| `FormMode.View` | Display only | Show record details |
| `FormMode.Edit` | Edit existing record | Update record |
| `FormMode.New` | Create new record | Add new record |

### Key Form Properties

| Property | Purpose | Example |
|----------|---------|---------|
| `DataSource` | Table to work with | `Accounts` |
| `Item` | Record to display/edit | `Gallery1.Selected` |
| `Mode` | View/Edit/New | `FormMode.Edit` |
| `Valid` | Is form valid? | `Form1.Valid` |
| `Error` | Error message | `Form1.Error` |
| `Unsaved` | Has unsaved changes? | `Form1.Unsaved` |
| `Updates` | Changes to save | `Form1.Updates` |
| `LastSubmit` | Last saved record | `Form1.LastSubmit` |

### Form Functions

| Function | Purpose | Example |
|----------|---------|---------|
| `SubmitForm()` | Save form changes | `SubmitForm(Form1)` |
| `ResetForm()` | Reset to original | `ResetForm(Form1)` |
| `EditForm()` | Switch to edit mode | `EditForm(Form1)` |
| `NewForm()` | Switch to new mode | `NewForm(Form1)` |
| `ViewForm()` | Switch to view mode | `ViewForm(Form1)` |

### Form Events

| Event | When Fired | Use Case |
|-------|------------|----------|
| `OnSuccess` | After successful save | Navigate to another screen, show notification |
| `OnFailure` | After save failure | Show error message |
| `OnReset` | After form reset | Clear variables |

### Complete Form Example

**Browse Screen** (Gallery):
```powerapps
Gallery1.Items = Accounts
Gallery1.OnSelect = Navigate(DetailScreen)
```

**Detail Screen** (Form):
```powerapps
// Form properties
Form1.DataSource = Accounts
Form1.Item = Gallery1.Selected
Form1.Mode = FormMode.Edit

// Save button
SaveButton.OnSelect = SubmitForm(Form1)
SaveButton.DisplayMode = If(Form1.Valid, DisplayMode.Edit, DisplayMode.Disabled)

// Cancel button
CancelButton.OnSelect = ResetForm(Form1); Back()

// Form OnSuccess
Form1.OnSuccess = Notify("Saved!", NotificationType.Success); Back()

// Form OnFailure
Form1.OnFailure = Notify("Error: " & Form1.Error, NotificationType.Error)
```

**New Screen** (Form):
```powerapps
Form1.Mode = FormMode.New
Form1.OnVisible = NewForm(Form1)  // Reset to blank form
```

### Data Cards

Each field in a form is a **Data Card**:
- Auto-generated based on data type
- Can be unlocked and customized
- Can add/remove cards
- Can lock cards (read-only)

**Unlocking a Data Card:**
1. Select data card
2. Click "Advanced" tab
3. Click "Unlock to change properties"
4. Modify controls inside card

> **Exam Tip**: Forms automatically handle data type validation (e.g., required fields, email format). Use `Form1.Valid` to check if all validations pass.

---

## Gallery Controls

### Understanding Galleries

Galleries display **collections of items** (records) from a data source:

```
┌────────────────────────────────────────────────────────────┐
│                       GALLERY CONTROL                       │
├────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌───────────────────────────────────────────────────────┐ │
│  │ Gallery1.Items = Filter(Accounts, Status = "Active") │ │
│  └───────────────────────────────────────────────────────┘ │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ ┌──────────── Template ────────────┐                │   │
│  │ │ Title:    ThisItem.'Account Name'│  <- Repeated  │   │
│  │ │ Subtitle: ThisItem.City          │     for each  │   │
│  │ │ Image:    ThisItem.Logo          │     record    │   │
│  │ └──────────────────────────────────┘                │   │
│  │                                                      │   │
│  │ ┌──────────────────────────────────┐                │   │
│  │ │ Contoso Manufacturing           │                │   │
│  │ │ Seattle                          │                │   │
│  │ └──────────────────────────────────┘                │   │
│  │ ┌──────────────────────────────────┐                │   │
│  │ │ Fabrikam Inc                     │                │   │
│  │ │ New York                         │                │   │
│  │ └──────────────────────────────────┘                │   │
│  │ ┌──────────────────────────────────┐                │   │
│  │ │ Adventure Works                  │                │   │
│  │ │ Chicago                          │                │   │
│  │ └──────────────────────────────────┘                │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
└────────────────────────────────────────────────────────────┘
```

### Gallery Layouts

| Layout | Description | Use Case |
|--------|-------------|----------|
| **Vertical** | Stacked vertically | Most common |
| **Horizontal** | Scrolls left/right | Image carousels |
| **Flexible Height** | Height adjusts per item | Variable content length |

### Gallery Properties

| Property | Purpose | Example |
|----------|---------|---------|
| `Items` | Data source | `Filter(Accounts, Status = "Active")` |
| `Selected` | Currently selected item | `Gallery1.Selected.'Account Name'` |
| `TemplateSize` | Height of each item | `100` |
| `TemplatePadding` | Space between items | `5` |
| `AllItems` | All items (even filtered) | `CountRows(Gallery1.AllItems)` |

### ThisItem Context

Inside a gallery, `ThisItem` refers to the current record:

```powerapps
// Inside gallery template
Title.Text = ThisItem.'Account Name'
Subtitle.Text = ThisItem.City
Image.Image = ThisItem.Logo

// Conditional formatting
Rectangle.Fill = If(ThisItem.Status = "Active", Green, Red)
```

### Gallery Selection

```powerapps
// Handle selection
Gallery1.OnSelect = Set(gblSelectedAccount, ThisItem)

// Navigate with selection
Gallery1.OnSelect = Navigate(DetailScreen)

// Use selected item elsewhere
Label1.Text = Gallery1.Selected.'Account Name'
Form1.Item = Gallery1.Selected
```

### Filtering Galleries

```powerapps
// Filter by status
Gallery1.Items = Filter(Accounts, Status = "Active")

// Filter by search box
Gallery1.Items = Filter(Accounts,
    StartsWith('Account Name', SearchBox.Text))

// Search across multiple fields
Gallery1.Items = Search(Accounts,
    SearchBox.Text,
    "Account Name", "City", "Industry")

// Sort
Gallery1.Items = Sort(Accounts, 'Account Name', Ascending)

// Sort and filter
Gallery1.Items = Sort(
    Filter(Accounts, Status = "Active"),
    'Account Name',
    Ascending
)
```

### Gallery Patterns

**Pattern 1: Search Box**
```powerapps
Gallery1.Items = Search(Accounts, SearchInput.Text, "Account Name")
```

**Pattern 2: Dropdown Filter**
```powerapps
Gallery1.Items = If(
    IsBlank(FilterDropdown.Selected.Value),
    Accounts,  // Show all
    Filter(Accounts, Status = FilterDropdown.Selected.Value)
)
```

**Pattern 3: Pagination**
```powerapps
// Show 10 items per page
Gallery1.Items = FirstN(Accounts, 10 * gblCurrentPage)

// Next button
NextButton.OnSelect = Set(gblCurrentPage, gblCurrentPage + 1)

// Previous button
PrevButton.OnSelect = Set(gblCurrentPage, Max(1, gblCurrentPage - 1))
```

> **Exam Tip**: Use `Search()` for searching across multiple text fields. Use `Filter()` for specific field conditions. Both are delegable with Dataverse.

---

## Components and Reusability

### Custom Components

Components are **reusable UI elements** that can be used across screens or apps:

```
┌────────────────────────────────────────────────────────────┐
│                      COMPONENT STRUCTURE                    │
├────────────────────────────────────────────────────────────┤
│                                                             │
│  Component: HeaderComponent                                │
│  ┌─────────────────────────────────────────────────────┐  │
│  │  Input Properties:                                  │  │
│  │    • Title (Text)                                   │  │
│  │    • BackButtonVisible (Boolean)                    │  │
│  │                                                      │  │
│  │  Visual Design:                                     │  │
│  │    ┌────────────────────────────────────────────┐  │  │
│  │    │ [←] {Title}                    [Profile]   │  │  │
│  │    └────────────────────────────────────────────┘  │  │
│  │                                                      │  │
│  │  Output Properties:                                 │  │
│  │    • OnBackClick (Behavior)                         │  │
│  │                                                      │  │
│  └─────────────────────────────────────────────────────┘  │
│                                                             │
│  Usage:                                                    │
│  HeaderComponent.Title = "Account Details"                │
│  HeaderComponent.BackButtonVisible = true                 │
│                                                             │
└────────────────────────────────────────────────────────────┘
```

### Creating Components

1. **Components** tab (left pane)
2. **New component**
3. Add controls to component
4. Define custom properties

### Component Properties

**Input Properties**: Pass data TO the component
```powerapps
// In component definition
Title (Text) = "Default Title"

// When using component
HeaderComponent.Title = "Account Details"
```

**Output Properties**: Get data FROM the component
```powerapps
// In component, create output property
OnBackClick (Behavior) = Back()

// When using component
HeaderComponent.OnBackClick
```

### Component Libraries

Components can be shared across apps using **Component Libraries**:
1. Create Canvas App
2. Set as **Component Library**
3. Add components
4. Publish
5. Import into other apps

> **Exam Tip**: Component Libraries enable reuse across multiple apps. Regular components are only reusable within the same app.

---

## Integration with Dataverse

### Dataverse as Primary Data Source

Canvas Apps work seamlessly with Dataverse:

```powerapps
// Connect to Dataverse table
Gallery1.Items = Accounts

// Filter
Gallery1.Items = Filter(Accounts, 'Account Name' = "Contoso")

// Create record
Patch(Accounts, Defaults(Accounts), {
    'Account Name': "New Account",
    City: "Seattle",
    Status: 'Status'.Active
})

// Update record
Patch(Accounts, Gallery1.Selected, {
    City: "Portland"
})

// Delete record
Remove(Accounts, Gallery1.Selected)
```

### Choice Fields (Option Sets)

Dataverse choice fields require special syntax:

```powerapps
// Choice field with Choices() function
Dropdown1.Items = Choices(Accounts.Status)

// Set choice value
Patch(Accounts, Record, {
    Status: 'Status'.Active  // Note the syntax!
})

// Read choice label
Gallery1.Selected.Status.Value  // Shows internal value
Gallery1.Selected.Status.Label  // Shows display label (localized)
```

> **Exam Tip**: Choice fields use `TableName.'FieldName'.ChoiceValue` syntax. Always use `.Label` for display, not `.Value`.

### Lookup Fields

```powerapps
// Get related record data
Gallery1.Selected.'Primary Contact'.'Full Name'

// Set lookup (must provide record)
Patch(Accounts, Record, {
    'Primary Contact': LookUp(Contacts, 'Contact ID' = GUID("..."))
})
```

### Dataverse Formulas (Server-Side)

Dataverse supports formulas (calculated columns):
- Calculated in Canvas Apps automatically
- Read-only
- No formula needed in app

### Business Rules

Dataverse business rules can run in Canvas Apps:
- Only **entity-scoped** business rules
- Form-scoped rules do NOT run
- Canvas App can check validations

---

## Performance and Optimization

### Performance Best Practices

#### 1. Minimize Data Calls

**Bad:**
```powerapps
// Calls data source every time
Label1.Text = CountRows(Accounts)
Label2.Text = CountRows(Accounts)
Label3.Text = CountRows(Accounts)
```

**Good:**
```powerapps
// Store in variable
App.OnStart = Set(gblAccountCount, CountRows(Accounts))

Label1.Text = gblAccountCount
Label2.Text = gblAccountCount
Label3.Text = gblAccountCount
```

#### 2. Use Delegation

Always check for blue delegation warnings:
- Rewrite formulas to be delegable
- Use Dataverse for large datasets
- Avoid Excel for large data

#### 3. Optimize Gallery Items

**Bad:**
```powerapps
// Loads ALL records, then displays
Gallery1.Items = Accounts
```

**Good:**
```powerapps
// Filter first (delegable)
Gallery1.Items = Filter(Accounts, Status = "Active")

// Or use pagination
Gallery1.Items = FirstN(Accounts, 100)
```

#### 4. Use Collections for Static Data

```powerapps
// Load once in App.OnStart
ClearCollect(colCountries, Countries)

// Use collection repeatedly (no network calls)
Dropdown1.Items = colCountries
```

#### 5. Concurrent Function

Load multiple data sources in parallel:

```powerapps
Concurrent(
    ClearCollect(colAccounts, Accounts),
    ClearCollect(colContacts, Contacts),
    ClearCollect(colLeads, Leads)
)
```

### Monitoring Performance

Use **App Checker** (Power Apps Studio):
- Errors
- Warnings
- Performance issues
- Accessibility issues

### Data Row Limits

Remember the limits:
- Non-delegable queries: 2,000 records (max)
- Can be adjusted in settings (still max 2,000)
- Delegable queries: Unlimited

---

## Publishing, Sharing, and Versioning

### Publishing an App

**Steps:**
1. **File** > **Save**
2. Enter version notes
3. Click **Publish**

**Important**: Saving does NOT publish. Users see the last published version.

### Versioning

Apps maintain version history:
- Each publish creates a new version
- Can restore previous versions
- Version notes help track changes

**Restore Previous Version:**
1. **File** > **See all versions**
2. Select version
3. **Restore**

> **Exam Tip**: Publishing is separate from saving. Only published versions are available to users.

### Sharing Apps

**Share with Users:**
1. **Share** button
2. Enter user names or groups
3. Select permission level
4. Send invitation

**Permission Levels:**
- **User**: Can run the app
- **Co-owner**: Can edit and share

### Canvas Apps in Solutions

Canvas Apps can be added to **Solutions** for ALM:
1. Create/open solution
2. Add existing app or create new
3. Export solution (managed or unmanaged)
4. Import to other environments

**Benefits:**
- Environment lifecycle management
- Connection references (no hardcoded connections)
- Versioning
- Deployment pipelines

### Licensing

| License | Includes |
|---------|----------|
| **Power Apps per app** | Access to ONE app (plus portals) |
| **Power Apps per user** | Access to ALL apps (unlimited) |
| **Microsoft 365** | Standard connectors only |
| **Office 365** | Standard connectors only |

**Premium Connectors** require Power Apps per app or per user license:
- Dataverse
- SQL Server
- HTTP/Custom Connectors
- Many others

> **Exam Tip**: Dataverse connector is PREMIUM. Microsoft 365 license is NOT enough for Dataverse access.

---

## Common Exam Scenarios

### Scenario 1: Data Not Showing All Records

**Question**: "Gallery only shows 500 records but the table has 10,000 records. What's the problem?"

**Answer**:
- Formula is **non-delegable**
- Canvas Apps limited to 2,000 records (or less)
- **Fix**: Rewrite formula to be delegable or increase data row limit in settings

### Scenario 2: Which App Type for Custom UI?

**Question**: "Need complete control over UI design with mobile-first approach. Canvas or Model-Driven?"

**Answer**: **Canvas App**
- Model-driven apps are data-first with limited UI customization

### Scenario 3: Delegation with Excel

**Question**: "App using Excel as data source needs to handle 5,000 rows. Will this work?"

**Answer**: **No**
- Excel does NOT support delegation
- Limited to 2,000 records maximum
- **Solution**: Move data to Dataverse or SQL Server

### Scenario 4: Choice Field Access

**Question**: "How to display the text label of a Dataverse choice field?"

**Answer**:
```powerapps
Gallery1.Selected.Status.Label  // Correct - shows display text
Gallery1.Selected.Status.Value  // Wrong - shows internal value
```

### Scenario 5: Multiple Actions on Button Click

**Question**: "Button must save data, show notification, and navigate to home screen."

**Answer**: Use semicolon to chain actions:
```powerapps
SubmitForm(Form1);
Notify("Saved!", NotificationType.Success);
Navigate(HomeScreen)
```

### Scenario 6: Passing Data Between Screens

**Question**: "Pass selected account from gallery to detail screen."

**Answer**: **Two methods:**
```powerapps
// Method 1: Global variable
Set(gblSelectedAccount, Gallery1.Selected);
Navigate(DetailScreen)

// Method 2: Context in Navigate
Navigate(DetailScreen, ScreenTransition.Fade, {locAccount: Gallery1.Selected})
```

### Scenario 7: Form Not Saving

**Question**: "Save button clicked but form doesn't save. What's wrong?"

**Answer**: Likely causes:
1. Button uses `Patch()` instead of `SubmitForm()`
2. Form not in Edit mode: `EditForm(Form1)` or `Form1.Mode = FormMode.Edit`
3. Form validation failing: Check `Form1.Valid`
4. Missing required fields

**Correct implementation:**
```powerapps
SaveButton.OnSelect = SubmitForm(Form1)
SaveButton.DisplayMode = If(Form1.Valid, DisplayMode.Edit, DisplayMode.Disabled)
```

### Scenario 8: Dropdown Shows Wrong Values

**Question**: "Dropdown should show Dataverse choice field options but shows blank."

**Answer**: Use `Choices()` function:
```powerapps
Dropdown1.Items = Choices(Accounts.Status)
```

### Scenario 9: Searching Multiple Fields

**Question**: "Search box must search across Account Name, City, and Industry fields."

**Answer**: Use `Search()` function:
```powerapps
Gallery1.Items = Search(Accounts, SearchBox.Text, "Account Name", "City", "Industry")
```

### Scenario 10: License Requirements

**Question**: "App connects to Dataverse. What license do users need?"

**Answer**:
- **Power Apps per app** OR **Power Apps per user**
- Microsoft 365 / Office 365 licenses are NOT sufficient (Dataverse is premium)

### Scenario 11: Collection Performance

**Question**: "Gallery using collection with 5,000 items is slow. How to improve?"

**Answer**:
- Collections are NOT delegable
- Filtering 5,000 items is client-side
- **Solution**: Use Dataverse directly with delegable formulas instead of collection

### Scenario 12: Context Variable Not Available

**Question**: "Set context variable on Screen1 but can't access on Screen2. Why?"

**Answer**:
- Context variables (`UpdateContext()`) are **screen-specific**
- **Solution**: Use global variable (`Set()`) or pass in Navigate third parameter

---

## Key Takeaways for the Exam

1. **Canvas Apps have complete UI control** - pixel-perfect design vs model-driven auto-generated
2. **Delegation is critical** - non-delegable queries limited to 2,000 records
3. **Excel does NOT support delegation** - use Dataverse or SQL for large datasets
4. **Three variable types**: Global (`Set`), Context (`UpdateContext`), Collection (`Collect`)
5. **Context variables are screen-specific** - not accessible in other screens
6. **Semicolon chains multiple actions** - essential for button OnSelect
7. **SubmitForm() for forms** - not Patch() (forms handle complexity)
8. **Form1.Valid checks all validations** - use to enable/disable save button
9. **ThisItem in galleries** - refers to current record in template
10. **Search() for multiple fields** - delegable with Dataverse
11. **Filter() for specific conditions** - delegable with Dataverse
12. **Choice fields use .Label** - for display text, not .Value
13. **Choices() function for dropdowns** - to populate with Dataverse choice options
14. **Publishing is separate from saving** - users only see published versions
15. **Dataverse connector is PREMIUM** - requires Power Apps license (not M365)
16. **App.OnStart runs once** - load lookup data and set global defaults
17. **Screen.OnVisible runs each time** - refresh screen-specific data
18. **Navigate() third parameter** - passes context variables to target screen
19. **Collections are client-side** - NOT delegable, avoid for large datasets
20. **Concurrent() for parallel loading** - load multiple data sources simultaneously

---

*Continue to the next section: [Power Automate Deep Dive](./PL-200-PowerAutomate-DeepDive.md)*
