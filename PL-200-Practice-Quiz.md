# Microsoft PL-200 Practice Quiz
## Power Platform Functional Consultant Exam

---

**Instructions**: This quiz contains questions similar to those found on the PL-200 exam. Each question has one or more correct answers. Answers and explanations are provided at the end of each section.

---

## Section 1: Configure Microsoft Dataverse (25-30%)

### Question 1
You must create a new table to support a new feature for an app. Records for the table must be associated with a business unit and specify security roles for the business unit.

You need to configure table ownership.

**Which table ownership type should you use?**

- A) None
- B) Organization
- C) User or Team
- D) Business Unit

---

### Question 2
A company uses a Dataverse environment accessed from canvas and model-driven apps. The environment contains a table with the following columns: Name, Company, and Contacted On.

The company requires that the table not contain any duplicate rows.

**What should you create?**

- A) Duplicate detection rule
- B) Business rule
- C) Alternate key
- D) Calculated column

---

### Question 3
You create a custom table in Dataverse. The table must include a column that displays a calculated value that aggregates data from related child records. The aggregated value must include the sum of a currency column from the child table.

**Which type of column should you create?**

- A) Calculated column
- B) Formula column
- C) Rollup column
- D) Currency column with default value

---

### Question 4
You need to create a choice column that will be used across multiple tables in your Dataverse environment. Several tables need to use the same set of status values.

**What type of choice should you create?**

- A) Local choice
- B) Global choice
- C) Lookup column
- D) Multi-select choice

---

### Question 5
A company has the following security requirements:
- Users should be dynamically added to share specific records
- The sharing should not require the users to have a specific security role
- Team membership should be based on record-level requirements

**Which type of team should you use?**

- A) Owner team
- B) Access team
- C) AAD security group team
- D) Microsoft 365 group team

---

### Question 6
You need to create a relationship between two tables where:
- One Account can have multiple Contacts
- Each Contact belongs to only one Account

**What type of relationship should you create?**

- A) Many-to-Many relationship
- B) One-to-Many relationship from Account to Contact
- C) Polymorphic lookup
- D) Hierarchical relationship

---

### Question 7
You have a Many-to-Many relationship between Products and Categories. You need to add a custom field to track the date each product was added to each category.

**What should you do?**

- A) Add a column to the relationship table
- B) Create a new junction table with a One-to-Many relationship to both tables
- C) Add the date column to the Product table
- D) Use a calculated column on the Category table

---

### Question 8
You need to copy reference data from your development environment to your production environment.

**Which tool should you use?**

- A) Solution exporter
- B) Package Deployer
- C) Configuration Migration Tool
- D) Power Automate

---

### Question 9
An access level on a security role shows a filled circle symbol (●).

**What level of access does this represent?**

- A) None
- B) User (Basic)
- C) Business Unit (Local)
- D) Organization (Global)

---

### Question 10
You need to ensure that users can only see and edit records that they own, records owned by users in their business unit, and records owned by users in any child business units.

**Which access level should you configure?**

- A) User
- B) Business Unit
- C) Parent: Child (Deep)
- D) Organization

---

## Section 1 Answers

| Q | Answer | Explanation |
|---|--------|-------------|
| 1 | **C** | User or Team owned tables support security role assignments and business unit association. Organization-owned tables don't support this level of security granularity. |
| 2 | **C** | Alternate keys enforce uniqueness at the platform level across the combination of specified columns. Duplicate detection rules identify duplicates but don't prevent them. |
| 3 | **C** | Rollup columns aggregate data from related (child) records. Calculated and Formula columns work on data within the same record only. |
| 4 | **B** | Global choices can be reused across multiple tables. Local choices are specific to a single table. |
| 5 | **B** | Access teams are designed for dynamic, record-level sharing without requiring security roles. Owner teams own records and have security roles. |
| 6 | **B** | One-to-Many from Account (parent) to Contact (child) creates the correct relationship where one Account has many Contacts. |
| 7 | **B** | Many-to-Many relationships don't allow custom columns on the intersect table. You must create a custom junction table with 1:N relationships to both tables. |
| 8 | **C** | Configuration Migration Tool is specifically designed for copying reference/configuration data between environments. |
| 9 | **B** | The filled circle (●) represents User/Basic level - access only to records the user owns. |
| 10 | **C** | Parent: Child (Deep) access provides access to the user's BU and all subordinate BUs. |

---

## Section 2: Create Apps Using Power Apps (25-30%)

### Question 11
You are creating a model-driven app. Users need to quickly create new Contact records without navigating away from the Account form.

**Which form type should you configure?**

- A) Main form
- B) Quick View form
- C) Quick Create form
- D) Card form

---

### Question 12
You create a model-driven app. You need to display data from a parent Account record on the Contact main form.

**Which form type should you use?**

