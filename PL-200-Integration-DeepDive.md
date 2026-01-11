# Integration Scenarios - Complete Deep Dive
## PL-200 Exam Preparation

---

## Table of Contents
1. [Integration Overview](#integration-overview)
2. [Connectors Deep Dive](#connectors-deep-dive)
3. [Virtual Tables (External Data)](#virtual-tables-external-data)
4. [On-Premises Data Gateway](#on-premises-data-gateway)
5. [Dataverse Web API](#dataverse-web-api)
6. [Power Automate Integration Patterns](#power-automate-integration-patterns)
7. [SharePoint Integration](#sharepoint-integration)
8. [Microsoft Teams Integration](#microsoft-teams-integration)
9. [Excel Integration](#excel-integration)
10. [SQL Server Integration](#sql-server-integration)
11. [Custom Connectors](#custom-connectors)
12. [Webhooks and Events](#webhooks-and-events)
13. [AI Builder Integration](#ai-builder-integration)
14. [Power BI Integration](#power-bi-integration)
15. [Common Integration Scenarios](#common-integration-scenarios)

---

## Integration Overview

### The Power Platform Integration Landscape

Power Platform excels at connecting disparate systems:

```
┌─────────────────────────────────────────────────────────────────┐
│              POWER PLATFORM INTEGRATION ARCHITECTURE             │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │                    Power Platform                         │  │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐         │  │
│  │  │ Canvas Apps│  │Model-Driven│  │Power       │         │  │
│  │  │            │  │   Apps     │  │Automate    │         │  │
│  │  └──────┬─────┘  └──────┬─────┘  └──────┬─────┘         │  │
│  │         │                │                │               │  │
│  │         └────────────────┼────────────────┘               │  │
│  │                          │                                │  │
│  │  ┌───────────────────────┴──────────────────────────┐    │  │
│  │  │              Connector Platform                   │    │  │
│  │  │            (1000+ Connectors)                     │    │  │
│  │  └───────────────────────┬──────────────────────────┘    │  │
│  └────────────────────────────┬──────────────────────────────┘  │
│                               │                                  │
│  ┌────────────────────────────┴──────────────────────────────┐  │
│  │                   Integration Methods                      │  │
│  │                                                            │  │
│  │  Cloud Services        On-Premises         Custom         │  │
│  │  ┌──────────────┐     ┌──────────────┐   ┌────────────┐  │  │
│  │  │• Dataverse   │     │• SQL Server  │   │• REST APIs │  │  │
│  │  │• SharePoint  │     │  (Gateway)   │   │• SOAP APIs │  │  │
│  │  │• Dynamics365 │     │• File System │   │• Webhooks  │  │  │
│  │  │• Office 365  │     │  (Gateway)   │   │• Custom    │  │  │
│  │  │• Salesforce  │     │• SAP         │   │  Connectors│  │  │
│  │  └──────────────┘     └──────────────┘   └────────────┘  │  │
│  │                                                            │  │
│  │  Virtual Tables        Direct Connect    Event-Based      │  │
│  │  ┌──────────────┐     ┌──────────────┐   ┌────────────┐  │  │
│  │  │• SQL Server  │     │• Web API     │   │• Webhooks  │  │  │
│  │  │• SharePoint  │     │• OData       │   │• Dataverse │  │  │
│  │  │• Azure Cosmos│     │• HTTP        │   │  Events    │  │  │
│  │  └──────────────┘     └──────────────┘   └────────────┘  │  │
│  └────────────────────────────────────────────────────────────┘  │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Integration Approaches

| Approach | Use Case | Pros | Cons |
|----------|----------|------|------|
| **Connectors** | Pre-built integrations | Easy, no code | Limited customization |
| **Virtual Tables** | Display external data | Real-time, no copy | Read-mostly, complexity |
| **Data Gateway** | On-premises systems | Secure tunnel | Requires setup |
| **Web API** | Custom integration | Full control | Requires coding |
| **Power Automate** | Workflow automation | Low-code, visual | Performance limits |
| **Custom Connectors** | Unique APIs | Reusable | Development effort |

---

## Connectors Deep Dive

### What are Connectors?

**Connectors** are pre-built integrations to external services:

```
┌─────────────────────────────────────────────────────────────┐
│                    CONNECTOR ARCHITECTURE                    │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  Power Platform App/Flow                                    │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  "Send email via Outlook"                            │  │
│  └────────────────────┬─────────────────────────────────┘  │
│                       │                                     │
│  ┌────────────────────┴─────────────────────────────────┐  │
│  │         Office 365 Outlook Connector                  │  │
│  │  ┌────────────────────────────────────────────────┐  │  │
│  │  │  • Authentication (OAuth)                      │  │  │
│  │  │  • API abstraction                             │  │  │
│  │  │  • Actions: Send email, Get emails             │  │  │
│  │  │  • Triggers: New email arrives                 │  │  │
│  │  └────────────────┬───────────────────────────────┘  │  │
│  └────────────────────┴─────────────────────────────────┘  │
│                       │                                     │
│  ┌────────────────────┴─────────────────────────────────┐  │
│  │           Microsoft Outlook Service                   │  │
│  │           (Office 365)                                │  │
│  └──────────────────────────────────────────────────────┘  │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Connector Categories

#### Standard Connectors

**Included with base license:**
- Office 365 (Outlook, Users, Excel)
- SharePoint
- OneDrive for Business
- Microsoft Teams
- OneNote
- Planner
- Approvals
- Notifications

#### Premium Connectors

**Require Premium license:**
- **Dataverse**
- SQL Server
- HTTP (custom APIs)
- On-premises data gateway
- Oracle Database
- SAP
- Salesforce
- ServiceNow
- Adobe Creative Cloud
- Azure services (beyond standard)

#### Custom Connectors

**User-created:**
- REST API wrapper
- OAuth/API Key authentication
- Reusable across organization
- Premium license required

### Connector Components

**Actions**: Operations you can perform
```
Example - SharePoint Connector:
  • Create item
  • Get item
  • Update item
  • Delete item
  • Get files
  • Send HTTP request
```

**Triggers**: Events that start flows
```
Example - SharePoint Connector:
  • When an item is created
  • When an item is created or modified
  • When a file is created (properties only)
  • For a selected file
```

### Connection References

In solutions, connectors become **connection references**:

```
Development Environment:
  Connection: SQL Server (dev-server)
     ↓ Export Solution
Test Environment:
  Connection Reference: SQL Server
     → Map to: SQL Server (test-server)
     ↓ Export Solution
Production Environment:
  Connection Reference: SQL Server
     → Map to: SQL Server (prod-server)
```

**Benefits:**
- No hardcoded connections
- Easy environment promotion
- Reconfigure without editing apps/flows

> **Exam Tip**: Connection references enable ALM. When moving solutions between environments, you map connections to target systems. This is commonly tested.

---

## Virtual Tables (External Data)

### What are Virtual Tables?

Virtual tables display **external data** in Dataverse without copying it:

```
┌─────────────────────────────────────────────────────────────┐
│              VIRTUAL TABLE ARCHITECTURE                      │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  Model-Driven App / Canvas App                              │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  Form displays "Products" table                      │  │
│  └────────────────────┬─────────────────────────────────┘  │
│                       │                                     │
│  ┌────────────────────┴─────────────────────────────────┐  │
│  │       Dataverse (Virtual Table)                       │  │
│  │                                                        │  │
│  │  Table: Products (Virtual)                            │  │
│  │    • No data stored in Dataverse                      │  │
│  │    • Schema mapped to external source                 │  │
│  │    • Real-time query on access                        │  │
│  └────────────────────┬─────────────────────────────────┘  │
│                       │                                     │
│  ┌────────────────────┴─────────────────────────────────┐  │
│  │         Data Provider (Plugin)                        │  │
│  │    • SQL Server Provider                              │  │
│  │    • SharePoint Provider                              │  │
│  │    • Azure Cosmos DB Provider                         │  │
│  │    • OData Provider                                   │  │
│  │    • Custom Provider                                  │  │
│  └────────────────────┬─────────────────────────────────┘  │
│                       │                                     │
│  ┌────────────────────┴─────────────────────────────────┐  │
│  │         External Data Source                          │  │
│  │    (SQL Server, SharePoint, etc.)                     │  │
│  │                                                        │  │
│  │    Products Table (actual data)                       │  │
│  └──────────────────────────────────────────────────────┘  │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Virtual Table Benefits

| Benefit | Description |
|---------|-------------|
| **No data duplication** | Data stays in source system |
| **Real-time access** | Always current |
| **No sync needed** | No sync jobs or delays |
| **Reduced storage** | Doesn't count against Dataverse capacity |
| **Single interface** | Access like native Dataverse table |

### Virtual Table Limitations

| Limitation | Impact |
|------------|--------|
| **Read-mostly** | Limited write operations (depends on provider) |
| **Performance** | Slower than native tables |
| **No relationships** | Limited relationship support |
| **No rollup/calculated** | Can't use these column types |
| **No audit** | Can't audit virtual table data |
| **No offline** | Requires connectivity |

### Supported Data Sources

| Data Source | Provider | Capabilities |
|-------------|----------|--------------|
| **SQL Server** | SQL Data Provider | Read/Write with gateway |
| **SharePoint** | SharePoint Provider | Read/limited write |
| **Azure Cosmos DB** | Cosmos DB Provider | Read/Write |
| **OData v4** | OData Provider | Read/Write (varies) |
| **Custom** | Custom Data Provider | Developer-defined |

### Creating Virtual Tables

**SQL Server Example:**

1. **Settings** → **Virtual tables data sources**
2. **New data source** → Select provider (e.g., SQL)
3. Configure connection:
   - **Server**: SQL server address
   - **Database**: Database name
   - **Authentication**: Credentials or gateway
4. **Create virtual tables**:
   - Select tables/views from SQL
   - Map to Dataverse schema
   - Configure columns
5. **Save and publish**

**Result:**
- Tables appear in Dataverse
- Can be used in apps and forms
- Data queried from SQL in real-time

### Use Cases

**When to Use Virtual Tables:**
- Data changes frequently in source
- Don't want to duplicate data
- Source system is authoritative
- Display-only scenarios

**When NOT to Use Virtual Tables:**
- Need offline access
- Performance is critical
- Need complex relationships
- Need rollup/calculated columns
- Heavy write operations

> **Exam Tip**: Virtual tables DON'T copy data. They query in real-time. Use when you want live data without duplication. This is a key distinguisher from regular tables.

---

## On-Premises Data Gateway

### What is the Gateway?

**On-premises data gateway** creates secure tunnel between cloud and on-premises:

```
┌─────────────────────────────────────────────────────────────┐
│           ON-PREMISES DATA GATEWAY ARCHITECTURE              │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  Cloud (Azure)                                              │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  Power Platform                                      │  │
│  │    ├─ Canvas App                                     │  │
│  │    ├─ Model-Driven App                               │  │
│  │    ├─ Power Automate                                 │  │
│  │    └─ Power BI                                       │  │
│  └────────────────────┬─────────────────────────────────┘  │
│                       │ (Outbound HTTPS)                    │
│                       │                                     │
│  ┌────────────────────┴─────────────────────────────────┐  │
│  │         Azure Gateway Service                         │  │
│  │         (Relay)                                       │  │
│  └────────────────────┬─────────────────────────────────┘  │
│                       │ (Encrypted tunnel)                  │
│                       ↓                                     │
│ ════════════════════ Firewall ═══════════════════════════  │
│                       ↓                                     │
│  On-Premises                                                │
│  ┌────────────────────┴─────────────────────────────────┐  │
│  │    On-Premises Data Gateway (Windows Service)        │  │
│  │      • Polls Azure for requests                       │  │
│  │      • Executes queries locally                       │  │
│  │      • Returns results to Azure                       │  │
│  └────────────────────┬─────────────────────────────────┘  │
│                       │                                     │
│  ┌────────────────────┴─────────────────────────────────┐  │
│  │       On-Premises Data Sources                        │  │
│  │         ├─ SQL Server                                 │  │
│  │         ├─ File System                                │  │
│  │         ├─ Oracle                                     │  │
│  │         ├─ SAP                                        │  │
│  │         └─ Other systems                              │  │
│  └──────────────────────────────────────────────────────┘  │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Gateway Features

| Feature | Description |
|---------|-------------|
| **Secure** | Encrypted tunnel, no inbound firewall rules |
| **Multiple sources** | One gateway, many data sources |
| **Clustering** | High availability with multiple machines |
| **Centralized** | One gateway for organization |
| **Monitoring** | Track performance and errors |

### Gateway Modes

**Personal Mode:**
- Single user
- For Power BI only
- Not shared

**Standard Mode:**
- Multiple users
- All Power Platform services
- Shared across organization
- Recommended for production

### Installing Gateway

**Requirements:**
- Windows Server 2016 or later (or Windows 10/11)
- .NET Framework 4.7.2 or later
- 64-bit operating system
- 8 GB RAM (minimum)
- Access to on-premises data sources

**Installation Steps:**
1. Download gateway installer
2. Run installer on on-premises machine
3. Sign in with account
4. Register gateway with name
5. Configure (recovery key, region)
6. Start gateway service

### Configuring Data Sources

After gateway installation:

1. **Power Platform Admin Center**
2. **Data** → **On-premises data gateways**
3. Select gateway
4. **Add data source**:
   - **Data source name**
   - **Type** (SQL Server, File System, etc.)
   - **Server/path**
   - **Database** (if applicable)
   - **Authentication method**
   - **Credentials**
5. **Add users** who can use this data source

### Using Gateway in Apps/Flows

**Canvas Apps:**
```
Add data source → SQL Server
  Connection: Use gateway
  Gateway: [Your Gateway]
  Server: [Server name]
  Database: [Database name]
  Authentication: Windows / SQL
```

**Power Automate:**
```
Action: Get rows (SQL Server)
  Connection: Create new
  Gateway: [Your Gateway]
  Server: [Server name]
  Database: [Database name]
```

### Gateway Management

**Monitoring:**
- **Gateway status** (online/offline)
- **Query performance**
- **Error logs**
- **Data source usage**

**High Availability:**
- Install gateway on multiple machines
- Forms cluster automatically
- Load balanced
- Failover if one fails

> **Exam Tip**: Gateway is required for accessing on-premises data sources from cloud. Polls from inside → outside (no inbound firewall rules). This architecture is commonly tested.

---

## Dataverse Web API

### What is Dataverse Web API?

**RESTful API** for programmatic access to Dataverse:

```
Endpoint: https://[org].api.crm.dynamics.com/api/data/v9.2/

Example Requests:

GET /accounts
  → List accounts

GET /accounts(guid)
  → Get specific account

POST /accounts
  Body: {"name": "Contoso", "city": "Seattle"}
  → Create account

PATCH /accounts(guid)
  Body: {"city": "Portland"}
  → Update account

DELETE /accounts(guid)
  → Delete account
```

### Authentication

**Azure AD OAuth 2.0:**

```
1. Register app in Azure AD
2. Get client ID and secret
3. Request token
4. Use token in API calls

Authorization: Bearer {token}
```

### Common Operations

#### Query Data

**Basic query:**
```
GET /accounts?$select=name,revenue&$filter=revenue gt 100000
```

**Query options (OData):**

| Option | Purpose | Example |
|--------|---------|---------|
| `$select` | Choose columns | `$select=name,revenue` |
| `$filter` | Filter rows | `$filter=revenue gt 100000` |
| `$orderby` | Sort | `$orderby=name asc` |
| `$top` | Limit rows | `$top=10` |
| `$expand` | Include related | `$expand=primarycontactid($select=fullname)` |

#### Create Record

```
POST /accounts
Content-Type: application/json

{
  "name": "Contoso Manufacturing",
  "revenue": 500000,
  "address1_city": "Seattle"
}

Response: 201 Created
Location: /accounts(guid)
```

#### Update Record

```
PATCH /accounts(guid)
Content-Type: application/json

{
  "revenue": 600000
}

Response: 204 No Content
```

#### Delete Record

```
DELETE /accounts(guid)

Response: 204 No Content
```

### Using Web API in Power Automate

**HTTP with Azure AD action:**

```
Action: HTTP with Azure AD
  Method: GET
  URI: https://[org].api.crm.dynamics.com/api/data/v9.2/accounts
  Headers:
    Accept: application/json
    Content-Type: application/json
  Authentication: Active Directory OAuth
  Tenant: [Tenant ID]
  Audience: https://[org].api.crm.dynamics.com
  Client ID: [App ID]
  Credential Type: Secret
  Secret: [Client Secret]
```

**Parse JSON** to use results:

```
Parse JSON
  Content: outputs('HTTP_with_Azure_AD')?['body']
  Schema: {schema from sample}

Access values:
  body('Parse_JSON')?['value'][0]?['name']
```

> **Exam Tip**: Web API is for programmatic access. Use connectors when available (easier). Use Web API for custom code or advanced scenarios. HTTP action requires Premium license.

---

## Power Automate Integration Patterns

### Common Integration Patterns

#### Pattern 1: Sync Data Between Systems

```
┌─────────────────────────────────────────────────────────────┐
│                    DATA SYNC PATTERN                         │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  Trigger: When a row is added or modified (Dataverse)      │
│     ↓                                                        │
│  Action: Get row details                                    │
│     ↓                                                        │
│  Condition: Check if record exists in external system       │
│     ├─ Yes: Update in external system                       │
│     └─ No: Create in external system                        │
│     ↓                                                        │
│  Action: Log sync status                                    │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

#### Pattern 2: Aggregate Data from Multiple Sources

```
Trigger: Recurrence (daily)
  ↓
Get data from Dataverse
  ↓
Get data from SharePoint
  ↓
Get data from SQL Server
  ↓
Combine data (Union arrays)
  ↓
Create Excel report
  ↓
Send email with report
```

#### Pattern 3: Event-Driven Updates

```
Trigger: When an item is created (SharePoint)
  ↓
Transform data (compose/parse)
  ↓
Create row in Dataverse
  ↓
Send notification (Teams/Email)
```

#### Pattern 4: Approval Workflow

```
Trigger: When a row is added (Dataverse)
  ↓
Condition: If amount > threshold
  ↓
Start and wait for an approval
  ↓
Condition: If approved
  ├─ Yes: Update status to Approved
  └─ No: Update status to Rejected
  ↓
Send notification
```

### Error Handling in Integrations

**Scope + Configure Run After:**

```
Scope: Try
  ├─ Get data from System A
  ├─ Transform data
  └─ Update System B

Scope: Catch (Configure run after: has failed, has timed out)
  ├─ Log error to Dataverse
  ├─ Send error notification
  └─ Set retry flag

Scope: Finally (Configure run after: all)
  └─ Update audit log
```

### Throttling and Limits

**Be aware of:**
- API call limits (per user/per flow)
- Action timeout (120 seconds default)
- Concurrent flow runs (50 per flow)
- Apply to each iterations (100,000)

**Best practices:**
- Use concurrency in loops
- Batch operations when possible
- Filter data server-side
- Use change tracking (deltas)

---

## SharePoint Integration

### Connecting to SharePoint

**In Canvas Apps:**
```
Data → Add data → SharePoint
  Choose site
  Select lists/libraries
```

**In Power Automate:**
```
Trigger: When an item is created or modified
  Site Address: [SharePoint site URL]
  List Name: [List name]
```

### Common SharePoint Operations

| Operation | Use Case |
|-----------|----------|
| **Get items** | Retrieve list items |
| **Create item** | Add to list |
| **Update item** | Modify existing |
| **Delete item** | Remove item |
| **Get files** | Retrieve documents |
| **Create file** | Upload document |
| **Get file content** | Download file |
| **Copy file** | Duplicate document |
| **Move file** | Relocate document |

### SharePoint and Dataverse

**Document Management:**

```
Dataverse Table: Accounts
  ↓ Enable Document Management
SharePoint Site: Automatically created
  Folder per Account record
```

**Benefits:**
- Documents stored in SharePoint
- Accessible from Dataverse forms
- Version control
- Co-authoring
- Large file support

**Configuration:**
1. **Settings** → **Document Management**
2. **Enable document management**
3. Select tables
4. SharePoint site/location
5. Folder structure

> **Exam Tip**: Dataverse + SharePoint is the RECOMMENDED approach for document storage. Dataverse stores record data, SharePoint stores documents. This integration is commonly tested.

---

## Microsoft Teams Integration

### Teams Integration Options

#### Power Apps in Teams

**Embed Canvas App:**
1. Teams channel → **+** (Add tab)
2. **Power Apps**
3. Select app
4. Configure tab name

**Create App in Teams:**
- Use Teams environment
- Dataverse for Teams (included)
- Build app directly in Teams

#### Power Automate in Teams

**Flows for Teams:**
- Teams approval flows
- Post messages to channels
- Create teams/channels
- Add members

**Adaptive Cards:**
```
Post adaptive card in channel:
  {
    "type": "AdaptiveCard",
    "body": [
      {"type": "TextBlock", "text": "Order Approved!"}
    ],
    "actions": [
      {"type": "Action.OpenUrl", "title": "View", "url": "..."}
    ]
  }
```

#### Power Virtual Agents in Teams

**Add bot to Teams:**
1. Publish bot
2. **Channels** → **Microsoft Teams**
3. **Open bot** in Teams
4. Share with users

**Teams-specific features:**
- SSO (automatic authentication)
- Adaptive cards in conversations
- Access Teams data (members, channels)

### Teams Environment

**Dataverse for Teams:**
- Limited Dataverse instance
- Included with Teams license
- Per-team storage
- Simplified maker experience

**Limitations:**
- Cannot access from outside Teams
- Lower capacity (2 GB per team)
- Fewer premium features
- Simplified security model

---

## Excel Integration

### Excel as Data Source

**Excel connector in Canvas Apps:**

```
Requirements:
- Excel file in OneDrive or SharePoint
- Formatted as Table (Insert → Table)
- Table has headers
```

**Delegation:**
- Excel does NOT support delegation
- Limited to 2,000 rows maximum
- Use Dataverse for larger datasets

### Excel Export

**Power Automate pattern:**

```
Trigger: Scheduled or manual
  ↓
List rows (Dataverse)
  ↓
Create CSV table
  ↓
Create file (OneDrive/SharePoint)
  Content: CSV output
  File: Report.csv
  ↓
Send email with attachment
```

### Excel Templates

**Generate Excel from template:**

```
Flow:
1. Get template file from SharePoint
2. Populate with data (using Excel actions)
3. Save as new file
4. Share or email

Excel Actions:
- Get tables
- Add row to table
- Update row
- Run script (Office Scripts)
```

---

## SQL Server Integration

### SQL Connector

**Connection types:**

| Type | Use Case |
|------|----------|
| **SQL Server (cloud)** | Azure SQL Database |
| **SQL Server (on-premises)** | Local SQL Server via gateway |

### SQL Operations

**In Power Automate:**

| Action | Purpose |
|--------|---------|
| **Get rows** | Query table |
| **Get row** | Get single row by ID |
| **Insert row** | Add record |
| **Update row** | Modify record |
| **Delete row** | Remove record |
| **Execute stored procedure** | Run SP |
| **Execute a SQL query** | Custom SQL |

**Best practices:**
- Use **Get rows** with filter (not custom query)
- Select specific columns for performance
- Use stored procedures for complex logic
- Parameterize queries (prevent SQL injection)

### SQL to Dataverse Sync

**Common pattern:**

```
Trigger: Recurrence (hourly)
  ↓
Get rows from SQL Server
  Filter: ModifiedDate > {last sync time}
  ↓
Apply to each: SQL rows
  ↓
  Check if exists in Dataverse
    ├─ Yes: Update row
    └─ No: Add row
  ↓
Update last sync time
```

---

## Custom Connectors

### What are Custom Connectors?

Custom connectors wrap **REST APIs** for reuse:

```
┌─────────────────────────────────────────────────────────────┐
│              CUSTOM CONNECTOR ARCHITECTURE                   │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  Power App / Flow                                           │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  Action: "Get Weather"                               │  │
│  └────────────────────┬─────────────────────────────────┘  │
│                       │                                     │
│  ┌────────────────────┴─────────────────────────────────┐  │
│  │       Custom Connector: Weather API                   │  │
│  │  ┌────────────────────────────────────────────────┐  │  │
│  │  │  Actions:                                      │  │  │
│  │  │    • Get current weather                       │  │  │
│  │  │    • Get forecast                              │  │  │
│  │  │  Triggers: (if supported)                      │  │  │
│  │  │    • Weather alert                             │  │  │
│  │  │  Authentication:                               │  │  │
│  │  │    • API Key                                   │  │  │
│  │  └────────────────────────────────────────────────┘  │  │
│  └────────────────────┬─────────────────────────────────┘  │
│                       │                                     │
│  ┌────────────────────┴─────────────────────────────────┐  │
│  │         External REST API                             │  │
│  │         (weather.api.com)                             │  │
│  └──────────────────────────────────────────────────────┘  │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Creating Custom Connectors

**Methods:**

1. **From blank** - Define manually
2. **From OpenAPI** - Import spec
3. **From Postman collection** - Import collection

**Steps:**

1. **General information**:
   - Name, description, host URL
   - Icon, color

2. **Security**:
   - Authentication type (API Key, OAuth, etc.)
   - Configuration

3. **Definition**:
   - Actions (requests)
   - Triggers (webhooks)
   - Request/response schema

4. **Test**:
   - Create connection
   - Test operations

5. **Share**:
   - Share with organization
   - Use in apps/flows

### Authentication Types

| Type | Use Case |
|------|----------|
| **No authentication** | Public APIs |
| **API Key** | Simple token-based |
| **Basic** | Username/password |
| **OAuth 2.0** | Secure delegated access |
| **Windows** | Windows auth |

### Custom Connector Use Cases

- Internal APIs without existing connector
- Third-party services
- Legacy systems with REST endpoints
- Standardize API access across organization

> **Exam Tip**: Custom connectors are premium. They wrap REST APIs for easy reuse. If scenario mentions "company's own API" or "REST API without connector", answer is custom connector.

---

## Webhooks and Events

### Dataverse Webhooks

**Webhooks** call external HTTP endpoints when events occur:

```
┌─────────────────────────────────────────────────────────────┐
│                    WEBHOOK PATTERN                           │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  Dataverse                                                  │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  Event: Account record created                       │  │
│  └────────────────────┬─────────────────────────────────┘  │
│                       │                                     │
│  ┌────────────────────┴─────────────────────────────────┐  │
│  │       Webhook Registration                            │  │
│  │  Message: Create                                      │  │
│  │  Table: Account                                       │  │
│  │  Endpoint: https://external-system.com/webhook       │  │
│  └────────────────────┬─────────────────────────────────┘  │
│                       │ HTTP POST                           │
│                       ↓                                     │
│  ┌────────────────────────────────────────────────────────┐│
│  │       External System                                  ││
│  │  Receives notification                                 ││
│  │  Processes event                                       ││
│  └────────────────────────────────────────────────────────┘│
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

**Webhook Configuration:**

1. **Settings** → **Customizations** → **Customize the System**
2. **Service Endpoints** → **New**
3. Configure:
   - **Name**
   - **URL**: External endpoint
   - **Authentication**: None, SAS, etc.
4. Register step (which events)

### Power Automate HTTP Trigger

**Webhook alternative using Power Automate:**

```
Trigger: When an HTTP request is received
  (Generates webhook URL)
  ↓
Parse JSON (request body)
  ↓
Process event
  ↓
Respond to HTTP request
```

**Calling from external system:**

```
POST https://[flow-url]
Content-Type: application/json

{
  "event": "order.created",
  "orderId": "12345"
}
```

---

## AI Builder Integration

### What is AI Builder?

**AI Builder** adds AI capabilities to Power Platform:

```
AI Models:
  • Form processing (extract data from documents)
  • Object detection (identify objects in images)
  • Text recognition (OCR)
  • Prediction (custom ML models)
  • Category classification (classify text)
  • Entity extraction (extract info from text)
  • Sentiment analysis (positive/negative/neutral)
  • Key phrase extraction
  • Language detection
```

### Using AI Builder in Power Automate

**Example: Invoice Processing**

```
Trigger: When a file is created (SharePoint)
  ↓
Get file content
  ↓
Process and save information from forms (AI Builder)
  Model: Invoice processing
  Form: File content
  ↓
Apply to each: Extracted fields
  ↓
  Create row in Dataverse
    Invoice Number: {field}
    Total: {field}
    Date: {field}
  ↓
Move file to "Processed" folder
```

### Using AI Builder in Canvas Apps

**Example: Business Card Scanner**

```
Screen with Camera control
  ↓
User takes photo of business card
  ↓
BusinessCard.Analyze(Camera1.Photo)
  ↓
Extract:
  • Name
  • Company
  • Email
  • Phone
  ↓
Create contact in Dataverse
```

### AI Builder Models

**Pre-built models:**
- Ready to use
- No training required
- Business card reader, receipt processing, etc.

**Custom models:**
- Train with your data
- Category classification
- Object detection
- Prediction

> **Exam Tip**: AI Builder requires premium license. Pre-built models are ready to use. Custom models need training data. Commonly tested for invoice/form processing scenarios.

---

## Power BI Integration

### Power BI and Dataverse

**Direct connection:**

```
Power BI Desktop
  Get Data → Dataverse
  Enter environment URL
  Select tables
  Load data
  ↓
Create visualizations
  ↓
Publish to Power BI Service
  ↓
Embed in Power Apps
```

### Embedding Power BI in Power Apps

**Canvas Apps:**

```
Insert → Power BI tile
  Select workspace
  Select report
  Select tile

Dynamic filtering:
  PowerBITile1.DatasetId
  PowerBITile1.Report
  PowerBITile1.Filters = "[{...}]"
```

**Model-Driven Apps:**

```
Form designer → Components → Power BI
  Configure:
    - Workspace
    - Report
    - Tile
```

### Power BI Dataflows

**ETL for Power Platform:**

```
Power BI Dataflows:
  Extract data from sources
  Transform (Power Query)
  Load to Dataverse or storage
  ↓
Refresh schedule
  ↓
Apps/reports use data
```

---

## Common Integration Scenarios

### Scenario 1: SharePoint to Dataverse

**Question**: "Copy SharePoint list items to Dataverse daily."

**Solution**: Scheduled flow
```
Trigger: Recurrence (daily)
  ↓
Get items (SharePoint)
  ↓
Apply to each: Items
  ↓
  Add row (Dataverse)
```

### Scenario 2: On-Premises SQL to Cloud

**Question**: "Access on-premises SQL Server from Canvas App."

**Solution**: On-premises data gateway
```
1. Install gateway on-premises
2. Configure SQL data source
3. Canvas App: Add SQL connection via gateway
4. Use gateway in connection settings
```

### Scenario 3: External API Integration

**Question**: "Call company's REST API from Power Automate."

**Solution**: Custom connector or HTTP action
```
Option 1: Create custom connector (reusable)
Option 2: HTTP action with Azure AD (one-off)
```

### Scenario 4: Document Generation

**Question**: "Generate PDF contract from Dataverse data."

**Solution**: Word template + PDF conversion
```
Get row (Dataverse)
  ↓
Populate Word template
  ↓
Convert Word to PDF (using connector)
  ↓
Store in SharePoint or email
```

### Scenario 5: Real-Time External Data

**Question**: "Display live SQL Server data without copying to Dataverse."

**Solution**: Virtual table
```
1. Configure SQL Server virtual table
2. Map schema
3. Use in model-driven app
4. Data queried in real-time
```

### Scenario 6: Multi-System Sync

**Question**: "Keep Dataverse, SharePoint, and SQL Server in sync."

**Solution**: Hub-and-spoke pattern
```
Dataverse = Hub (master)
  ↓
Flow: When Dataverse changes
  ├─ Update SharePoint
  └─ Update SQL Server

Flow: When SharePoint/SQL changes
  └─ Update Dataverse
```

### Scenario 7: Approval with External System

**Question**: "Request approval in Teams for Dataverse record, update external system if approved."

**Solution**: Approval flow with HTTP action
```
Trigger: When row is added (Dataverse)
  ↓
Post adaptive card in Teams
  ↓
Wait for response
  ↓
If approved:
  ├─ Update Dataverse
  └─ HTTP POST to external API
```

### Scenario 8: Invoice Processing

**Question**: "Extract data from PDF invoices and create records."

**Solution**: AI Builder form processing
```
Trigger: File created (SharePoint)
  ↓
Process form (AI Builder)
  ↓
Create row (Dataverse)
  ↓
Send notification
```

### Scenario 9: Large Dataset from Excel

**Question**: "Import 10,000 rows from Excel to Dataverse."

**Solution**: Use Dataflows or Power Automate batch
```
Option 1: Dataflows
  - Connect to Excel
  - Transform
  - Load to Dataverse

Option 2: Power Automate
  - List rows (Excel)
  - Apply to each (with concurrency)
  - Add rows (Dataverse)
```

### Scenario 10: External Data in Forms

**Question**: "Display SQL Server data in Dataverse form without copying."

**Solution**: Virtual table
```
1. Create virtual table from SQL
2. Add relationship to Dataverse table
3. Add subgrid to form
4. Shows live SQL data
```

---

## Key Takeaways for the Exam

1. **Connectors are pre-built integrations** - 1000+ available
2. **Standard vs Premium connectors** - Dataverse, SQL, HTTP are premium
3. **Virtual tables display external data** - No copying, real-time query
4. **On-premises gateway required** - For on-premises data sources from cloud
5. **Gateway polls outbound** - No inbound firewall rules needed
6. **Connection references enable ALM** - Reconfigure connections between environments
7. **SharePoint for documents** - Dataverse + SharePoint is recommended pattern
8. **Excel does NOT support delegation** - Limited to 2,000 rows
9. **Web API for programmatic access** - REST API, OAuth authentication
10. **Custom connectors wrap REST APIs** - Reusable, require premium
11. **Webhooks for event notifications** - Call external endpoints on Dataverse events
12. **AI Builder requires premium** - Pre-built and custom models
13. **Power BI embeds in apps** - Direct Dataverse connection in Power BI
14. **Dataverse for Teams is limited** - Included with Teams license
15. **Use gateway for on-premises SQL** - Secure tunnel from cloud
16. **Virtual tables read-mostly** - Limited write operations
17. **SharePoint integration via document management** - Folders per record
18. **Teams adaptive cards** - Rich notifications in Teams channels
19. **HTTP action requires premium** - For custom API calls
20. **Sync patterns need error handling** - Scope + Configure Run After

---

*This concludes the Integration Scenarios deep dive. Continue to other deep dive documents for comprehensive PL-200 preparation.*
