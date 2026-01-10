# Power Pages - Complete Deep Dive
## PL-200 Exam Preparation

---

## Table of Contents
1. [What is Power Pages?](#what-is-power-pages)
2. [Power Pages Architecture](#power-pages-architecture)
3. [Security Model In-Depth](#security-model-in-depth)
4. [Pages and Navigation](#pages-and-navigation)
5. [Lists and Forms](#lists-and-forms)
6. [Authentication](#authentication)
7. [Advanced Features](#advanced-features)
8. [Common Exam Scenarios](#common-exam-scenarios)

---

## What is Power Pages?

### Overview

Power Pages (formerly Power Apps Portals) allows you to create **external-facing websites** that interact with Dataverse data. Unlike model-driven and canvas apps which are for internal users, Power Pages is for:

- Customers
- Partners
- Vendors
- Citizens (government)
- Anyone outside your organization

```
┌─────────────────────────────────────────────────────────────────┐
│                    POWER PLATFORM APPS                           │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  INTERNAL USERS                    EXTERNAL USERS               │
│  ┌─────────────────────┐          ┌─────────────────────┐       │
│  │   Model-Driven      │          │                     │       │
│  │      Apps           │          │    POWER PAGES      │       │
│  ├─────────────────────┤          │                     │       │
│  │   Canvas Apps       │          │   • Customers       │       │
│  ├─────────────────────┤          │   • Partners        │       │
│  │   Power Automate    │          │   • Public users    │       │
│  └─────────────────────┘          └─────────────────────┘       │
│           │                                │                     │
│           └────────────┬───────────────────┘                    │
│                        ▼                                         │
│              ┌─────────────────┐                                │
│              │    DATAVERSE    │                                │
│              └─────────────────┘                                │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Key Use Cases

| Scenario | Example |
|----------|---------|
| **Self-Service Portal** | Customers submit and track support cases |
| **Partner Portal** | Partners access shared documents, opportunities |
| **Community Forum** | Users discuss and share knowledge |
| **Application Portal** | Citizens apply for permits, licenses |
| **Event Registration** | External users register for events |

### Power Pages vs SharePoint Sites

| Feature | Power Pages | SharePoint |
|---------|-------------|------------|
| Data Source | Dataverse | SharePoint Lists |
| Authentication | Local, Azure AD B2C, Social | Microsoft 365 |
| Customization | High | Medium |
| External Users | Primary focus | Requires configuration |
| Forms/Workflows | Integrated with Power Platform | Limited |

---

## Power Pages Architecture

### Component Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                      POWER PAGES SITE                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │                    WEB PAGES                             │    │
│  │  • Home Page                                             │    │
│  │  • About Us                                              │    │
│  │  • Contact Us                                            │    │
│  │  • Support Cases                                         │    │
│  └─────────────────────────────────────────────────────────┘    │
│                                                                  │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐          │
│  │    LISTS     │  │    FORMS     │  │   WEB ROLES  │          │
│  │  (Entity     │  │  (Basic &    │  │  (Security)  │          │
│  │   Lists)     │  │   Advanced)  │  │              │          │
│  └──────────────┘  └──────────────┘  └──────────────┘          │
│                                                                  │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐          │
│  │    TABLE     │  │    PAGE      │  │   SITE       │          │
│  │ PERMISSIONS  │  │ PERMISSIONS  │  │  SETTINGS    │          │
│  └──────────────┘  └──────────────┘  └──────────────┘          │
│                                                                  │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │                    DATAVERSE                             │    │
│  │              (Data Storage & Security)                   │    │
│  └─────────────────────────────────────────────────────────┘    │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Site Components

| Component | Purpose | Stored In |
|-----------|---------|-----------|
| **Web Pages** | Content pages (HTML, text) | Dataverse |
| **Web Templates** | Page layouts and structure | Dataverse |
| **Content Snippets** | Reusable content blocks | Dataverse |
| **Web Files** | CSS, JavaScript, images | Dataverse |
| **Site Settings** | Configuration key-value pairs | Dataverse |
| **Site Markers** | Named references to pages | Dataverse |

> **Key Concept**: Everything in Power Pages is stored in Dataverse tables. The portal reads from these tables to render the website.

### Portal Management App

A model-driven app for advanced configuration:

```
┌─────────────────────────────────────────────────────────────────┐
│                 PORTAL MANAGEMENT APP                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  Website                                                         │
│  ├── Web Pages                                                  │
│  ├── Web Files                                                  │
│  ├── Web Templates                                              │
│  ├── Content Snippets                                           │
│  └── Page Templates                                             │
│                                                                  │
│  Security                                                        │
│  ├── Web Roles                                                  │
│  ├── Table Permissions                                          │
│  ├── Page Permissions (Website Access Permissions)              │
│  └── Web Page Access Control Rules                              │
│                                                                  │
│  Content                                                         │
│  ├── Site Settings                                              │
│  ├── Site Markers                                               │
│  └── Shortcuts                                                  │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Security Model In-Depth

### Security Layers

Power Pages has multiple security layers. Understanding this is **critical for the exam**.

```
┌─────────────────────────────────────────────────────────────────┐
│                   SECURITY LAYERS                                │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  LAYER 1: AUTHENTICATION                                        │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │ Who is this user?                                        │    │
│  │ • Anonymous (not logged in)                              │    │
│  │ • Authenticated (logged in)                              │    │
│  └─────────────────────────────────────────────────────────┘    │
│                          ▼                                       │
│  LAYER 2: WEB ROLES                                             │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │ What role does this user have?                           │    │
│  │ • Customer                                               │    │
│  │ • Partner                                                │    │
│  │ • Administrator                                          │    │
│  └─────────────────────────────────────────────────────────┘    │
│                          ▼                                       │
│  LAYER 3: PAGE PERMISSIONS                                      │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │ Which pages can this role access?                        │    │
│  │ • Public pages (anyone)                                  │    │
│  │ • Authenticated pages (logged-in users)                  │    │
│  │ • Role-specific pages                                    │    │
│  └─────────────────────────────────────────────────────────┘    │
│                          ▼                                       │
│  LAYER 4: TABLE PERMISSIONS                                     │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │ What data can this role access?                          │    │
│  │ • Which tables                                           │    │
│  │ • CRUD operations                                        │    │
│  │ • Which records (scope)                                  │    │
│  └─────────────────────────────────────────────────────────┘    │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Web Roles

Web Roles are the **foundation of portal security**. They group users with similar access needs.

```
┌─────────────────────────────────────────────────────────────────┐
│                        WEB ROLES                                 │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │ Web Role: Customer                                       │    │
│  │ ┌─────────────────────────────────────────────────────┐ │    │
│  │ │ Page Permissions:                                   │ │    │
│  │ │   • View Support page                               │ │    │
│  │ │   • View Knowledge Base                             │ │    │
│  │ ├─────────────────────────────────────────────────────┤ │    │
│  │ │ Table Permissions:                                  │ │    │
│  │ │   • Cases: Create, Read (own), Update (own)        │ │    │
│  │ │   • KB Articles: Read (all)                        │ │    │
│  │ └─────────────────────────────────────────────────────┘ │    │
│  └─────────────────────────────────────────────────────────┘    │
│                                                                  │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │ Web Role: Partner                                        │    │
│  │ ┌─────────────────────────────────────────────────────┐ │    │
│  │ │ Page Permissions:                                   │ │    │
│  │ │   • View Partner Portal                             │ │    │
│  │ │   • View Opportunities                              │ │    │
│  │ ├─────────────────────────────────────────────────────┤ │    │
│  │ │ Table Permissions:                                  │ │    │
│  │ │   • Opportunities: Read (related to account)       │ │    │
│  │ │   • Documents: Read, Create (related)              │ │    │
│  │ └─────────────────────────────────────────────────────┘ │    │
│  └─────────────────────────────────────────────────────────┘    │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

#### Authenticated Users Role

A special web role property that **automatically assigns** the role to any logged-in user.

```
┌─────────────────────────────────────────────────────────────────┐
│ Web Role: Authenticated Users                                    │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ☑ Authenticated Users Role = YES                               │
│                                                                  │
│  This means:                                                     │
│  • Every user who logs in automatically gets this role          │
│  • No manual assignment needed                                   │
│  • Good for baseline permissions                                 │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

> **Exam Tip**: When asked "How to automatically grant permissions to all logged-in users?" → Create a Web Role with **Authenticated Users Role = Yes**

#### Anonymous Users Role

For users who are NOT logged in:

```
┌─────────────────────────────────────────────────────────────────┐
│ Web Role: Anonymous Users                                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ☑ Anonymous Users Role = YES                                   │
│                                                                  │
│  This means:                                                     │
│  • Applies to users not logged in                               │
│  • Typically read-only access to public content                 │
│  • Cannot access table data (usually)                           │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Table Permissions

Control what **data** each web role can access.

#### Permission Components

| Component | Description |
|-----------|-------------|
| **Table** | Which Dataverse table |
| **Privileges** | Create, Read, Update, Delete, Append, Append To |
| **Scope** | Which records (Global, Contact, Account, Parent, Self) |
| **Web Role** | Which role gets this permission |

#### Scope Types (Critical for Exam!)

```
┌─────────────────────────────────────────────────────────────────┐
│                    TABLE PERMISSION SCOPES                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  GLOBAL SCOPE                                                    │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │ Access ALL records in the table                         │    │
│  │ Use for: Public reference data (Countries, Categories)  │    │
│  └─────────────────────────────────────────────────────────┘    │
│                                                                  │
│  CONTACT SCOPE                                                   │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │ Access records where Contact = logged-in user's contact │    │
│  │ Use for: User's own records (My Cases, My Orders)       │    │
│  │ Requires: Contact lookup on the table                   │    │
│  └─────────────────────────────────────────────────────────┘    │
│                                                                  │
│  ACCOUNT SCOPE                                                   │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │ Access records where Account = user's parent account    │    │
│  │ Use for: Company-wide data (All company orders)         │    │
│  │ Requires: Account lookup on the table                   │    │
│  └─────────────────────────────────────────────────────────┘    │
│                                                                  │
│  PARENT SCOPE                                                    │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │ Access records related to a parent table permission     │    │
│  │ Use for: Child records (Order Lines for accessible      │    │
│  │          Orders)                                        │    │
│  │ Requires: Parent table permission configured            │    │
│  └─────────────────────────────────────────────────────────┘    │
│                                                                  │
│  SELF SCOPE                                                      │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │ Access the Contact record that IS the logged-in user    │    │
│  │ Use for: Profile pages (Edit my profile)                │    │
│  │ Only for: Contact table                                 │    │
│  └─────────────────────────────────────────────────────────┘    │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

#### Example: Customer Support Portal

```
Customer Web Role
    │
    ├── Table Permission: Cases
    │   ├── Scope: Contact
    │   ├── Read: ✓
    │   ├── Create: ✓
    │   ├── Update: ✓ (own cases only)
    │   └── Delete: ✗
    │
    ├── Table Permission: Case Notes
    │   ├── Scope: Parent (Cases)
    │   ├── Read: ✓
    │   └── Create: ✓
    │
    └── Table Permission: KB Articles
        ├── Scope: Global
        └── Read: ✓
```

### Page Permissions

Control which **pages** users can see.

#### Web Page Access Control Rules

```
┌─────────────────────────────────────────────────────────────────┐
│           WEB PAGE ACCESS CONTROL RULE                           │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  Page: Support Portal                                            │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │ Right: Grant Change                                      │    │
│  │ Scope: All Content                                       │    │
│  │ Web Role: Customer                                       │    │
│  └─────────────────────────────────────────────────────────┘    │
│                                                                  │
│  Right Options:                                                  │
│  • Restrict Read - Deny access to page                          │
│  • Grant Change - Allow access (read + interact)                │
│                                                                  │
│  Scope Options:                                                  │
│  • All Content - Page and all child pages                       │
│  • Exclude direct child web files - Page only                   │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Pages and Navigation

### Page Types

| Type | Description | Use Case |
|------|-------------|----------|
| **Web Page** | Content page with HTML | General content |
| **Web Link** | URL redirect | External links |
| **Shortcut** | Internal page reference | Quick navigation |

### Page Templates

Define the **layout structure** for pages.

```
┌─────────────────────────────────────────────────────────────────┐
│                    PAGE TEMPLATE                                 │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  Template: Full Page                                             │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │ {% include 'Header' %}                                   │    │
│  │                                                          │    │
│  │ <div class="container">                                  │    │
│  │   {{ page.content }}    ← Page content goes here        │    │
│  │ </div>                                                   │    │
│  │                                                          │    │
│  │ {% include 'Footer' %}                                   │    │
│  └─────────────────────────────────────────────────────────┘    │
│                                                                  │
│  Template: Two Column                                            │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │ {% include 'Header' %}                                   │    │
│  │                                                          │    │
│  │ <div class="row">                                        │    │
│  │   <div class="col-8">{{ page.content }}</div>           │    │
│  │   <div class="col-4">{% include 'Sidebar' %}</div>      │    │
│  │ </div>                                                   │    │
│  │                                                          │    │
│  │ {% include 'Footer' %}                                   │    │
│  └─────────────────────────────────────────────────────────┘    │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Liquid Template Language

Power Pages uses **Liquid** for dynamic content.

**Common Liquid Tags:**

```liquid
{% Comment %}
{{ variable }}              - Output a variable
{% if condition %}          - Conditional logic
{% for item in collection %} - Loop through items
{% include 'snippet' %}     - Include content snippet
{% endcomment %}

Example: Show user's name if logged in
{% if user %}
  Welcome, {{ user.fullname }}!
{% else %}
  Please sign in.
{% endif %}
```

**Accessing Dataverse Data:**

```liquid
{% fetchxml cases %}
<fetch>
  <entity name="incident">
    <attribute name="title" />
    <attribute name="ticketnumber" />
    <filter>
      <condition attribute="customerid" operator="eq" value="{{ user.id }}" />
    </filter>
  </entity>
</fetch>
{% endfetchxml %}

{% for case in cases.results.entities %}
  <p>{{ case.ticketnumber }}: {{ case.title }}</p>
{% endfor %}
```

---

## Lists and Forms

### Entity Lists (Lists)

Display **multiple records** from a Dataverse table.

```
┌─────────────────────────────────────────────────────────────────┐
│                      ENTITY LIST                                 │
│                   "My Support Cases"                             │
├─────────────────────────────────────────────────────────────────┤
│ Case Number  │ Title           │ Status    │ Created    │ Action│
├──────────────┼─────────────────┼───────────┼────────────┼───────┤
│ CAS-00123    │ Login Issue     │ Active    │ 01/15/2024 │ [View]│
│ CAS-00124    │ Billing Question│ Resolved  │ 01/14/2024 │ [View]│
│ CAS-00125    │ Feature Request │ Active    │ 01/13/2024 │ [View]│
└──────────────┴─────────────────┴───────────┴────────────┴───────┘
           │                                              │
           │                                              │
    Uses a Dataverse View                    Links to Basic Form
    to determine columns                     for record details
```

**List Configuration:**

| Setting | Description |
|---------|-------------|
| **Table** | Which Dataverse table to display |
| **View** | Which view determines columns and filter |
| **Page Size** | Records per page |
| **Create Button** | Enable/disable new record creation |
| **Details Action** | Link to form for viewing details |
| **Search** | Enable search box |

### Basic Forms

Display/edit **a single record**.

```
┌─────────────────────────────────────────────────────────────────┐
│                      BASIC FORM                                  │
│                   "Submit Support Case"                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  Title:                                                          │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │ [Enter case title...                                   ]│    │
│  └─────────────────────────────────────────────────────────┘    │
│                                                                  │
│  Description:                                                    │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │ [Enter description...                                  ]│    │
│  │ [                                                      ]│    │
│  │ [                                                      ]│    │
│  └─────────────────────────────────────────────────────────┘    │
│                                                                  │
│  Priority:                                                       │
│  ○ Low   ○ Medium   ● High                                      │
│                                                                  │
│                                        [Cancel]  [Submit]        │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

**Basic Form Modes:**

| Mode | Purpose |
|------|---------|
| **Insert** | Create new record |
| **Edit** | Modify existing record |
| **ReadOnly** | View record (no editing) |

**Basic Form Configuration:**

| Setting | Description |
|---------|-------------|
| **Table** | Which table |
| **Form** | Which Dataverse form to use |
| **Mode** | Insert, Edit, or ReadOnly |
| **Redirect** | Where to go after submit |
| **Success Message** | Message shown after submit |

### Advanced Forms (Multistep Forms)

Guide users through **multiple steps** to complete a process.

```
┌─────────────────────────────────────────────────────────────────┐
│                    ADVANCED FORM                                 │
│               "Loan Application Process"                         │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  Progress: [Step 1]═══[Step 2]═══[Step 3]═══[Step 4]            │
│              ✓         Current      ○          ○                │
│           Personal    Employment   Financial  Review            │
│            Info        Info         Info                        │
│                                                                  │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  STEP 2: Employment Information                                  │
│                                                                  │
│  Employer Name:                                                  │
│  [________________________________]                              │
│                                                                  │
│  Position:                                                       │
│  [________________________________]                              │
│                                                                  │
│  Years Employed:                                                 │
│  [____]                                                          │
│                                                                  │
│                               [◄ Previous]  [Next ►]            │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

**Advanced Form Components:**

| Component | Purpose |
|-----------|---------|
| **Advanced Form** | Container for the wizard |
| **Advanced Form Steps** | Individual steps in sequence |
| **Step Type** | Load Form, Load Tab, Redirect, etc. |
| **Conditions** | Control step flow based on data |

### Forms vs Lists Comparison

| Feature | Basic Form | Advanced Form | List |
|---------|------------|---------------|------|
| Records | Single | Single | Multiple |
| Steps | One | Multiple | N/A |
| Create/Edit | Yes | Yes | Link to form |
| Branching | No | Yes | No |
| Progress | No | Yes | N/A |

---

## Authentication

### Authentication Options

Power Pages supports multiple identity providers:

```
┌─────────────────────────────────────────────────────────────────┐
│                 AUTHENTICATION OPTIONS                           │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  LOCAL AUTHENTICATION                                            │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │ • Email + Password                                       │    │
│  │ • User records in Dataverse (Contact table)             │    │
│  │ • Password reset, email verification                     │    │
│  └─────────────────────────────────────────────────────────┘    │
│                                                                  │
│  AZURE AD B2C                                                    │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │ • Enterprise identity management                         │    │
│  │ • Custom policies                                        │    │
│  │ • MFA, conditional access                                │    │
│  └─────────────────────────────────────────────────────────┘    │
│                                                                  │
│  SOCIAL PROVIDERS                                                │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │ • Microsoft Account                                      │    │
│  │ • Google                                                 │    │
│  │ • Facebook                                               │    │
│  │ • LinkedIn                                               │    │
│  │ • Twitter                                                │    │
│  └─────────────────────────────────────────────────────────┘    │
│                                                                  │
│  AZURE AD (Work/School)                                          │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │ • For partner portals                                    │    │
│  │ • Uses existing Azure AD accounts                        │    │
│  └─────────────────────────────────────────────────────────┘    │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Contact-Based Authentication

Portal users are linked to **Contact** records in Dataverse.

```
Portal Login                    Dataverse Contact
    │                               │
    │   User logs in with          │
    │   email/password             │
    │         │                    │
    │         ▼                    ▼
    │   ┌─────────────────────────────────────────┐
    │   │ Contact: John Smith                     │
    │   │ ├── Email: john@customer.com            │
    │   │ ├── Account: Contoso Ltd (Parent)       │
    │   │ └── Web Roles: [Customer, VIP]          │
    │   └─────────────────────────────────────────┘
    │                    │
    │                    ▼
    │           Security is determined by:
    │           • Contact's web roles
    │           • Contact's parent account
    │           • Table permissions (Contact/Account scope)
```

### Registration and Invitation

**Open Registration:**
- Anyone can create an account
- Email verification required
- Automatically gets default web role

**Invitation-Based:**
- Admin creates invitation
- Sends unique link to user
- User completes registration
- Can pre-assign web roles

---

## Advanced Features

### Site Settings

Configuration values stored in Dataverse.

| Setting | Purpose |
|---------|---------|
| `Authentication/Registration/Enabled` | Enable self-registration |
| `Authentication/Registration/RequiresConfirmation` | Require email verification |
| `Search/Enabled` | Enable site search |
| `Profile/ShowContactIdColumn` | Show contact ID in profile |

### Content Snippets

Reusable content blocks.

```
Content Snippet: "ContactUsEmail"
Value: support@company.com

Usage in page:
{% editable snippets 'ContactUsEmail' %}

Output: support@company.com
```

### Web Templates

Custom HTML/Liquid templates for complex layouts.

```liquid
{% Web Template: "CaseList" %}

<div class="case-list">
  {% fetchxml cases %}
  <fetch>
    <entity name="incident">
      <attribute name="title" />
      <attribute name="createdon" />
      <filter>
        <condition attribute="customerid" operator="eq" value="{{ user.id }}" />
      </filter>
    </entity>
  </fetch>
  {% endfetchxml %}

  {% for case in cases.results.entities %}
    <div class="case-card">
      <h3>{{ case.title }}</h3>
      <p>Created: {{ case.createdon | date: "%m/%d/%Y" }}</p>
    </div>
  {% endfor %}
</div>
```

---

## Common Exam Scenarios

### Scenario 1: Auto-Assign Web Role
**Question:** "All logged-in users should automatically have access to the Knowledge Base section."

**Answer:**
1. Create a Web Role (e.g., "Authenticated User")
2. Set **Authenticated Users Role = Yes**
3. Add Page Permission for Knowledge Base to this role
4. Add Table Permission for KB Articles (Global scope, Read)

### Scenario 2: User's Own Records
**Question:** "Customers should only see support cases they created."

**Answer:**
1. Create Table Permission for Cases
2. Set **Scope = Contact**
3. Grant Read privilege
4. Ensure Cases table has Contact lookup filled when created

### Scenario 3: Company-Wide Access
**Question:** "All employees from the same company should see all company orders."

**Answer:**
1. Create Table Permission for Orders
2. Set **Scope = Account**
3. Ensure users' Contacts are linked to their Account
4. Ensure Orders have Account lookup filled

### Scenario 4: Related Child Records
**Question:** "Users can view Order Lines for Orders they have access to."

**Answer:**
1. First, create parent Table Permission for Orders (Contact or Account scope)
2. Create child Table Permission for Order Lines
3. Set **Scope = Parent**
4. Select Orders as the parent relationship

### Scenario 5: Profile Page
**Question:** "Users should be able to edit their own contact information."

**Answer:**
1. Create Table Permission for Contact table
2. Set **Scope = Self**
3. Grant Read and Update privileges

### Scenario 6: Multi-Step Application
**Question:** "Users need to complete a 4-step application process with conditional routing."

**Answer:** Use **Advanced Form** (not Basic Form)
- Create Advanced Form with 4 steps
- Configure step conditions for routing
- Use different form tabs for each step

---

## Key Takeaways for the Exam

1. **Web Roles are central** - all security flows through web roles
2. **Authenticated Users Role** auto-assigns to logged-in users
3. **Table Permission Scopes**: Global, Contact, Account, Parent, Self
4. **Contact scope** = user's own records
5. **Account scope** = company-wide records
6. **Parent scope** = child records of accessible parent
7. **Basic Forms** = single step, **Advanced Forms** = multi-step
8. **Lists** use Dataverse Views for columns/filtering
9. **Liquid** is the template language for dynamic content
10. **Portal users are Contacts** in Dataverse

---

*Continue to the next section: [Business Process Flows Deep Dive](./PL-200-BPF-DeepDive.md)*
