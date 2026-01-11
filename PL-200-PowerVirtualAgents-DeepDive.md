# Power Virtual Agents - Complete Deep Dive
## PL-200 Exam Preparation

---

## Table of Contents
1. [What are Power Virtual Agents?](#what-are-power-virtual-agents)
2. [Chatbot Architecture](#chatbot-architecture)
3. [Topics Deep Dive](#topics-deep-dive)
4. [Trigger Phrases](#trigger-phrases)
5. [Entities](#entities)
6. [Question Nodes](#question-nodes)
7. [Message Nodes](#message-nodes)
8. [Conditions and Branching](#conditions-and-branching)
9. [Variables](#variables)
10. [Integration with Power Automate](#integration-with-power-automate)
11. [Integration with Power Apps](#integration-with-power-apps)
12. [Authentication](#authentication)
13. [Publishing and Channels](#publishing-and-channels)
14. [Analytics and Monitoring](#analytics-and-monitoring)
15. [Best Practices](#best-practices)
16. [Common Exam Scenarios](#common-exam-scenarios)

---

## What are Power Virtual Agents?

### The Chatbot Platform

Power Virtual Agents (PVA) enables creation of **AI-powered chatbots** without code:

```
┌─────────────────────────────────────────────────────────────────┐
│              POWER VIRTUAL AGENTS ARCHITECTURE                   │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  User Interface                                                 │
│  ┌───────────────────────────────────────────────────────────┐ │
│  │  Chat Widget (Website, Teams, Facebook, etc.)             │ │
│  └───────────────────────────┬───────────────────────────────┘ │
│                               │                                  │
│  Chatbot Engine                                                 │
│  ┌────────────────────────────┴──────────────────────────────┐ │
│  │  Natural Language Processing (AI)                         │ │
│  │    ├─ Intent recognition                                  │ │
│  │    ├─ Entity extraction                                   │ │
│  │    └─ Context management                                  │ │
│  └────────────────────────┬──────────────────────────────────┘ │
│                            │                                     │
│  Topics and Logic                                               │
│  ┌────────────────────────┴──────────────────────────────────┐ │
│  │  Topics (conversation flows)                              │ │
│  │    ├─ Trigger phrases                                     │ │
│  │    ├─ Question nodes                                      │ │
│  │    ├─ Message nodes                                       │ │
│  │    ├─ Conditions                                          │ │
│  │    └─ Actions (Power Automate, Shortcuts)                │ │
│  └────────────────────────┬──────────────────────────────────┘ │
│                            │                                     │
│  Integration Layer                                              │
│  ┌────────────────────────┴──────────────────────────────────┐ │
│  │  • Power Automate (flows)                                 │ │
│  │  • Dataverse (data)                                       │ │
│  │  • Authentication (Azure AD, OAuth)                       │ │
│  │  • Skills (external bots)                                 │ │
│  └───────────────────────────────────────────────────────────┘ │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Key Capabilities

| Feature | Description |
|---------|-------------|
| **No-Code Builder** | Visual authoring canvas |
| **AI-Powered** | Natural language understanding |
| **Multi-Channel** | Web, Teams, Facebook, etc. |
| **Power Automate Integration** | Call flows for actions |
| **Authentication** | Secure user identification |
| **Analytics** | Track bot performance |
| **Handoff** | Transfer to human agents |

### Use Cases

- **Customer Service**: Answer FAQs, resolve issues
- **HR Support**: Onboarding, policy questions
- **IT Helpdesk**: Password resets, ticket creation
- **Sales Support**: Product info, lead capture
- **Internal Tools**: Data lookup, form submissions

---

## Chatbot Architecture

### Bots and Topics

```
┌─────────────────────────────────────────────────────────────┐
│                    BOT STRUCTURE                             │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  Bot: Customer Service Bot                                  │
│  ┌──────────────────────────────────────────────────────┐  │
│  │                                                       │  │
│  │  System Topics (Pre-built):                          │  │
│  │    ├─ Greeting                                       │  │
│  │    ├─ Goodbye                                        │  │
│  │    ├─ Escalate                                       │  │
│  │    ├─ Start over                                     │  │
│  │    └─ Fallback (when not understood)                │  │
│  │                                                       │  │
│  │  Custom Topics (You create):                         │  │
│  │    ├─ Store Hours                                    │  │
│  │    ├─ Return Policy                                  │  │
│  │    ├─ Order Status                                   │  │
│  │    ├─ Product Information                            │  │
│  │    └─ Submit Feedback                                │  │
│  │                                                       │  │
│  │  Entities:                                            │  │
│  │    ├─ Pre-built (City, Date, Number)                │  │
│  │    └─ Custom (Product Name, Order ID)               │  │
│  │                                                       │  │
│  │  Variables:                                           │  │
│  │    └─ Global bot variables                           │  │
│  │                                                       │  │
│  └──────────────────────────────────────────────────────┘  │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### System Topics

Pre-built topics you can customize:

| System Topic | Purpose | When Triggered |
|--------------|---------|----------------|
| **Greeting** | Welcome user | Conversation starts |
| **Goodbye** | End conversation | User says goodbye |
| **Escalate** | Transfer to human | User requests human |
| **Start over** | Reset conversation | User wants to restart |
| **Fallback** | Handle unknown input | Bot doesn't understand |
| **Thank you** | Acknowledge thanks | User thanks bot |
| **Confirm success** | Verify completion | Action completes |
| **Confirm failure** | Handle errors | Action fails |

> **Exam Tip**: System topics are PRE-BUILT and can be enabled/disabled or customized. Custom topics are created from scratch. This distinction is commonly tested.

---

## Topics Deep Dive

### What is a Topic?

A **topic** is a conversation flow about a specific subject:

```
┌─────────────────────────────────────────────────────────────┐
│                    TOPIC STRUCTURE                           │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  Topic: Store Hours                                         │
│  ┌──────────────────────────────────────────────────────┐  │
│  │                                                       │  │
│  │  Trigger Phrases:                                    │  │
│  │    • "What are your store hours?"                    │  │
│  │    • "When are you open?"                            │  │
│  │    • "Are you open on weekends?"                     │  │
│  │                                                       │  │
│  │  ┌─────────────────────────────────────────────────┐│  │
│  │  │ Trigger                                          ││  │
│  │  └───────────────┬─────────────────────────────────┘│  │
│  │                  │                                   │  │
│  │  ┌───────────────┴─────────────────────────────────┐│  │
│  │  │ Message: "Our store hours are..."              ││  │
│  │  └───────────────┬─────────────────────────────────┘│  │
│  │                  │                                   │  │
│  │  ┌───────────────┴─────────────────────────────────┐│  │
│  │  │ Question: "Would you like to know our location?"││  │
│  │  └───────────────┬─────────────────────────────────┘│  │
│  │                  │                                   │  │
│  │           ┌──────┴──────┐                           │  │
│  │           │             │                           │  │
│  │      ┌────┴────┐   ┌────┴────┐                     │  │
│  │      │   Yes   │   │   No    │                     │  │
│  │      └─────────┘   └─────────┘                     │  │
│  │                                                       │  │
│  └──────────────────────────────────────────────────────┘  │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Creating Topics

**Steps:**
1. **Topics** tab → **New topic**
2. **Name** the topic
3. **Add trigger phrases** (5-10 recommended)
4. **Build conversation flow** with nodes
5. **Save**
6. **Test** in test canvas

### Topic Properties

| Property | Purpose |
|----------|---------|
| **Name** | Topic identifier |
| **Display name** | User-facing name |
| **Description** | Purpose documentation |
| **Trigger** | Phrases that activate topic |
| **Status** | On/Off |

---

## Trigger Phrases

### Understanding Triggers

**Trigger phrases** are examples of what users might say to activate a topic:

```
Topic: Password Reset

Trigger Phrases:
  • "I forgot my password"
  • "Reset my password"
  • "Can't log in"
  • "Password help"
  • "Locked out of account"
  • "Need new password"
```

### Best Practices for Trigger Phrases

1. **Add 5-10 variations** per topic
2. **Use natural language** (how users actually talk)
3. **Include synonyms** and variations
4. **Different phrasings** of same intent
5. **Common misspellings** (optional)

### AI Recognition

The AI engine:
- Analyzes trigger phrases
- Learns from conversations
- Recognizes similar inputs
- Suggests new phrases based on usage

**Example:**
```
Trigger: "What are your hours?"

AI also recognizes:
  • "When do you open?"
  • "Are you open today?"
  • "What time do you close?"
```

> **Exam Tip**: You DON'T need to list every possible variation. AI learns and generalizes from examples. 5-10 good examples are sufficient.

### Suggested Topics

Power Virtual Agents can analyze:
- Website content
- Support documents
- Existing conversations

And **suggest topics** to create:
1. **Suggest topics** → Select sources
2. AI analyzes content
3. Suggests topic names and trigger phrases
4. You review and add to bot

---

## Entities

### What are Entities?

**Entities** extract specific information from user input:

```
User says: "I need to return order 12345"

Bot extracts:
  • Action: return
  • Order ID: 12345
```

### Pre-built Entities

| Entity | Extracts | Example |
|--------|----------|---------|
| **Age** | Numeric age | "I am 25 years old" → 25 |
| **Boolean** | Yes/no | "yes" → true |
| **City** | City names | "Seattle" → Seattle |
| **Color** | Color names | "blue shirt" → blue |
| **Date and time** | Dates/times | "tomorrow at 3pm" → 2026-01-12 15:00 |
| **Duration** | Time periods | "2 weeks" → P14D |
| **Email** | Email addresses | "john@contoso.com" |
| **Language** | Languages | "Spanish" → es |
| **Money** | Currency amounts | "$100" → 100 USD |
| **Number** | Numbers | "five" → 5 |
| **Ordinal** | Position | "first" → 1 |
| **Percentage** | Percentages | "50%" → 0.5 |
| **Phone number** | Phone numbers | "(555) 123-4567" |
| **Speed** | Velocity | "60 mph" → 60 mph |
| **State** | US states | "Washington" → WA |
| **Temperature** | Temperatures | "72F" → 72°F |
| **URL** | Web addresses | "www.contoso.com" |
| **Zip code** | ZIP codes | "98052" |

### Custom Entities

Create entities for business-specific data:

**Types:**

#### 1. Closed List

Pre-defined list of values:

```
Entity: Product Category

Values:
  • Laptops
  • Desktops
  • Tablets
  • Accessories

With synonyms:
  • Laptops: laptop, notebook, portable computer
  • Desktops: desktop, tower, workstation
```

**Use Case:** Fixed set of options (departments, products, statuses)

#### 2. Regular Expression (Regex)

Pattern matching:

```
Entity: Order ID

Pattern: ORD-\d{5}

Matches:
  • ORD-12345
  • ORD-98765
```

**Use Case:** Formatted data (IDs, codes, license plates)

#### 3. Smart Matching

AI learns from examples:

```
Entity: Product Name

Examples:
  • Surface Laptop
  • Surface Pro
  • Surface Book

AI learns and recognizes:
  • New product names
  • Variations and misspellings
```

**Use Case:** Open-ended but specific domain (product names, locations)

> **Exam Tip**: Use **Closed List** for fixed options, **Regex** for patterns, **Smart Matching** for AI learning. Choose based on data characteristics.

---

## Question Nodes

### Asking Questions

**Question nodes** collect information from users:

```
┌─────────────────────────────────────────────────────────────┐
│                    QUESTION NODE                             │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  Question: "What is your order number?"                     │
│                                                              │
│  Identify: Order ID (custom entity - regex)                 │
│                                                              │
│  Save response as: Var_OrderNumber                          │
│                                                              │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  User input: "My order is ORD-12345"                 │  │
│  └──────────────────────────────────────────────────────┘  │
│                                                              │
│  Entity extracted: ORD-12345                                │
│  Variable set: Var_OrderNumber = "ORD-12345"               │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Question Types

| Entity Type | Input Control | Example |
|-------------|---------------|---------|
| **Multiple choice** | Buttons/list | Select from options |
| **User's entire response** | Free text | Any text input |
| **Pre-built entity** | Text field | Date, number, email |
| **Custom entity** | Text field | Product, order ID |

### Question Properties

| Property | Purpose |
|----------|---------|
| **Ask a question** | What bot asks |
| **Identify** | Entity to extract |
| **Save response as** | Variable name |
| **Reprompt** | Message if not understood |
| **Max retries** | Attempts before escalation |

### Multiple Choice Questions

Present buttons for selection:

```
Question: "What can I help you with?"

Options:
  • Store hours
  • Return policy
  • Order status
  • Talk to agent

Each option can:
  • Go to different topic
  • Set variable
  • Branch to condition
```

### Validation

Questions can validate input:
- **Numeric range**: "Must be between 1 and 100"
- **Date range**: "Must be within next 30 days"
- **Pattern**: "Must match order format"

**Reprompt on Failure:**
```
Invalid input → Reprompt with help
2nd invalid → Reprompt again
3rd invalid → Escalate or fallback
```

---

## Message Nodes

### Sending Messages

**Message nodes** display information to users:

```
Message: "Your order ORD-12345 was shipped on January 10, 2026."

Can include:
  • Plain text
  • Variables: {x} varOrderNumber
  • Line breaks
  • Formatting (bold, italic)
```

### Message Variations

Add variety to responses:

```
Message:
  • Variation 1: "Sure, I can help with that!"
  • Variation 2: "Happy to help!"
  • Variation 3: "Let me assist you with that."

Bot randomly selects one
```

**Benefits:**
- More natural conversation
- Less repetitive
- Better user experience

### Rich Content

Messages can include:

#### Text Formatting

```
**Bold text**
*Italic text*
[Link text](https://contoso.com)
```

#### Images

```
Insert image: URL or upload
Alt text for accessibility
```

#### Videos

```
Video URL (YouTube, Vimeo, etc.)
Embedded in chat
```

#### Cards

```
Adaptive Cards:
  • Title
  • Subtitle
  • Image
  • Buttons
  • Actions
```

#### Quick Replies

```
Message: "Would you like to continue?"

Quick replies:
  [Yes] [No] [Not sure]
```

---

## Conditions and Branching

### Condition Nodes

Branch based on logic:

```
┌─────────────────────────────────────────────────────────────┐
│                    CONDITION BRANCHING                       │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  Condition: Var_OrderTotal                                  │
│                                                              │
│           ┌──────┴──────┐                                   │
│           │             │                                   │
│      ┌────┴─────┐  ┌────┴─────┐                            │
│      │ > $100   │  │ ≤ $100   │                            │
│      └────┬─────┘  └────┬─────┘                            │
│           │             │                                   │
│    ┌──────┴──────┐ ┌────┴──────┐                           │
│    │ Free ship   │ │ Std ship  │                           │
│    └─────────────┘ └───────────┘                           │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Condition Types

**Comparison Operators:**

| Operator | Meaning | Example |
|----------|---------|---------|
| **is equal to** | Exact match | Status = "Active" |
| **is not equal to** | Not match | Status ≠ "Cancelled" |
| **is greater than** | Numeric > | Total > 100 |
| **is greater than or equal to** | Numeric ≥ | Quantity ≥ 5 |
| **is less than** | Numeric < | Price < 50 |
| **is less than or equal to** | Numeric ≤ | Stock ≤ 10 |
| **contains** | Text contains | Email contains "@" |
| **does not contain** | Text doesn't contain | Name !contains "test" |
| **starts with** | Text starts | OrderID starts "ORD" |
| **ends with** | Text ends | Email ends ".com" |
| **is blank** | Empty/null | Phone is blank |
| **is not blank** | Has value | Address is not blank |

### Multiple Conditions

Use **AND** / **OR** logic:

```
Condition:
  Var_OrderTotal > 100
  AND
  Var_CustomerTier = "Gold"

  → Yes: Apply 20% discount
  → No: Apply 10% discount
```

### All Other Conditions

Add "All other conditions" branch:
- Catches anything not matched
- Like "else" in programming
- Good for error handling

---

## Variables

### Understanding Variables

**Variables** store information during conversation:

```
┌─────────────────────────────────────────────────────────────┐
│                    VARIABLE TYPES                            │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  TOPIC VARIABLES                                            │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  • Scope: Current topic only                         │  │
│  │  • Created by: Question nodes                        │  │
│  │  • Lifetime: Until topic ends                        │  │
│  │  • Example: Var_OrderNumber                          │  │
│  └──────────────────────────────────────────────────────┘  │
│                                                              │
│  GLOBAL VARIABLES (Bot variables)                           │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  • Scope: Entire bot (all topics)                    │  │
│  │  • Created by: Manual creation                       │  │
│  │  │  • Lifetime: Entire conversation                   │  │
│  │  • Example: Global.UserID                            │  │
│  └──────────────────────────────────────────────────────┘  │
│                                                              │
│  SYSTEM VARIABLES                                           │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  • Scope: Built-in, always available                 │  │
│  │  • Examples:                                          │  │
│  │    - bot.UserId                                      │  │
│  │    - bot.UserDisplayName                             │  │
│  │    - conversation.Id                                 │  │
│  └──────────────────────────────────────────────────────┘  │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Topic Variables

Created automatically by question nodes:

```
Question: "What is your email?"
Identify: Email
Save response as: Var_Email

Creates topic variable: Var_Email
Available in current topic
```

**Usage:**
```
Message: "Thanks! We'll send confirmation to {x} Var_Email"
```

### Global Variables

Available across all topics:

**Creating Global Variables:**
1. **Variables** tab
2. **New variable**
3. Name (e.g., Global.CustomerTier)
4. Type (String, Number, Boolean, etc.)

**Setting Global Variables:**
- In **Set Variable Value** node
- From Power Automate flow output
- From authentication (user info)

**Use Cases:**
- User information (name, email, ID)
- Shopping cart data
- Session preferences
- Data from external systems

### System Variables

Pre-built variables:

| Variable | Contains |
|----------|----------|
| `bot.UserId` | User identifier |
| `bot.UserDisplayName` | User's name (if authenticated) |
| `bot.UserPrincipalName` | User's email (if authenticated) |
| `conversation.Id` | Unique conversation ID |
| `activity.text` | User's last message |
| `activity.channelId` | Channel (Teams, Web, etc.) |

**Authentication Required:**
- `bot.UserDisplayName`
- `bot.UserPrincipalName`
- Other user profile data

> **Exam Tip**: Topic variables are LOCAL (current topic). Global variables are PERSISTENT (all topics). System variables are READ-ONLY. This scoping is frequently tested.

---

## Integration with Power Automate

### Calling Flows from Chatbot

Power Automate extends bot capabilities:

```
┌─────────────────────────────────────────────────────────────┐
│              POWER AUTOMATE INTEGRATION                      │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  Chatbot Topic                                              │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  Collect: Order Number, Email                        │  │
│  │     ↓                                                 │  │
│  │  Call Power Automate Flow                            │  │
│  │     ├─ Input: OrderNumber, Email                     │  │
│  │     ↓                                                 │  │
│  └──────┼───────────────────────────────────────────────┘  │
│         │                                                   │
│  Power Automate Flow                                        │
│  ┌──────┴───────────────────────────────────────────────┐  │
│  │  Receive inputs from bot                             │  │
│  │     ↓                                                 │  │
│  │  Get order from Dataverse                            │  │
│  │     ↓                                                 │  │
│  │  Check status                                        │  │
│  │     ↓                                                 │  │
│  │  Return status to bot                                │  │
│  │     ├─ Output: Status, ShipDate, TrackingNumber      │  │
│  │     ↓                                                 │  │
│  └──────┼───────────────────────────────────────────────┘  │
│         │                                                   │
│  Chatbot Topic (continued)                                  │
│  ┌──────┴───────────────────────────────────────────────┐  │
│  │  Receive flow outputs                                │  │
│  │     ↓                                                 │  │
│  │  Message: "Your order {Status} was shipped {Date}"  │  │
│  └──────────────────────────────────────────────────────┘  │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Creating Flow for PVA

**Flow Requirements:**
1. Trigger: **Power Virtual Agents**
2. Define input parameters
3. Perform actions (query data, update records, etc.)
4. Return values to bot

**Example Flow:**

```
Trigger: Power Virtual Agents
  Inputs:
    - OrderNumber (text)
    - Email (text)
    ↓
Get row by ID (Dataverse)
  Table: Orders
  Row ID: OrderNumber
    ↓
Condition: If order found
  Yes:
    Return value(s) to Power Virtual Agents
      - Status (text)
      - ShipDate (date)
      - TrackingNumber (text)
  No:
    Return value(s) to Power Virtual Agents
      - Status: "Not Found"
```

### Calling Flow from Bot

**In topic:**
1. Add **Call an action** node
2. Select flow
3. Map inputs (variables → flow parameters)
4. Save outputs to variables
5. Use output variables in subsequent nodes

### Use Cases for Power Automate

- **Data Lookup**: Query Dataverse, SQL, SharePoint
- **Record Creation**: Create cases, leads, orders
- **Approvals**: Start approval workflows
- **Notifications**: Send emails, Teams messages
- **External APIs**: Call REST APIs
- **Complex Logic**: Multi-step processes

> **Exam Tip**: Power Automate is the PRIMARY way to access external data from chatbots. If scenario requires database access or complex logic, answer is Power Automate. This is very commonly tested.

---

## Integration with Power Apps

### Embedding Chatbot in Power Apps

Add chatbot to Canvas App:

**Steps:**
1. Canvas App → Insert → **Chatbot** (preview)
2. Select Power Virtual Agents bot
3. Configure:
   - Bot ID
   - Environment
   - Schema name

**Passing Context:**
```powerapps
// Pass variable to bot
Chatbot1.StartNewConversation({UserID: gblCurrentUser.ID})
```

### Launching Bot from Power Apps

Use button to open bot:

```powerapps
Button.OnSelect =
  Launch("https://[bot-url]", {
    UserId: User().Email,
    Context: "PowerApp"
  })
```

---

## Authentication

### User Authentication

Authenticate users to access secure data:

```
┌─────────────────────────────────────────────────────────────┐
│                  AUTHENTICATION FLOW                         │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  User starts conversation                                   │
│     ↓                                                        │
│  Bot: "Please sign in"                                      │
│     ↓                                                        │
│  Authentication dialog                                      │
│    (Azure AD, OAuth, etc.)                                  │
│     ↓                                                        │
│  User authenticates                                         │
│     ↓                                                        │
│  Bot receives:                                              │
│    • bot.UserId                                             │
│    • bot.UserDisplayName                                    │
│    • bot.UserPrincipalName                                  │
│    • Token (for API calls)                                  │
│     ↓                                                        │
│  Bot can now:                                               │
│    • Personalize messages                                   │
│    • Access user-specific data                              │
│    • Make authenticated API calls                           │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Authentication Providers

| Provider | Use Case |
|----------|----------|
| **Azure AD** | Microsoft 365 users |
| **Azure AD B2C** | External customers |
| **OAuth 2.0 Generic** | Custom providers |
| **Facebook** | Social login |
| **Google** | Social login |

### Configuring Authentication

**Steps:**
1. **Settings** → **Security** → **Authentication**
2. Choose provider
3. Configure:
   - Client ID
   - Client secret
   - Scopes
4. Test authentication
5. Save

### Requiring Authentication

**In topic:**
1. Add **Authenticate** node
2. User prompted to sign in
3. After authentication:
   - System variables populated
   - Token available for API calls
   - Flow can use token

### Using Authenticated Data

```
Message: "Hello {x} bot.UserDisplayName!"

Call Power Automate:
  - Pass: bot.UserPrincipalName
  - Flow uses to query user's data
```

> **Exam Tip**: Authentication enables personalization and secure data access. Use Azure AD for internal users, Azure AD B2C for external customers.

---

## Publishing and Channels

### Publishing Process

**Steps:**
1. **Publish** tab
2. Review changes
3. **Publish**
4. Wait for deployment (2-3 minutes)

**What Happens:**
- Bot deployed to channels
- Changes go live
- Previous version replaced

### Channels

Deploy bot to multiple channels:

| Channel | Description | Use Case |
|---------|-------------|----------|
| **Demo website** | Test web page | Testing, demos |
| **Custom website** | Your website | Customer-facing |
| **Microsoft Teams** | Teams integration | Internal users |
| **Facebook** | Facebook Messenger | Social customer service |
| **Mobile app** | Native mobile | Mobile-first experience |
| **Azure Bot Service** | Custom channels | Advanced scenarios |

### Custom Website Integration

**Embed in website:**

```html
<script src="https://cdn.botframework.com/botframework-webchat/latest/webchat.js"></script>

<div id="webchat" role="main"></div>

<script>
  window.WebChat.renderWebChat({
    directLine: window.WebChat.createDirectLine({
      token: 'YOUR_DIRECT_LINE_TOKEN'
    }),
    userID: 'USER_ID',
    username: 'USER_NAME'
  }, document.getElementById('webchat'));
</script>
```

**Customization:**
- Colors and styling
- Welcome message
- Button text
- Bot avatar

### Teams Integration

**Add to Teams:**
1. **Channels** → **Microsoft Teams**
2. **Turn on Teams**
3. **Open bot** in Teams
4. Share with organization
5. Users add bot or find in Teams

**Teams Features:**
- Proactive messages
- Adaptive cards
- Personal and team scope
- Integration with Teams data

---

## Analytics and Monitoring

### Bot Analytics

Track performance:

```
┌─────────────────────────────────────────────────────────────┐
│                      ANALYTICS OVERVIEW                      │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  Summary                                                    │
│    • Total sessions: 1,234                                  │
│    • Engagement rate: 85%                                   │
│    • Resolution rate: 70%                                   │
│    • Escalation rate: 10%                                   │
│    • Abandonment rate: 15%                                  │
│                                                              │
│  Sessions Over Time                                         │
│    [Chart showing daily sessions]                           │
│                                                              │
│  Top Topics                                                 │
│    1. Store Hours - 350 sessions                           │
│    2. Return Policy - 280 sessions                          │
│    3. Order Status - 210 sessions                           │
│                                                              │
│  Outcomes                                                   │
│    • Resolved: 70%                                          │
│    • Escalated: 10%                                         │
│    • Abandoned: 15%                                         │
│    • Unrecognized: 5%                                       │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Key Metrics

| Metric | Meaning | Good/Bad |
|--------|---------|----------|
| **Engagement rate** | % who interact beyond first message | High is good |
| **Resolution rate** | % resolved without escalation | High is good |
| **Escalation rate** | % transferred to human | Low is good |
| **Abandonment rate** | % who leave mid-conversation | Low is good |
| **CSAT** | Customer satisfaction score | High is good |

### Conversation Transcripts

Review actual conversations:
- **Analytics** → **Sessions**
- Filter by date, outcome, topic
- Click session to see transcript
- Identify issues and improvements

### Topic Performance

Track individual topics:
- **Analytics** → **Topic details**
- See trigger rates
- Resolution vs escalation
- User feedback
- Suggested improvements

> **Exam Tip**: Use analytics to identify topics that need improvement. High escalation or abandonment = problem. This data-driven approach is commonly tested.

---

## Best Practices

### Topic Design

1. **Keep topics focused** - One topic = one purpose
2. **Use clear trigger phrases** - 5-10 natural variations
3. **Limit conversation depth** - 3-5 exchanges ideal
4. **Provide escape routes** - Allow users to restart or escalate
5. **Test with real users** - Iterate based on feedback

### Message Design

1. **Be concise** - Short, clear messages
2. **Use friendly tone** - Conversational, not robotic
3. **Chunk information** - Multiple short messages > one long
4. **Add variations** - Avoid repetition
5. **Use formatting** - Bold, bullets for readability

### Entity Usage

1. **Use pre-built entities** when possible
2. **Custom entities** for business-specific data
3. **Add synonyms** to closed lists
4. **Test extraction** with real examples
5. **Provide help text** in reprompts

### Power Automate Integration

1. **Keep flows simple** - Single responsibility
2. **Handle errors** - Return error messages to bot
3. **Optimize performance** - Fast flows = better UX
4. **Pass only needed data** - Minimize inputs/outputs
5. **Test independently** - Debug flow before bot integration

### Testing

1. **Test canvas** - Quick testing during development
2. **Demo website** - Test full experience
3. **Real users** - Beta testing before launch
4. **Edge cases** - Test error scenarios
5. **Multiple channels** - Verify each channel works

### Maintenance

1. **Monitor analytics** - Weekly review
2. **Update trigger phrases** - Based on unrecognized inputs
3. **Improve low-performing topics** - High abandonment
4. **Add new topics** - Address common questions
5. **Keep content current** - Update information regularly

---

## Common Exam Scenarios

### Scenario 1: When to Use Power Automate

**Question**: "Chatbot needs to query Dataverse for order status. How?"

**Answer**: **Call Power Automate flow**
- Chatbots cannot directly query Dataverse
- Power Automate provides data access
- Flow receives order ID, queries Dataverse, returns status

### Scenario 2: Variable Scope

**Question**: "Variable set in Topic A is not available in Topic B. Why?"

**Answer**: **Topic variables are local**
- Topic variables only exist in that topic
- Use **Global variables** for cross-topic data
- Or pass data when redirecting to topic

### Scenario 3: User Not Recognized

**Question**: "Bot doesn't understand 80% of user inputs. What to do?"

**Answer**: **Add more trigger phrases**
- Review analytics for common inputs
- Add unrecognized phrases to trigger lists
- Use suggested topics feature
- Test with real user language

### Scenario 4: Authentication Required

**Question**: "Bot must access user's personal data from Dataverse."

**Answer**: **Configure authentication**
- Set up Azure AD authentication
- Add Authenticate node in topic
- Use bot.UserPrincipalName in Power Automate
- Flow queries user-specific data with token

### Scenario 5: Multiple Channels

**Question**: "Bot must work in Teams and on website."

**Answer**: **Publish to multiple channels**
- One bot, multiple channels
- Configure each channel in Channels tab
- Test each channel separately
- May need channel-specific features (Teams cards)

### Scenario 6: Fallback Behavior

**Question**: "What happens when bot doesn't understand user?"

**Answer**: **Fallback topic triggers**
- System Fallback topic activates
- Bot asks user to rephrase
- After retries, can escalate
- Customize Fallback topic for better experience

### Scenario 7: Escalation to Human

**Question**: "User requests human agent. How to handle?"

**Answer**: **Escalate system topic**
- System Escalate topic handles this
- Can integrate with Omnichannel for Customer Service
- Or provide contact information
- Log escalation reason for analysis

### Scenario 8: Entity Extraction Fails

**Question**: "Bot asks for email but doesn't recognize input."

**Answer**: **Check entity configuration**
- Verify correct entity type (Email pre-built)
- Check reprompt message
- Increase max retries
- Provide example format in question

### Scenario 9: Global vs Topic Variables

**Question**: "Need to track user's name throughout conversation across topics."

**Answer**: **Use Global variable**
- Create Global.UserName variable
- Set in first topic
- Available in all topics
- Topic variables would reset

### Scenario 10: Performance Issues

**Question**: "Bot is slow when calling Power Automate."

**Answer**: **Optimize flow**
- Reduce complexity
- Use Select columns in Dataverse queries
- Filter data server-side
- Check for loops or delays
- Use concurrency in Apply to each

---

## Key Takeaways for the Exam

1. **Topics are conversation flows** - One topic per purpose
2. **Trigger phrases need 5-10 variations** - AI learns from examples
3. **Entities extract information** - Pre-built, Closed list, Regex, Smart matching
4. **Variables have scope** - Topic (local), Global (all topics), System (read-only)
5. **Power Automate for data access** - Bots don't directly access Dataverse
6. **Authentication enables personalization** - Use for secure data access
7. **System topics are pre-built** - Greeting, Fallback, Escalate, etc.
8. **Analytics track performance** - Monitor engagement, resolution, escalation
9. **Multiple channels from one bot** - Teams, web, Facebook, etc.
10. **Fallback handles unknown input** - Customizable system topic
11. **Escalate transfers to human** - System topic or custom logic
12. **Question nodes create variables** - Automatically from user input
13. **Conditions enable branching** - Based on variable values
14. **Message variations reduce repetition** - More natural conversation
15. **Global variables persist** - Throughout entire conversation
16. **Adaptive cards for rich content** - Images, buttons, actions
17. **Test canvas for quick testing** - During development
18. **Publish deploys changes** - To all channels
19. **Closed list entities for fixed options** - Regex for patterns
20. **bot.UserDisplayName requires authentication** - System variable populated after auth

---

*Continue to the next section: [Integration Scenarios Deep Dive](./PL-200-Integration-DeepDive.md)*