- A) Main form
- B) Quick View form
- C) Quick Create form
- D) Card form

---

### Question 13
You create a new case form that must include a timeline control and a business process flow.

**Which form type should you use?**

- A) Main form
- B) Quick View form
- C) Quick Create form
- D) Card form

---

### Question 14
A company needs a mobile-friendly case form that displays case data on an interactive dashboard.

**Which form type should you use?**

- A) Main form
- B) Quick View form
- C) Quick Create form
- D) Card form

---

### Question 15
You are creating a business rule for a model-driven app. The rule must apply only to a specific form named "Case Information Form" and not affect other forms or apps.

**What scope should you select?**

- A) Entity
- B) All Forms
- C) Case Information Form
- D) Global

---

### Question 16
You create a business rule that must apply to a canvas app that uses Dataverse as a data source.

**What scope should you configure for the business rule?**

- A) The specific form used by the canvas app
- B) All Forms
- C) Entity (Table)
- D) Canvas App scope

---

### Question 17
You have a canvas app with multiple screens. The app needs to:
- Store temporary data while running
- Each screen must maintain a separate copy of data
- Pass the data to another screen when navigating

**What type of variable should you use?**

- A) Global variable
- B) Context variable
- C) Collection
- D) Environment variable

---

### Question 18
Your canvas app needs to store tabular data where you can update separate rows independently.

**What type of variable should you use?**

- A) Global variable
- B) Context variable
- C) Collection
- D) SharePoint list

---

### Question 19
You have a canvas app with a Gallery control that displays products with checkboxes for selection. Users select multiple products, then navigate to a checkout screen. You need to implement a "Clear All" button that clears all selections.

**What is the correct approach?**

- A) Use Reset(Gallery1) to reset the gallery
- B) Use ForAll(Gallery1.AllItems, Reset(Checkbox1))
- C) Store selections in a collection using Collect/Remove on checkbox check/uncheck, then use Clear(CollectionName)
- D) Set the Default property of checkboxes to false

---

### Question 20
You are creating a canvas app that connects to Dataverse. The table contains 10,000 records. You use the Filter function with a non-delegable function.

**What happens when the app runs?**

- A) All 10,000 records are returned and filtered on the client
- B) Only the first 500 records are processed (default delegation limit)
- C) The app throws an error
- D) Dataverse processes all records server-side

---

### Question 21
You are configuring Power Pages. You need to allow all authenticated users to automatically access certain resources without manual permission assignment.

**What should you configure?**

- A) Create a Web Role and set "Anonymous Users Role" to Yes
- B) Create a Web Role and set "Authenticated Users Role" to Yes
- C) Assign a security role to all users
- D) Create a public page with no permissions

---

### Question 22
Your organization does not permit the use of custom code for solutions. You need to create a view that can be viewed by all users in an organization.

**Where should you create the view?**

- A) Microsoft Visual Studio
- B) JavaScript
- C) Templates area
- D) Maker portal

---

## Section 2 Answers

| Q | Answer | Explanation |
|---|--------|-------------|
| 11 | **C** | Quick Create forms allow rapid record creation without navigating away from the current form. |
| 12 | **B** | Quick View forms display read-only data from related (parent) records on another form. |
| 13 | **A** | Main forms support timeline controls and business process flows. Other form types don't. |
| 14 | **D** | Card forms are designed for compact, mobile-friendly display and interactive dashboards. |
| 15 | **C** | Selecting the specific form name limits the business rule to only that form. |
| 16 | **C** | Entity scope executes server-side in Dataverse, which is the only way business rules work with canvas apps. |
| 17 | **B** | Context variables are screen-specific and can be passed using Navigate() function. |
| 18 | **C** | Collections store tabular data and allow independent row updates using Patch, Remove, etc. |
| 19 | **C** | You cannot reset controls inside a Gallery from outside. Use collections to track selections and Clear() to reset. |
| 20 | **B** | Non-delegable operations only process records up to the delegation limit (default 500, max 2000). |
| 21 | **B** | Setting "Authenticated Users Role" to Yes automatically assigns the web role to anyone who logs in. |
| 22 | **D** | The Maker portal provides a no-code interface for creating views. |

---

## Section 3: Logic and Process Automation (25-30%)

### Question 23
You need to create a Power Automate flow that processes qualification records. For each record, you must check if it is valid and take different actions based on the status.

**Which control should you use?**

- A) Apply to each with Condition inside
- B) Switch
- C) Do until
- D) Scope

---

### Question 24
You are creating a Power Automate flow that creates several SharePoint list items. If any of the actions fail, you must revert all changes made by the flow.

**What should you implement?**

- A) Use a Condition action
- B) Use a Scope action with error handling
- C) Use the Terminate action
- D) Configure retry policy

---

### Question 25
You have a Power Automate flow that works in your development environment. You need to deploy this flow to production as part of a solution.

