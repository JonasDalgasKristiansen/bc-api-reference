# BC API Reference — Why This Repo Exists

### What the POS System Does

- **Works fully offline** — the cash register never calls BC during checkout
- **Mirrors all BC data locally** — items, customers, prices, tax groups, inventory
- **Product search** — fast lookup from local database
- **Two-step checkout wizard** — Step 1: select customer (search local DB, create walk-in in BC, or continue anonymous). Step 2: payment
- **On-screen receipt** — shown after every sale, with a print button
- **Export to BC** — one click exports all unsynced sales as Sales Invoices, posts them, and emails the customer
- **Idempotent sync** — uses `externalDocumentNumber` (sale number like `POS-0001`) to prevent duplicates if you click export twice
- **Admin dashboard** — BC connection status, last sync time, error logs, force sync button

---

### The Prompt We Used

```
Full POS System with Business Central Sync

Build a full web-based POS system that syncs with Microsoft Business Central.

The app must:
- Import all products, prices, customers, taxes, and inventory
  from Business Central
- Store all imported data locally in the backend
- Work fully offline — no live BC calls during checkout
- Save all sales transactions in the backend database
- Include an "Export to Business Central" button to sync unsynced
  sales once BC is online again

Requirements:
- Modern POS UI (touch-friendly, barcode scan, product search,
  cart, payments, receipts)
- Backend with local database (store BC mirror + offline sales)
- Scheduled sync from BC → local database
- Track sync status per transaction
- Export unsynced sales as Sales Invoices — post and email
  automatically
- Prevent duplicate exports (idempotent sync logic using
  externalDocumentNumber)
- Cash payment button at checkout with on-screen receipt
- Admin dashboard with: BC connection status, last sync timestamp,
  error logs, force sync button

Important:
- Read AI_INSTRUCTIONS.md first — follow all rules
- Read resources/pos-system/pos-system.md for exact BC queries
  and architecture
- Credentials stored as Lovable Secrets

API Reference:
github.com/JonasDalgasKristiansen/bc-api-reference
```

---

## What Is This?

A complete, structured API reference for **Microsoft Dynamics 365 Business Central** — written specifically so AI coding tools can read it and generate working code.

It covers **80+ endpoints** across 15 categories: customers, items, sales, purchase, finance, employees, projects, time tracking, and more. Every endpoint includes field definitions with data types, full HTTP examples, JSON payloads, OData query patterns, and known errors.

**It is not a library. It is not a framework. It is documentation — structured for machines.**

---

## The Problem

Lovable can build full web applications in minutes. But it has **no idea** how the Business Central API works.

If you just type *"Build me an offline POS system connected to Business Central"* — this is what happens:

---

### What Goes Wrong Without This Repo

**1. It builds a fake app**

Lovable has no BC knowledge, so it generates a demo with hardcoded products, fake customers, and placeholder API calls that don't connect to anything. It looks great — but it's not real.

**2. It guesses field names — and guesses wrong**

The item name field in BC is `displayName`. Lovable will use `description` — because that's what most APIs call it. The result: **400 Bad Request** on every item import.

```
// What Lovable writes:
$select=id,number,description,unitPrice

// What BC actually needs:
$select=id,number,displayName,unitPrice
```

There is no `description` field on BC items. You find out after 30 minutes of debugging.

**3. It includes fields that break the API**

Lovable will try to fetch `balance`, `overdueAmount`, and `totalSalesExcludingTax` on customers — because those sound useful. In most BC environments, these computed fields cause a **400 error** when included in `$select`.

```
// What Lovable writes:
$select=id,number,displayName,email,balance,overdueAmount

// What actually works:
$select=id,number,displayName,email,phoneNumber,addressLine1,city,postalCode
```

**4. It gets authentication wrong**

BC uses OAuth2 Client Credentials — not API keys, not basic auth. Lovable doesn't know this. It'll either skip auth entirely, use the wrong flow, or hardcode credentials in the frontend.

**5. It puts the company name in the URL path — which breaks**

Company names like `CRONUS Danmark A/S` contain spaces and `/`. Lovable will embed them in the URL. HTTP proxies decode `%2F` back to `/`, splitting the path. The call fails silently or returns a confusing 404.

The correct pattern is to **resolve the company GUID first** and use the GUID in all subsequent calls.

**6. It sends local database IDs to BC**

When exporting sales, Lovable will use the local UUID for customer and item references. BC doesn't know those IDs — it needs its own GUIDs. Every export call fails.

---

### Why You Can't Just Write a Better Prompt

You might think: *"Maybe I just need to be more specific in my prompt."*

But you'd need to tell Lovable:

- The exact OAuth2 token endpoint and scope
- Which fields exist on every entity and their exact names
- Which fields look valid but cause 400 errors
- That `displayName` is the item name, not `description` or `name`
- How to resolve the company GUID before making any other call
- How to use `$filter`, `$select`, `$expand` in OData v4 format
- That posted invoices require a bound action (`Microsoft.NAV.post`)
- That `externalDocumentNumber` has a 35-character limit
- That you need to resolve `itemId` (BC GUID) from a local mirror, not send your database UUID

That's not a prompt — that's writing an entire API reference. Which is exactly what we did.

---

## What's In This Repo

