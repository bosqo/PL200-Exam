# Environment Variables and ALM - Complete Deep Dive
## PL-200 Exam Preparation

---

## Table of Contents
1. [What are Environment Variables?](#what-are-environment-variables)
2. [Environment Variable Types](#environment-variable-types)
3. [Creating and Managing Environment Variables](#creating-and-managing-environment-variables)
4. [Using Environment Variables in Power Platform](#using-environment-variables-in-power-platform)
5. [Connection References](#connection-references)
6. [Azure Key Vault Integration](#azure-key-vault-integration)
7. [Application Lifecycle Management (ALM) Overview](#application-lifecycle-management-alm-overview)
8. [Solution Structure for ALM](#solution-structure-for-alm)
9. [Deploying Across Environments](#deploying-across-environments)
10. [Environment Variables in ALM](#environment-variables-in-alm)
11. [Best Practices](#best-practices)
12. [Common Exam Scenarios](#common-exam-scenarios)

---

## What are Environment Variables?

### The Problem Environment Variables Solve

```
BEFORE Environment Variables:
┌──────────────────────────────────────────┐
│  API Endpoint: https://api.prod.com      │
│  Username: admin@prod.onmicrosoft.com    │
│  Database: ProdDB                        │
│  Hardcoded in Power Automate flows       │
└──────────────────────────────────────────┘
        ↓
Issue: Move flow to TEST environment
        ↓
Must MANUALLY change:
  ├─ API endpoint to TEST
  ├─ Username to TEST
  └─ Database to TEST
        ↓
Error-prone, time-consuming, security risk

AFTER Environment Variables:
┌──────────────────────────────────────────┐
│  Variable: ApiEndpoint                   │
│  Variable: AppUsername                   │
│  Variable: DatabaseName                  │
│  Referenced in Power Automate flows      │
└──────────────────────────────────────────┘
        ↓
Move flow to TEST environment
        ↓
Environment variables AUTOMATICALLY
  updated by system administrator
        ↓
No manual flow editing needed!
```

### Key Benefits

| Benefit | Explanation |
|---------|-------------|
| **No Hardcoding** | Configuration separate from logic |
| **Portability** | Easy movement between environments |
| **Security** | Secrets not exposed in solutions |
| **Consistency** | Same value used everywhere |
| **Maintainability** | Change value once, updates all |

### Use Cases

- **API Endpoints**: Different URLs per environment
- **Usernames/Emails**: Recipient changes per environment
- **API Keys**: Different keys for dev/test/prod
- **Feature Flags**: Enable/disable features per environment
- **Configuration Settings**: App-wide settings

---

## Environment Variable Types

### 1. Configuration Values (Text)

Plain text values that change between environments:

```
Variable Name: ApiEnvironment
Type: Text
Values:
  ├─ DEV: https://dev-api.company.com
  ├─ TEST: https://test-api.company.com
  └─ PROD: https://api.company.com
```

**Use Cases:**
- URLs
- Endpoint addresses
- File paths
- Server names
- Email addresses

### 2. Secrets (Sensitive Values)

Encrypted values for sensitive data:

```
Variable Name: ApiKey
Type: Secret
Values:
  ├─ DEV: [encrypted-dev-key]
  ├─ TEST: [encrypted-test-key]
  └─ PROD: [encrypted-prod-key]
```

**Characteristics:**
- Encrypted in storage
- Cannot be read/exported
- Must be manually entered (not automated)
- Only admin can set values

**Use Cases:**
- API keys
- Passwords
- Authentication tokens
- Connection strings with credentials

**Important:** Secrets are NOT automatically updated when moving solutions. Admins must manually enter values in target environment.

### Comparison

| Aspect | Configuration Value | Secret |
|--------|---------------------|---------|
| **Encrypted** | ✗ No | ✓ Yes |
| **Displayed in UI** | ✓ Yes | ✗ No |
| **Updated on Import** | ✓ Automatic (if defined) | ✗ Manual entry required |
| **Exportable** | ✓ Yes | ✗ No |
| **Security** | Medium | High |

---

## Creating and Managing Environment Variables

### Creating in Power Platform Admin Center

**Steps:**
1. **Power Platform Admin Center** → **Environments**
2. Select environment
3. **Settings** → **Product updates** → **Environment variables**
4. **New environment variable**
5. Configure:
   - **Name**: Variable identifier
   - **Display name**: User-friendly name
   - **Type**: Configuration Value or Secret
   - **Value**: Default value (optional)

### Environment Variable Values

Each variable can have DIFFERENT values per environment:

```
Variable: ApiKey (Type: Secret)

Environment: Development
  └─ Value: dev-key-12345 [Set by Admin]

Environment: Testing
  └─ Value: test-key-67890 [Set by Admin]

Environment: Production
  └─ Value: prod-key-abcde [Set by Admin]
```

**Setting Values:**
1. Go to environment settings
2. **Environment variables**
3. Select variable
4. **Current value** → Set value for this environment
5. Save

### Lifecycle

```
Create Variable
    ↓
    ├─→ DEV: Set value
    ├─→ TEST: Set value (on import)
    ├─→ PROD: Set value (on import)
    ↓
Use in Power Automate / Power Apps
    ↓
Reference value: Use() expression
    ↓
Value pulled from current environment
```

---

## Using Environment Variables in Power Platform

### Power Automate (Flows)

**Reference environment variable:**

```
Dynamic content → "Environment Variables" section
  ├─ ApiEndpoint
  ├─ ApiKey
  └─ AppUsername

Example Flow:
  Action: HTTP request
    URL: @{environment('ApiEndpoint')}/customers
    Headers: Authorization: Bearer @{environment('ApiKey')}
```

**Expression syntax:**
```
@environment('VariableName')
```

### Power Apps (Canvas & Model-Driven)

**Reference environment variable in formulas:**

```powerapps
// Canvas App
Label1.Text = Concatenate("API:", Param('ApiEndpoint').Value)

// Model-Driven custom page
ThisForm.Setting('FeatureFlag').Value
```

**Important:** Environment variables accessible in:
- ✓ Power Automate flows
- ✓ Custom pages (model-driven)
- ✓ Canvas apps (Power Apps CLI)
- ✗ Regular model-driven forms (limitations)

### Power BI

Environment variables used in:
- Data source queries (parameter passing)
- Refresh schedules
- Report filters

---

## Connection References

### What are Connection References?

Connection references allow solutions to **contain connectors without hardcoded credentials**:

```
┌─────────────────────────────────────────────┐
│           CONNECTION REFERENCE              │
├─────────────────────────────────────────────┤
│  Name: SQL Server Connection                │
│  Connector: SQL Server                      │
│  Server: [Configured per environment]       │
│  Database: [Configured per environment]     │
│                                              │
│  When moved to new environment:              │
│  Admin reconfigures server/database          │
│  Flows use new connection automatically      │
└─────────────────────────────────────────────┘
```

### Creating Connection References

**In Solution:**
1. **Add existing** → **Connection reference**
2. Select connector (SQL Server, SharePoint, etc.)
3. Create new connection reference
4. Name: "SQL Server Production"
5. Add to solution

### Using Connection References

**In Power Automate Flow (within solution):**

```
Action: Execute a SQL query
  Connection: @ref('SQL_Server_Production')
```

**On Import:**
1. Solution imported to TEST environment
2. Admin sees: "Connection references need configuration"
3. Admin selects TEST SQL Server instance
4. Flow automatically uses new connection

### Advantages

| Aspect | Hardcoded | Connection Reference |
|--------|-----------|----------------------|
| **Visible credentials** | ✓ Yes (security risk) | ✗ No (secure) |
| **Portable** | ✗ No | ✓ Yes |
| **Update on import** | ✗ Manual | ✓ Admin configures |
| **Security** | ✗ Poor | ✓ Good |

---

## Azure Key Vault Integration

### What is Azure Key Vault?

**Azure Key Vault** = Centralized secret storage service for sensitive credentials:

```
┌─────────────────────────────────────────────────────┐
│              AZURE KEY VAULT ARCHITECTURE            │
├─────────────────────────────────────────────────────┤
│                                                      │
│  Key Vault (in Azure)                              │
│  ├─ API Keys                                        │
│  ├─ Connection Strings                              │
│  ├─ Passwords                                       │
│  ├─ Certificates                                    │
│  └─ Access controlled by IAM                        │
│                                                      │
│  Power Platform accesses via:                       │
│  ├─ Azure AD authentication                         │
│  ├─ Managed Identity (recommended)                  │
│  └─ Service Principal                               │
│                                                      │
│  Environment Variable → Point to Azure Key Vault  │
│      ↓                                               │
│  Power Automate / Power Apps access secret         │
│      ↓                                               │
│  Key Vault enforces access control                 │
│                                                      │
└─────────────────────────────────────────────────────┘
```

### Setting Up Key Vault Integration

**Prerequisites:**
- Azure subscription
- Azure Key Vault created
- Secrets stored in Key Vault
- Power Platform service principal configured

**Configuration:**

1. **Create secret in Key Vault:**
   - Key: `ApiKey`
   - Value: `your-api-key-12345`
   - Access policy: Allow Power Platform app

2. **Create environment variable:**
   - Name: `ApiKeyFromVault`
   - Type: Secret
   - Leave Value blank (will fetch from Key Vault)

3. **Configure Key Vault reference:**
   - Secret: Select from Key Vault
   - This can vary per environment

4. **In Power Automate:**
   - Use: `@environment('ApiKeyFromVault')`
   - Power Platform fetches from Key Vault automatically

### Azure Key Vault URI Format

```
https://vault.azure.net/secrets/{secret-name}/{version}

Example:
https://myvault.vault.azure.net/secrets/ApiKey/1234567890
```

### Benefits Over Direct Secrets

| Aspect | Direct Secret | Key Vault |
|--------|---------------|-----------|
| **Centralized** | ✗ No | ✓ Yes |
| **Audit Log** | ✗ Limited | ✓ Full |
| **Access Control** | ✗ Basic | ✓ Advanced (IAM) |
| **Compliance** | ⚠ Partial | ✓ Full |
| **Rotation** | Manual | Automatic |

---

## Application Lifecycle Management (ALM) Overview

### ALM Definition

ALM = Managing solutions from **development → test → production** with:
- Version control
- Testing
- Deployment automation
- Environment configuration

```
┌──────────────────────────────────────────────────────────┐
│                    ALM LIFECYCLE                          │
├──────────────────────────────────────────────────────────┤
│                                                           │
│  Phase 1: DEVELOP                                        │
│  ├─ Create features in DEV environment                   │
│  ├─ Build solutions                                      │
│  ├─ Unit test locally                                    │
│  └─ Export as managed solution                           │
│       ↓                                                   │
│  Phase 2: TEST (UAT)                                     │
│  ├─ Import managed solution                              │
│  ├─ Configure environment variables                      │
│  ├─ Run user acceptance tests                            │
│  ├─ Validate integrations                                │
│  └─ Approve or request changes                           │
│       ↓                                                   │
│  Phase 3: PRODUCTION                                     │
│  ├─ Import managed solution                              │
│  ├─ Configure production values                          │
│  ├─ Execute cutover plan                                 │
│  ├─ Monitor for issues                                   │
│  └─ Go live                                              │
│       ↓                                                   │
│  Phase 4: MAINTAIN                                       │
│  ├─ Monitor and support                                  │
│  ├─ Fix bugs → Test → Deploy                            │
│  └─ Plan enhancements → Cycle back to Develop           │
│                                                           │
└──────────────────────────────────────────────────────────┘
```

### ALM Scope

| Item | Manages | Doesn't Manage |
|------|---------|-----------------|
| **Solutions** | ✓ Components | ✗ User data |
| **Environment Variables** | ✓ Configuration | ✗ Secrets (manual) |
| **Connections** | ✓ References | ✗ Credentials |
| **Flows** | ✓ Logic | ✗ Runtime data |

---

## Solution Structure for ALM

### Managed vs Unmanaged Solutions (Review)

```
UNMANAGED (Development)
├─ Everything editable
├─ Components merge with Default Solution
├─ No versioning
├─ Use in: DEV only

MANAGED (Test/Prod)
├─ Locked and protected
├─ Can be uninstalled cleanly
├─ Versioning (1.0, 1.1, etc.)
├─ Use in: TEST, PROD
```

### Solution Publisher Configuration

Every solution needs a publisher:

```
Publisher Setup
├─ Publisher name: Contoso
├─ Publisher prefix: contoso
├─ Option value prefix: 10000
└─ Used for: Schema naming, option sets
```

**Schema Names Generated:**
- Table: `contoso_project`
- Field: `contoso_estimate`
- Choice: `contoso_priority`

### Solution Composition

```
Solution: Sales Management v1.0.0
├─ Publisher: Contoso
├─ Version: 1.0.0
├─ Components:
│  ├─ Tables (Opportunity, Quote, Contract)
│  ├─ Forms (Opportunity Main, Quote Detail)
│  ├─ Views (Active Opportunities)
│  ├─ Flows (Send Quote Approval)
│  ├─ Business Rules (Validate discount)
│  ├─ Charts (Revenue pipeline)
│  ├─ Dashboards (Sales Dashboard)
│  ├─ Environment Variables (ApiEndpoint, FeatureFlag)
│  └─ Connection References (SQL Server connection)
```

---

## Deploying Across Environments

### Standard Deployment Process

```
1. Export from DEV
   └─ Solution → Export as Managed
      Solution_1.0.0.zip

2. Import to TEST
   ├─ Power Platform Admin Center
   ├─ Import solution
   ├─ Select: TEST environment
   ├─ Upload: Solution_1.0.0.zip
   └─ Choose: Update or Upgrade

3. Configure in TEST
   ├─ Environment variables → Set TEST values
   ├─ Connection references → Reconfigure
   ├─ Test flows with TEST data

4. Export from TEST (optional)
   └─ Create backup

5. Import to PROD
   ├─ Power Platform Admin Center
   ├─ Import solution
   ├─ Select: PROD environment
   ├─ Upload: Solution_1.0.0.zip
   └─ Choose: Update or Upgrade

6. Configure in PROD
   ├─ Environment variables → Set PROD values
   ├─ Connection references → Point to PROD systems
   └─ Verify all connections working

7. Cut Over
   ├─ Enable flows in PROD
   ├─ Notify users
   ├─ Monitor for issues
```

### Update vs Upgrade

```
UPDATE:
├─ Adds new components
├─ Updates existing components
├─ KEEPS old components not in new solution
├─ Use for: Incremental updates

Example:
  OLD Solution: Tables A, B, Forms X
  NEW Solution: Tables A, B, C, Forms X, Y
  Result: A, B, C, X, Y (all kept)

UPGRADE:
├─ Replaces entire solution
├─ REMOVES components not in new version
├─ Use for: Major version changes

Example:
  OLD Solution: Tables A, B, Forms X
  NEW Solution: Tables A, C, Forms Y
  Result: A, C, Y (B and X removed)
```

> **Exam Tip**: Use UPGRADE when you've deleted components in DEV and want them removed from target. Use UPDATE for normal deployments.

---

## Environment Variables in ALM

### Using Environment Variables for Environment-Specific Configuration

```
┌─────────────────────────────────────────────────────────┐
│          ENVIRONMENT VARIABLE VALUES BY ENVIRONMENT      │
├─────────────────────────────────────────────────────────┤
│                                                          │
│ Variable: ApiEndpoint                                  │
│ ├─ DEV:  https://dev-api.internal.com                  │
│ ├─ TEST: https://test-api.internal.com                 │
│ └─ PROD: https://api.company.com                       │
│                                                          │
│ Variable: SendErrorNotificationTo                      │
│ ├─ DEV:  admin@internal.com                            │
│ ├─ TEST: qa-team@internal.com                          │
│ └─ PROD: support@company.com                           │
│                                                          │
│ Variable: FeatureFlag_NewReports                       │
│ ├─ DEV:  true (testing new feature)                    │
│ ├─ TEST: true (verified by users)                      │
│ └─ PROD: false (rollout next week)                     │
│                                                          │
│ Variable: DataImportBatchSize                          │
│ ├─ DEV:  100                                            │
│ ├─ TEST: 1000                                           │
│ └─ PROD: 5000 (production-scale)                       │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

### Secrets in ALM

**Important:** Secrets are NOT imported with values!

```
On Import to TEST:
├─ Solution imported successfully
├─ Environment variable created
├─ Secret value: [EMPTY]
├─ Admin must manually enter value
└─ System asks for input during import
```

**Recommended Approach:**
1. Create Configuration Value (not Secret) when possible
2. If must be Secret:
   - Document in deployment guide
   - Have TEST admin enter value during import
   - Use Azure Key Vault for PROD

---

## Best Practices

### 1. Naming Conventions

```
Environment Variables:
├─ ApiEndpoint_Salesforce
├─ ApiEndpoint_Accounting
├─ FeatureFlag_NewUI
├─ FeatureFlag_AdvancedReporting
├─ Email_SupportTeam
├─ Email_AdminTeam

Connection References:
├─ SQL_Server_Main
├─ Accounting_API_Connection
├─ SharePoint_HR_Connection
```

### 2. Documentation

Create deployment guide with:
```
Environment Variables to Configure:
├─ ApiEndpoint
│  ├─ DEV: ___________
│  ├─ TEST: __________
│  └─ PROD: __________
│
├─ ConnectionString
│  ├─ DEV: __________
│  ├─ TEST: __________
│  └─ PROD: __________
```

### 3. Testing Configuration

```
Always test with environment variable values:
├─ In DEV with DEV values
├─ In TEST with TEST values
├─ In PROD with PROD values

Never use hardcoded fallbacks!
```

### 4. Secrets Management

```
✓ DO:
├─ Use Azure Key Vault for PROD secrets
├─ Have only admin know sensitive values
├─ Rotate keys regularly
├─ Log all secret access

✗ DON'T:
├─ Hardcode API keys
├─ Share secrets in email/docs
├─ Use same key in all environments
├─ Store secrets in version control
```

---

## Common Exam Scenarios

### Scenario 1: Moving Flow Between Environments

**Question**: "Flow in DEV environment uses hardcoded API endpoint (https://dev-api.com). Moving to TEST requires TEST endpoint (https://test-api.com). What's the best approach?"

**Answer**: Create environment variable:
- Name: `ApiEndpoint`
- Type: Configuration Value
- DEV value: https://dev-api.com
- TEST value: https://test-api.com
- Reference in flow: `@environment('ApiEndpoint')`
- No manual edits needed when moving solutions!

### Scenario 2: Sensitive Configuration

**Question**: "Power Automate flow needs to call API with secret key. Key differs per environment and should be secure. Solution?"

**Answer**:
1. Create environment variable:
   - Name: `ApiKey`
   - Type: **Secret** (encrypted)
2. Store in Azure Key Vault (for PROD)
3. Reference in flow: `@environment('ApiKey')`
4. Admin manually enters values during import
5. Never hardcoded, never in logs

### Scenario 3: Multi-Environment Configuration

**Question**: "Solution needs different email recipients per environment (admin@internal for DEV, qa@internal for TEST, support@company for PROD). How to implement?"

**Answer**:
```
Create Environment Variable: ErrorNotificationRecipient
├─ Type: Configuration Value
├─ DEV: admin@internal.com
├─ TEST: qa@internal.com
└─ PROD: support@company.com

In Flow → Send email action:
└─ To: @environment('ErrorNotificationRecipient')

Each environment automatically sends to correct recipient!
```

### Scenario 4: Feature Flag Rollout

**Question**: "New report feature must be disabled in PROD until next week, but enabled in TEST for verification. How?"

**Answer**:
```
Environment Variable: FeatureFlag_NewReports
├─ DEV: true
├─ TEST: true
└─ PROD: false

In Flow/App:
if environment('FeatureFlag_NewReports') then
    Show new reports
else
    Show old reports

No code changes needed!
Change flag value day of release.
```

### Scenario 5: Solution Import with Connection Reference

**Question**: "Managed solution contains Power Automate flow using SQL Server connector. Importing to TEST. What happens?"

**Answer**:
1. Solution imports successfully
2. System recognizes connection reference
3. Admin sees: "Reconfigure connection references"
4. Admin selects TEST SQL Server instance
5. Flow automatically uses new connection
6. No need to edit flow!

---

## Key Takeaways for the Exam

1. **Environment variables separate configuration from logic**
2. **Two types: Configuration Value (text) and Secret (encrypted)**
3. **Secrets not automatically imported** - admin must enter manually
4. **Configuration Values automatically updated** on import (if defined)
5. **Connection References** - store connector configs separately
6. **Azure Key Vault** - centralized secret storage for PROD
7. **ALM Lifecycle**: DEV (unmanaged) → TEST (managed) → PROD (managed)
8. **Managed solutions** can be versioned and uninstalled cleanly
9. **UPDATE** vs **UPGRADE** - choose based on whether components deleted
10. **Always reference environment variables** in flows/apps instead of hardcoding
11. **Solutions include** tables, forms, flows, business rules, charts, dashboards, variables
12. **Never commit secrets** to version control or expose in code

---

*Created for PL-200 Exam Preparation*