The production environment uses different connections than development.

**What should you do?**

- A) Create new connections in production after importing
- B) Convert connections to connection references before exporting
- C) Export the flow as a template
- D) Manually recreate the flow in production

---

### Question 26
You need to create a workflow that:
- Runs immediately when triggered
- Validates when a condition is met
- Sends an email based on a mail merge template

**Which workflow configuration should you use?**

- A) Background workflow triggered on create
- B) Real-time workflow triggered on create
- C) Power Automate scheduled flow
- D) Business Process Flow

---

### Question 27
You create a Business Process Flow that spans multiple tables. The process includes Accounts, Opportunities, and Orders.

**What is the maximum number of tables a Business Process Flow can span?**

- A) 3
- B) 5
- C) 10
- D) Unlimited

---

### Question 28
You need to create automation that guides users through a structured, multi-step sales process. Users must complete steps in a specific order, and managers need to track where each opportunity is in the process.

**What should you create?**

- A) Cloud flow with approval steps
- B) Business Process Flow
- C) Classic workflow
- D) Canvas app with multiple screens

---

### Question 29
A company needs to trigger a Power Automate flow from a canvas app button.

**Which trigger should you use?**

- A) When a record is created
- B) Recurrence
- C) When Power Apps calls a flow (V2)
- D) Manual trigger

---

### Question 30
You are creating a flow that needs to connect to a company's internal REST API web application as part of a nightly batch job.

**What type of flow should you create?**

- A) Scheduled cloud flow with HTTP connector
- B) Instant flow with custom connector
- C) Scheduled cloud flow with custom connector
- D) Desktop flow with API calls

---

### Question 31
You need to implement logic that repeats an action until a specific condition becomes true.

**Which control should you use?**

- A) Apply to each
- B) Do until
- C) Condition
- D) Switch

---

### Question 32
A Business Process Flow is created on the Lead table. You need to report on how long leads spend in each stage of the process.

**Where is this data stored?**

- A) In the Lead table directly
- B) In a separate BPF table created automatically
- C) In the audit log
- D) In Power BI only

---

## Section 3 Answers

| Q | Answer | Explanation |
|---|--------|-------------|
| 23 | **A** | Apply to each iterates through records, and Condition inside evaluates each record's status. |
| 24 | **B** | Scope actions allow you to group actions and configure error handling using "Run After" settings. |
| 25 | **B** | Connection references allow the solution to work across environments with different connections. |
| 26 | **B** | Real-time (synchronous) workflows run immediately. Background workflows run asynchronously. |
| 27 | **B** | Business Process Flows can span a maximum of 5 tables. |
| 28 | **B** | Business Process Flows are designed for guiding users through structured processes with tracking. |
| 29 | **C** | "When Power Apps calls a flow (V2)" trigger is specifically designed for canvas app integration. |
| 30 | **C** | Custom connectors are needed for internal REST APIs, and scheduled triggers work for batch jobs. |
| 31 | **B** | Do until loops continue executing until a condition becomes true. |
| 32 | **B** | Each BPF creates its own table that stores process instance data for reporting. |

---

## Section 4: Manage Environments (15-20%)

### Question 33
You are preparing to deploy a solution from your development environment to production.

**Which solution type should you export?**

- A) Unmanaged
- B) Managed
- C) Default
- D) Sandbox

---

### Question 34
You have imported a managed solution into your production environment. A bug is discovered in one of the components.

**What should you do?**

- A) Edit the component directly in production
- B) Delete the managed solution and recreate manually
- C) Fix the issue in development (unmanaged), export as managed, and import as an upgrade
- D) Convert the managed solution to unmanaged

---

### Question 35
A user reports that after you deleted an unmanaged solution, all the components were removed from the environment.

**Is this expected behavior?**

- A) Yes, deleting an unmanaged solution removes all components
- B) No, deleting an unmanaged solution only removes the solution container, not the components
- C) Only if the components were created within that solution
- D) It depends on the solution settings

---

### Question 36
You need to create an environment for individual development work. The environment should include a Dataverse database at no additional cost.

**Which environment type should you use?**

- A) Production
- B) Sandbox
- C) Developer
- D) Trial

---

### Question 37
You need to test a solution before deploying to production. The environment should allow you to reset data and start fresh if needed.

**Which environment type should you use?**

- A) Production
- B) Sandbox
- C) Developer
- D) Default

---

### Question 38
You are configuring environment security. You want to restrict access to a production environment to only members of a specific Azure AD security group.

**Which statement is true?**

- A) Security groups can be assigned to any environment type
- B) Security groups cannot be assigned to Default and Developer environments
- C) Security groups only work with Sandbox environments
- D) Security roles must be used instead of security groups

---

### Question 39
You have an environment variable in your solution. The variable has a Default Value set to "development.example.com". When you import the solution to production, you want the value to be "production.example.com".

