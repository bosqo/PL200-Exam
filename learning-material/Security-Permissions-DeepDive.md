# Dataverse Security and Permissions - Complete Deep Dive
## PL-200 Exam Preparation

---

## Table of Contents
1. [Dataverse Security Architecture](#dataverse-security-architecture)
2. [Business Units](#business-units)
3. [Security Roles Deep Dive](#security-roles-deep-dive)
4. [Access Levels and Inheritance](#access-levels-and-inheritance)
5. [Row-Level Security (RLS)](#row-level-security-rls)
6. [Column-Level Security](#column-level-security)
7. [Teams and Access Teams](#teams-and-access-teams)
8. [Sharing and Record Access](#sharing-and-record-access)
9. [Hierarchy Security](#hierarchy-security)
10. [Power Pages Security Model](#power-pages-security-model)
11. [Security Configuration Scenarios](#security-configuration-scenarios)
12. [Common Exam Scenarios](#common-exam-scenarios)

---

## Dataverse Security Architecture

### The Security Hierarchy

```
┌─────────────────────────────────────────────────────────────┐
│                         TENANT                               │
│  (Microsoft 365 Organization)                               │
│                                                              │
│  ┌──────────────────────────────────────────────────────┐   │
│  │                    ENVIRONMENT                        │   │
│  │  (Isolated Dataverse Database)                       │   │
│  │                                                       │   │
│  │  ┌────────────────────────────────────────────────┐  │   │
│  │  │        ROOT BUSINESS UNIT                      │  │   │
│  │  │  (Auto-created, organization-wide)             │  │   │
│  │  │                                                 │  │   │
│  │  │  ┌──────────────────────────────────────────┐ │  │   │
│  │  │  │  Child Business Unit: Sales               │ │  │   │
│  │  │  │  ┌────────────────────────────────────┐  │ │  │   │
│  │  │  │  │  Team: Sales_North                 │  │ │  │   │
│  │  │  │  │  ┌──────────────────────────────┐  │  │ │  │   │
│  │  │  │  │  │  Users (inherit team perms)  │  │  │ │  │   │
│  │  │  │  │  └──────────────────────────────┘  │  │ │  │   │
│  │  │  │  └────────────────────────────────────┘  │ │  │   │
│  │  │  │                                          │ │  │   │
│  │  │  │  ┌────────────────────────────────────┐  │ │  │   │
│  │  │  │  │  Team: Sales_South                │  │ │  │   │
│  │  │  │  │  (Same structure as Sales_North)  │  │ │  │   │
│  │  │  │  └────────────────────────────────────┘  │ │  │   │
│  │  │  └──────────────────────────────────────────┘ │  │   │
│  │  │                                                 │  │   │
│  │  │  ┌──────────────────────────────────────────┐ │  │   │
│  │  │  │  Child Business Unit: Marketing          │ │  │   │
│  │  │  │  ┌────────────────────────────────────┐  │ │  │   │
│  │  │  │  │  Team: Marketing_Digital           │  │ │  │   │
│  │  │  │  └────────────────────────────────────┘  │ │  │   │
│  │  │  └──────────────────────────────────────────┘ │  │   │
│  │  └────────────────────────────────────────────────┘  │   │
│  │                                                       │   │
│  │  Security Roles:                                      │   │
│  │  ├─ System Administrator (full access)               │   │
│  │  ├─ System Customizer (can customize)                │   │
│  │  ├─ Basic User (standard user)                       │   │
│  │  ├─ Environment Maker (create apps)                  │   │
│  │  └─ Custom Roles (organization-specific)             │   │
│  │                                                       │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Key Security Concepts

| Concept | Purpose | Scope |
|---------|---------|-------|
| **Tenant** | Organization | Microsoft 365 tenant |
| **Environment** | Isolation | Single Dataverse database |
| **Business Unit** | Organization | Users, teams, ownership |
| **Security Role** | Permissions | What can do (CRUD operations) |
| **Access Level** | Filtering | Which records can see |
| **Sharing** | Ad-hoc Access | Individual record access |
| **Teams** | Grouping | Users with shared security |

---

## Business Units

### What are Business Units?

Business Units are **organizational containers** for:
- Users
- Teams
- Ownership
- Security role assignment
- Hierarchy-based access control

```
Business Unit Structure:
┌────────────────────────────────┐
│     Root Business Unit         │
│  (Everyone in organization)    │
│                                │
├─ Sales (Child)                 │
│  ├─ North Region (Child)       │
│  │  ├─ Sales Rep 1             │
│  │  └─ Sales Rep 2             │
│  └─ South Region (Child)       │
│     ├─ Sales Rep 3             │
│     └─ Sales Rep 4             │
│                                │
└─ Marketing (Child)             │
   ├─ Digital (Child)            │
   │  ├─ Marketing Specialist 1  │
   │  └─ Marketing Specialist 2  │
   └─ Events (Child)             │
      ├─ Event Coordinator 1     │
      └─ Event Coordinator 2     │
```

### Business Unit Characteristics

```
┌─────────────────────────────────────────────────────────────┐
│                    BUSINESS UNIT PROPERTIES                  │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  Creation:                                                  │
│  ├─ Root Business Unit: Auto-created at environment setup   │
│  ├─ Child Business Units: Created manually                  │
│  └─ Maximum nesting: No hard limit but keep reasonable      │
│                                                              │
│  User Assignment:                                           │
│  ├─ Each user belongs to exactly ONE business unit          │
│  ├─ Cannot be in multiple business units                    │
│  ├─ Can be reassigned to different business unit            │
│  └─ Users inherit parent BU access through Manager role     │
│                                                              │
│  Record Ownership:                                          │
│  ├─ Owned by: Business Unit                                 │
│  ├─ Owning Business Unit field: Added to all tables         │
│  ├─ Cannot change record ownership to different BU          │
│  └─ Used for: Security role filtering                       │
│                                                              │
│  Hierarchy:                                                 │
│  ├─ Only ONE root business unit                             │
│  ├─ Child BUs can have children (parent hierarchy)          │
│  ├─ Cannot have multiple parents                            │
│  └─ Affects: Scope of security roles                        │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Creating Business Units

**Steps:**
1. Power Platform Admin Center
2. Environments → Settings
3. Business Management → Business Units
4. New → Create child business unit
5. Configure:
   - **Name**: US - East Region
   - **Parent**: Select parent BU (defaults to Root)
   - **Division**: Optional organizational code

### Business Unit and Security Role Access

```
Business Unit Hierarchy:
    Root (CEO)
    │
    ├─ Sales (VP Sales: Tom)
    │  ├─ Tom (works in Sales BU)
    │  ├─ Bob (works in Sales - North)
    │  └─ Jane (works in Sales - South)
    │
    └─ Marketing (VP Marketing: Amy)
       └─ Amy (works in Marketing BU)

Security Role: Salesperson (Business Unit Scope)
├─ Tom can see:
│  ├─ Records owned by Sales BU
│  ├─ Records owned by Sales - North
│  └─ Records owned by Sales - South
│     (All children of Sales)
│
├─ Bob can see:
│  ├─ Records owned by Sales - North
│  └─ NOT records in Sales - South
│
└─ Amy can see:
   ├─ Records owned by Marketing BU
   └─ NOT records in Sales
```

### Use Cases for Business Units

| Use Case | Structure |
|----------|-----------|
| **Geographic Organization** | Root → Americas → Canada, USA → California, Texas |
| **Department-Based** | Root → Sales, Marketing, Support, Operations |
| **Company Structure** | Root → Parent Company → Subsidiary A, Subsidiary B |
| **Functional Teams** | Root → Presales, Implementation, Support |

> **Exam Tip**: Business units are PERMANENT organizational structure, not temporary groupings. Use Teams for temporary or cross-functional grouping.

---

## Security Roles Deep Dive

### What are Security Roles?

Security roles define **WHAT users can do** on **WHICH tables**:

```
Security Role: Account Manager
├─ Accounts Table: Create, Read, Update, Delete
├─ Opportunities Table: Create, Read, Update (no Delete)
├─ Contacts Table: Read-only
├─ Tasks Table: Create, Read, Update, Delete
└─ Reports Table: Read-only
```

### Permission Types

```
┌────────────────────────────────────────────────────────────┐
│                    PERMISSION TYPES                         │
├────────────────────────────────────────────────────────────┤
│                                                             │
│  CREATE: Add new records to table                          │
│  ├─ Level: User, Business Unit, Parent BU, Organization   │
│  └─ Can user create ANY records or only in their BU?       │
│                                                             │
│  READ: View records in table                               │
│  ├─ Level: User, Business Unit, Parent BU, Organization   │
│  └─ Scope: Who's records can user see?                     │
│                                                             │
│  WRITE: Edit records in table                              │
│  ├─ Level: User, Business Unit, Parent BU, Organization   │
│  └─ Scope: Who's records can user edit?                    │
│                                                             │
│  DELETE: Remove records from table                         │
│  ├─ Level: User, Business Unit, Parent BU, Organization   │
│  └─ Scope: Who's records can user delete?                  │
│                                                             │
│  APPEND: Link related records (1:N relationships)          │
│  ├─ Can user attach records?                               │
│  └─ Example: Add Contact to Account                        │
│                                                             │
│  APPEND TO: Be linked from related records                 │
│  ├─ Can user be a target of relationship?                  │
│  └─ Example: Contact can be linked FROM Account            │
│                                                             │
│  ASSIGN: Change record owner                               │
│  ├─ Level: User, Business Unit, Parent BU, Organization   │
│  └─ Can user reassign records to others?                   │
│                                                             │
│  SHARE: Give others access to record                       │
│  ├─ Level: User, Business Unit, Parent BU, Organization   │
│  └─ Can user share records with others?                    │
│                                                             │
└────────────────────────────────────────────────────────────┘
```

### Creating Custom Security Roles

**Steps:**
1. Settings → Security → Security Roles
2. New → Create role
3. Configure permissions:
   - Select table
   - Set CRUD levels (None, User, BU, Parent BU, Org)
   - Configure special permissions
4. Assign to users/teams

### Permission Depth Levels

```
None            ⬜ Cannot access
User (Basic)    ⬜ Can only see own records
Business Unit   ⬜ Can see all records in their BU
Parent BU       ⬜ Can see BU + parent's BU records
Organization    ⬜ Can see ALL records (global)
```

---

## Access Levels and Inheritance

### Access Level Scope Visualization

```
                    ORGANIZATION
                    ┌─────────┐
                    │  All    │  ← User with Org access
                    │ Records │     sees everything
                    └────┬────┘
                         │
              PARENT:CHILD (Deep)
              ┌──────────┴──────────┐
              │  BU + Child Records │ ← User can see
              │  (Manager access)   │    their BU + all children
              └──────────┬──────────┘
                         │
                  BUSINESS UNIT
                  ┌──────┴──────┐
                  │ Own BU Only  │ ← User sees only
                  │   (Local)    │    their BU records
                  └──────┬──────┘
                         │
                      USER
                    ┌──┴──┐
                    │Me   │ ← User only sees
                    │Only │   their own records
                    └─────┘
```

### Inheritance Through Manager Role

```
Organization Structure:
    CEO (Root BU) - Has "Parent: Child" access
    │
    ├─ VP Sales (Sales BU) - Has "Parent: Child" access
    │  ├─ Sales Manager (Sales - North)
    │  │  ├─ Sales Rep 1 (Sales - North)
    │  │  └─ Sales Rep 2 (Sales - North)
    │  │
    │  └─ Sales Manager (Sales - South)
    │     ├─ Sales Rep 3 (Sales - South)
    │     └─ Sales Rep 4 (Sales - South)
    │
    └─ VP Marketing (Marketing BU)
       └─ Marketing Manager (Marketing)
          └─ Marketing Specialist (Marketing)

Who sees what (Opportunity records):
CEO sees: All opportunities across all BUs ✓
VP Sales sees: All opportunities in Sales BU + children ✓
Sales Manager - North sees: All opportunities in Sales - North ✓
Sales Rep 1 sees: Only own opportunities (User level)
```

### Effective Permissions

**Critical Concept**: Effective permissions = Most permissive access

```
User: Tom (Sales - North)
├─ Security Role 1: "Salesperson"
│  └─ Read: Business Unit level
│
├─ Security Role 2: "Manager"
│  └─ Read: Parent: Child level
│
└─ Effective Permission: Parent: Child level
   (Most permissive of the two roles)
```

---

## Row-Level Security (RLS)

### What is Row-Level Security?

Row-Level Security (RLS) restricts access to individual **records** (rows) based on:
- Business unit ownership
- User ownership
- Sharing
- Hierarchy

```
All Contacts Table (100 records)

Without RLS:
User sees: All 100 contacts (if read permission = Org level)

With RLS (based on Business Unit):
User in Sales - North sees:
  ├─ Records owned by Sales - North (45 records)
  ├─ Records owned by Sales BU (for managers)
  └─ Records shared with them (via sharing)

Result: User sees subset based on BU scope
```

### RLS Mechanisms

#### 1. **Ownership-Based RLS**

Records owned by:
- **User**: Owner must grant access
- **Business Unit**: Owner's BU members can see (based on role level)

#### 2. **Business Unit Hierarchy RLS**

Security role scope determines access:
```
If role scope = "Business Unit":
  └─ Can only see records owned by my BU

If role scope = "Parent: Child":
  └─ Can see records owned by my BU + all children

If role scope = "Organization":
  └─ Can see all records (no RLS filtering)
```

#### 3. **Sharing-Based RLS**

Users can share individual records:
- Grant READ access
- Grant WRITE access
- Grant DELETE access
- Grant ASSIGN access

```
Record: Account #123 (owned by Sales - North)

Without sharing:
├─ Users in Sales - North: See ✓
├─ Managers in Sales: See ✓
└─ Marketing users: Cannot see ✗

With sharing:
├─ Jane (Marketing): Shares with Account Manager Bob
├─ Bob now sees: Account #123 (shared)
└─ Even though Bob in Sales - South
```

### RLS in Power Pages

External portal users only see:
- Records granted via **web roles**
- Records owned by their **account** (if Access Level = Account)
- Records explicitly shared

---

## Column-Level Security

### What is Column-Level Security?

Column-Level Security restricts access to **specific fields** (columns):

```
Contact Record: John Smith

Without Column Security:
All fields visible:
├─ Name: John Smith ✓
├─ Email: john@company.com ✓
├─ Phone: 555-1234 ✓
├─ SSN: 123-45-6789 ✓
├─ Salary: $80,000 ✓
└─ Emergency Contact: Jane Smith ✓

With Column Security (HR staff only):
├─ Name: John Smith ✓
├─ Email: john@company.com ✓
├─ Phone: 555-1234 ✓
├─ SSN: ███-██-████ (Hidden)
├─ Salary: (Hidden)
└─ Emergency Contact: (Hidden)
```

### Setting Up Column-Level Security

**3-Step Process:**

1. **Enable on Column**
   - Table → Column properties
   - Enable "Enable for Field Security"

2. **Create Field Security Profile**
   - Settings → Security → Field Security Profiles
   - New → Configure permissions
   - Select columns to restrict

3. **Assign Users/Teams**
   - Add users/teams to profile
   - Select which columns they can read/update

### Column Security Permissions

```
For each secured column:
├─ Create: Can create records with this field?
├─ Read: Can view this field?
└─ Update: Can edit this field?

Examples:
HR Staff can Read/Update Salary field
├─ All other users: Cannot see ✗

Finance Team can Read Budget column
├─ Sales team: Cannot see ✗

Managers can Update Status field
├─ Regular users: Can Read, not Update
```

### Column Security + Row Security

Both can apply to same record:

```
Marketing user viewing Contact record:

Row Security says: "Can access this record" ✓
Column Security says: "Cannot see Salary field" ✗

Result: User sees record, but Salary field is hidden
```

---

## Teams and Access Teams

### Owner Teams vs Access Teams

```
OWNER TEAMS                          ACCESS TEAMS
┌────────────────────────────────┐  ┌────────────────────────────────┐
│ Can own records                │  │ Cannot own records              │
│ Has security roles             │  │ Has no security roles           │
│ Members inherit roles          │  │ Members don't inherit from team │
│ Permanent membership           │  │ Dynamic membership per record   │
│ Has business unit              │  │ No business unit               │
│ Used for: Team-level policies  │  │ Used for: Record-level collab   │
└────────────────────────────────┘  └────────────────────────────────┘
```

### Owner Team Example

```
Team: Sales North
├─ Type: Owner Team
├─ Business Unit: Sales - North
├─ Security Roles: Salesperson role
├─ Members:
│  ├─ Tom (inherits Salesperson role)
│  ├─ Bob (inherits Salesperson role)
│  └─ Jane (inherits Salesperson role)
│
└─ Can own records in Dataverse
   └─ Assigned to: Team "Sales North"
      Accessible by all team members
```

### Access Team Example

```
Record: Project "New Website"
├─ Owner: Marketing BU (Organization-owned table)
├─ Access Team: Website Project Team
│  ├─ Members can be from any BU
│  ├─ John (Sales)
│  ├─ Sarah (Marketing)
│  └─ Mike (Engineering)
│
└─ Members see record via ACCESS TEAM
   Not through security roles
   Dynamic access to this specific record
```

### When to Use Each

| Scenario | Use |
|----------|-----|
| Team should own records together | Owner Team |
| Team-wide security permissions | Owner Team |
| Project team with cross-functional members | Access Team |
| Ad-hoc collaboration on one record | Access Team |
| Department teams | Owner Team |
| Temporary project teams | Access Team |

---

## Sharing and Record Access

### How Sharing Works

```
Record Owner: Tom (Sales - North BU)
├─ Owns record
├─ Has full permissions
└─ Can share with others

Tom shares with:
├─ Jane (Sales - South)
│  └─ Jane gets: READ access (can view)
│
├─ Bob (Marketing)
│  └─ Bob gets: READ + WRITE access
│
└─ Sarah (Customer Service)
   └─ Sarah gets: DELETE access
```

### Sharing Access Types

| Access | Permissions |
|--------|-------------|
| **Read** | View record only, no editing |
| **Write** | View AND edit record |
| **Append** | Can attach related records |
| **Append To** | Can be target of relationship |
| **Assign** | Can reassign record to others |
| **Delete** | Can delete record |

### Sharing Limitations

```
❌ Cannot share:
   ├─ Records you don't own
   ├─ Organization-owned records (no owner)
   ├─ If you lack SHARE permission
   └─ With users outside your organization

✓ Can share:
   ├─ Your own records
   ├─ Records you have SHARE permission
   ├─ Team-owned records (by team members)
   └─ With users in your tenant
```

### Sharing vs Security Roles

```
SECURITY ROLES                    SHARING
├─ Global permissions             └─ Individual record access
├─ Set by admins                  └─ Can be set by users
├─ All records of that table      └─ One record at a time
├─ Permanent                      └─ Can be revoked
├─ Based on job function          └─ Based on projects/needs
└─ Difficult to manage at scale   └─ Easy but doesn't scale
```

---

## Hierarchy Security

### Manager Hierarchy for Escalation

Automatically grants access based on reporting structure:

```
CEO (Sarah)
│
├─ VP Sales (Tom)
│  ├─ Sales Manager (Bob) - reports to Tom
│  │  ├─ Sales Rep 1 (John) - reports to Bob
│  │  └─ Sales Rep 2 (Jane) - reports to Bob
│  │
│  └─ Sales Manager (Mike) - reports to Tom
│     ├─ Sales Rep 3 (Alice) - reports to Mike
│     └─ Sales Rep 4 (Charlie) - reports to Mike
│
└─ VP Marketing (Amy)
   └─ Marketing Manager (Dave) - reports to Amy
      └─ Marketing Specialist (Eve) - reports to Dave

Access for Sarah (CEO):
├─ Can see Tom's records
├─ Can see Bob's and Mike's records (Tom's reports)
├─ Can see John, Jane, Alice, Charlie records (team members down)
├─ Can see Amy's records
└─ Can see Dave's and Eve's records (team members down)

Access for Bob (Manager):
├─ Can see own records
├─ Can see John's and Jane's records (direct reports)
├─ Can see Tom's records (manager)
└─ CANNOT see other teams
```

### Configuring Hierarchy Security

1. Create **Manager** field on User table
2. Configure hierarchy:
   - Settings → Security → Hierarchy Security
   - New rule based on Manager field
3. Users gain access through manager relationship

---

## Power Pages Security Model

### Web Roles (External Users)

```
Power Pages Portal (External Users)
├─ Public User (Everyone)
│  └─ Can see public pages only
│
├─ Web Role: Customer
│  └─ Can see:
│     ├─ Customer pages
│     ├─ Their own account records
│     └─ Their support cases
│
└─ Web Role: Partner
   └─ Can see:
      ├─ Partner pages
      ├─ Their company records
      └─ Products/resources they can resell
```

### Table Permissions

Table permissions control portal access to Dataverse tables:

```
Table: Support Case
├─ Global Access: All portal users see all cases
├─ Account Access: See cases for their account only
├─ Contact Access: See cases for their contact record
└─ Self Access: Only see own record

Table: Product
├─ Global Access: All portal users see all products
└─ No individual record restriction
```

### Column Permissions

Hide sensitive columns in portal:

```
Contact Table in Portal:
├─ Visible columns:
│  ├─ Name
│  ├─ Email
│  └─ Phone
│
├─ Hidden columns:
│  ├─ Salary (hidden from portal)
│  ├─ SSN (hidden from portal)
│  └─ Internal Notes (hidden from portal)
```

### Power Pages vs Internal Users

| Aspect | Internal User | Portal User |
|--------|---------------|-|
| **Access Control** | Security Roles | Web Roles |
| **Scope** | Business Unit | Global |
| **RLS** | Business Unit scope | Table Permissions |
| **Columns** | Column Security | Column Permissions |
| **Admin Setup** | Central control | Delegated to portal admin |

---

## Security Configuration Scenarios

### Scenario 1: Multi-Region Sales Organization

```
Requirement:
- Sales managers see their region's data
- CEO sees all regions
- Cross-region visibility: NO

Solution:
├─ Business Units:
│  ├─ Sales - North (Manager: Tom)
│  └─ Sales - South (Manager: Mike)
│
├─ Security Roles:
│  ├─ "Sales Rep": Read/Write = Business Unit level
│  ├─ "Sales Manager": Read/Write = Parent: Child level
│  └─ "VP Sales": Read/Write = Organization level
│
└─ Results:
   ├─ Tom (North) sees North data
   ├─ Mike (South) sees South data
   └─ VP Sales sees all data
```

### Scenario 2: HR-Only Sensitive Fields

```
Requirement:
- HR staff can see Salary, SSN, Emergencies fields
- Everyone sees basic contact info
- Other fields should be hidden from non-HR

Solution:
├─ Enable Column Security on:
│  ├─ Salary field
│  ├─ SSN field
│  └─ Emergency Contact field
│
├─ Create Field Security Profile:
│  ├─ Name: "HR Only"
│  ├─ Secured columns: Salary, SSN, Emergency Contact
│  └─ Permissions: HR group can Read/Update
│
└─ Result:
   ├─ HR staff: See all fields ✓
   ├─ Non-HR staff: See basic info, sensitive fields hidden ✗
```

### Scenario 3: Project Collaboration (Cross-Functional)

```
Requirement:
- Project team from Sales, Marketing, Engineering
- Each user sees project records
- Temporary assignment (project ends in 3 months)

Solution:
├─ Create Access Team:
│  ├─ Team: "Website Redesign Project"
│  ├─ Members: John (Sales), Sarah (Marketing), Mike (Engineering)
│  └─ Type: Access Team
│
├─ Add team to records:
│  ├─ Project record: "Website Redesign"
│  ├─ Associated documents
│  └─ Task records
│
└─ Result:
   ├─ Team members see shared records
   ├─ Access controlled at record level, not BU
   └─ Easy to remove when project ends
```

---

## Common Exam Scenarios

### Scenario 1: User Can't See Records

**Question**: "User in Sales - North can't see any records even though they have the Salesperson role. What could be wrong?"

**Answers to check:**
1. **User not assigned to Business Unit** - Must be in Sales - North BU
2. **Role not assigned** - Verify Salesperson role assigned
3. **Access level too restrictive** - Check if role set to "User" level instead of BU
4. **Shared records only** - If role = User level, can only see own records
5. **Table permission = None** - Role might not have Read permission
6. **Deactivated user** - Check user account status

**Resolution**: Verify BU assignment, role assignment, role access levels.

### Scenario 2: Row-Level Security Between Business Units

**Question**: "How can VP Sales see all sales data across all regions (North, South, East) while Sales Reps only see their own region?"

**Answer**:
- VP Sales: "Parent: Child" access level (sees all child BUs)
- Sales Reps: "Business Unit" access level (sees only their BU)
- Business Unit hierarchy: Root > Sales > Sales - North/South/East

### Scenario 3: Column Security Needed

**Question**: "Only Finance team should see the Contract Cost field on Account records. Other users should see Account but not Cost. Solution?"

**Answer**:
1. Enable Column Security on "Contract Cost" field
2. Create Field Security Profile: "Finance Only"
3. Add Finance group members to profile
4. Set permissions: Read + Update for Finance group
5. All others: No access to field

### Scenario 4: Sharing a Record

**Question**: "Sales Rep Tom (Sales - North) needs Jane (Sales - South) to see his Account record temporarily. Jane doesn't have organizational-level access. How?"

**Answer**: **Sharing**
1. Tom opens his Account record
2. Click "Share"
3. Search for Jane
4. Grant: Read access (or Read + Write)
5. Jane can now see this specific record
6. Revoke sharing when no longer needed

### Scenario 5: Security Role Scope Decision

**Question**: "Organization-owned Contact table. Managers should see all contacts, sales reps should see only assigned to them. Which access levels?"

**Answer**:
- Table is organization-owned: RLS based on ASSIGNED TO field, not ownership
- Managers: Set permission to "Organization" level (see all)
- Sales Reps: Set permission to "User" level (see only assigned to them)

---

## Key Takeaways for the Exam

1. **Business units** organize users hierarchically
2. **Security roles** define permissions (CRUD operations)
3. **Access levels** filter which records users can see
4. **Row-level security** uses ownership + BU scope + sharing
5. **Column-level security** hides specific fields from users
6. **Owner Teams** own records, have security roles
7. **Access Teams** provide ad-hoc record-level access
8. **Sharing** grants individual record access without permanent role changes
9. **Manager hierarchy** grants access based on reporting structure
10. **Power Pages** uses web roles and table permissions (not security roles)
11. **Effective permissions** = most permissive when user has multiple roles
12. **Never assign organization-level access carelessly** - use specific access levels

---

*Created for PL-200 Exam Preparation*