```
bc-api-reference/
│
├── README.md                    # Quick-start for end users (setup + example prompts)
├── README_DOKUK.md              # This file — explains the repo and why it matters
├── AI_INSTRUCTIONS.md           # Rules the AI MUST follow (auth, field names, patterns)
├── HOW_TO_USE_WITH_AI.md        # Detailed prompting guide for Lovable, Bolt, Cursor, Copilot
├── DEVELOPER_GUIDE.md           # Technical reference for developers (OAuth2, OData, pagination)
│
└── resources/                   # The actual API documentation
    ├── _overview.md             # Master table of ALL 80+ endpoints + OData cheat sheet
    │
    ├── customers/               # Customers, payment journals
    ├── vendors/                 # Vendors, payment journals
    ├── contacts/                # Contacts, salespersons/purchasers
    ├── items/                   # Items, variants, categories, inventory, locations
    ├── sales/                   # Quotes, orders, invoices, credit memos, shipments
    ├── purchase/                # Purchase orders, invoices, credit memos, receipts
    ├── finance/                 # Chart of accounts, bank accounts, journals, G/L entries
    ├── employees/               # Employees
    ├── projects/                # Projects (jobs), job tasks
    ├── time-tracking/           # Time registration entries
    ├── fixed-assets/            # Fixed assets
    ├── documents/               # Attachments, PDF documents
    ├── dimensions/              # Dimensions, dimension set lines
    ├── setup/                   # Currencies, payment terms, units, countries, tax
    ├── company/                 # Company information
    ├── automation/              # Automation API (users, extensions, environments)
    ├── webhooks/                # Webhook subscriptions
    └── pos-system/              # Complete POS system guide (offline-first, sync, export)
```

Each resource file contains everything the AI needs for that endpoint — field names with types, full HTTP request/response examples, OData patterns, and common errors.

---

## How It Works With Lovable

1. Connect this GitHub repo to Lovable as a knowledge source
2. Add your 5 BC credentials as Lovable Secrets
3. Write a prompt describing what you want
4. Point Lovable at `AI_INSTRUCTIONS.md` and the relevant resource files

Lovable reads the documentation, follows the rules, and generates code that works against the real BC API — on the first try.

---

### Architecture (Lovable + Supabase)

```
┌─────────────────────┐      ┌───────────────────────────┐      ┌────────────────────┐
│   React Frontend    │◄────►│   Supabase                │◄────►│ Business Central   │
│   (Lovable-hosted)  │      │                           │      │ (REST API v2.0)    │
│                     │      │   PostgreSQL database:    │      │                    │
│  - Product grid     │      │    - items mirror         │      │  - Items           │
│  - Cart / checkout  │      │    - customers mirror     │      │  - Customers       │
│  - Customer search  │      │    - sales + sale_lines   │      │  - Sales Invoices  │
│  - Receipt view     │      │    - sync_logs            │      │  - Tax Groups      │
│  - Admin dashboard  │      │                           │      │  - Inventory       │
│                     │      │   Edge Functions (Deno):  │      │                    │
│                     │      │    - bc-import (sync)     │      │                    │
│                     │      │    - bc-export (invoices) │      │                    │
│                     │      │    - bc-auth (OAuth2)     │      │                    │
└─────────────────────┘      └───────────────────────────┘      └────────────────────┘
```

**Frontend** — React app hosted by Lovable. Touch-friendly POS UI with product grid, barcode scanning, cart, and checkout wizard.

**Backend** — Supabase PostgreSQL stores a local mirror of all BC data (items, customers, tax) plus all POS sales. Supabase Edge Functions handle OAuth2 authentication and all BC API calls server-side — credentials never touch the browser.

**Business Central** — The source of truth. Products, customers, and prices are imported from BC into the local database. Sales are exported back as posted invoices.

---

### Why It Actually Works

The AI built a working POS system because the repo told it exactly:

**1. The correct field names**
`displayName` on items, not `description`. Without this, every item import returns a 400 error.

**2. Which fields to NOT use**
`balance`, `overdueAmount`, `totalSalesExcludingTax` on customers crash most BC environments. The repo says: don't include them.

**3. The exact import queries**
Pre-built `$select` parameters that are tested and verified — so the AI doesn't have to guess.

**4. How to export sales to BC**
Step-by-step: create a draft Sales Invoice → add invoice lines with `itemId` (BC GUID, not local UUID) → POST the bound action `Microsoft.NAV.post` → call `Microsoft.NAV.send` to email.

**5. How to handle company lookup**
Resolve the company GUID first via `GET /companies?$filter=name eq '...'`, then use the GUID everywhere. Never embed the company name in URL paths.

**6. How to prevent duplicate exports**
Use `externalDocumentNumber` set to the sale number (`POS-0001`). BC enforces uniqueness — if you export twice, the second call is rejected instead of creating a duplicate invoice.

---

## The Point

Lovable is powerful — but it doesn't know Business Central.

Without this repo, it guesses. It gets field names wrong, authentication wrong, and URL patterns wrong. You spend hours debugging things that look right but aren't.

With this repo, it reads verified documentation and generates correct code from the start.

**This repo is the bridge between Business Central and AI coding tools.**

---

## Demo: A Full POS System — Built With One Prompt

To show what this repo makes possible, we built a **complete offline-first Point of Sale system** with Business Central sync. No manual coding. Just a prompt to Lovable with this repo connected.