**What should you configure?**

- A) Edit the Default Value after import
- B) Set a Current Value in the production environment
- C) Create a new environment variable
- D) Use a different solution for each environment

---

### Question 40
Before exporting a solution as managed, you want to prevent users in the target environment from being able to delete a specific table.

**What should you configure?**

- A) Security role permissions
- B) Managed properties
- C) Solution dependencies
- D) Table ownership

---

### Question 41
You need to analyze your solution for performance issues and deprecated features before deployment.

**Which tool should you use?**

- A) Power Automate
- B) Solution Checker
- C) Model-driven app designer
- D) Environment admin center

---

### Question 42
You are implementing ALM for your organization. Which statement correctly describes the flow of solutions between environments?

- A) Develop in managed, deploy as unmanaged
- B) Develop in unmanaged, deploy as managed
- C) Always use unmanaged solutions
- D) Always use managed solutions

---

## Section 4 Answers

| Q | Answer | Explanation |
|---|--------|-------------|
| 33 | **B** | Export as Managed for deployment to non-development environments. Managed solutions cannot be edited in the target environment. |
| 34 | **C** | You cannot edit managed solutions directly. Fix in the source (unmanaged) and redeploy. |
| 35 | **B** | Deleting an unmanaged solution only removes the container, not the components. This is a common exam trap! |
| 36 | **C** | Developer environments include Dataverse at no extra cost and are meant for individual development. |
| 37 | **B** | Sandbox environments are designed for testing and can be reset. |
| 38 | **B** | Security groups cannot be assigned to Default (shared with all) and Developer (single user) environments. |
| 39 | **B** | Current Value overrides Default Value and is environment-specific. Set the Current Value in production. |
| 40 | **B** | Managed properties control what can be customized/deleted in managed solutions. Configure before exporting. |
| 41 | **B** | Solution Checker analyzes solutions for issues, deprecated features, and best practices. |
| 42 | **B** | ALM best practice: develop in unmanaged solutions, export and deploy as managed. |

---

## Section 5: Mixed Topics & Scenarios

**Note**: Questions about Microsoft Copilot Studio (formerly Power Virtual Agents) have been removed as this topic was excluded from the PL-200 exam in January 2024.

### Question 43
You create a Power BI report and need to display it within a model-driven app.

**Which two actions should you take? (Choose two)**

- A) Add the Power BI report to the Site Map dashboards
- B) Embed the report using custom JavaScript
- C) Add the Power BI report to a dashboard in the model-driven app
- D) Convert the Power BI report to a Dataverse dashboard
- E) Use a canvas app to display the report

---

### Question 47
You need to assign 10% of incoming records to a quality review queue. The assignment must be automated and use table configuration.

**What should you create?**

- A) A calculated column and a business rule
- B) An autonumber column and a Power Automate flow that checks if the number is divisible by 10
- C) A rollup column and a workflow
- D) A random number formula column

---

### Question 45
A company has the following requirements for a Dataverse column:
- Must automatically generate a unique identifier
- The value cannot be edited after creation
- The sequence must be predictable

**What type of column should you create?**

- A) Text with default value
- B) Autonumber
- C) Calculated
- D) GUID

---

### Question 46
You need to create a lookup column that can reference either an Account OR a Contact record.

**What type of column should you create?**

- A) Standard lookup
- B) Many-to-many relationship
- C) Customer type (polymorphic lookup)
- D) Two separate lookup columns

---

## Section 5 Answers

| Q | Answer | Explanation |
|---|--------|-------------|
| 43 | **A, C** | Power BI reports can be added to site map dashboards and embedded in model-driven app dashboards. |
| 44 | **B** | Autonumber provides sequential numbers. A flow can check if divisible by 10 (every 10th = 10%). |
| 45 | **B** | Autonumber columns auto-generate sequential, read-only, predictable identifiers. |
| 46 | **C** | Customer type is a built-in polymorphic lookup that can reference Account or Contact. |

---

## Scoring Guide

| Score | Recommendation |
|-------|----------------|
| **45-50** | Excellent! You're ready for the exam. |
| **40-44** | Good foundation. Review weak areas. |
| **35-39** | More study needed. Focus on missed topics. |
| **30-34** | Significant gaps. Work through study guide thoroughly. |
| **Below 30** | Recommend comprehensive study before attempting exam. |

---

## Next Steps

1. **Review incorrect answers** - Read the explanations carefully
2. **Hands-on practice** - Try these scenarios in a Power Platform environment
3. **Official practice test** - Take the Microsoft Practice Assessment
4. **Study weak areas** - Use the Study Guide sections for topics you missed

**Good luck with your PL-200 exam!**

---

*Note: These questions are based on publicly available exam preparation materials and discussions. The actual exam may contain different questions. Always refer to the official Microsoft documentation and practice assessments for the most current information.*
