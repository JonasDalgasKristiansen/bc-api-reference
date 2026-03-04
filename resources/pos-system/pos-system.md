# POS System with Business Central Sync

A full web-based Point of Sale system that mirrors Microsoft Business Central data locally and works completely offline. Sales are stored locally and exported to BC as Sales Orders when connectivity is available.

## Architecture

```
┌─────────────────────┐      ┌──────────────────┐      ┌────────────────────┐
│   React Frontend    │◄────►│  Express Backend  │◄────►│ Business Central   │
│   (POS Terminal)    │      │  (SQLite DB)      │      │ (REST API v2.0)    │
│                     │      │                   │      │                    │
│  - Product grid     │      │  - Local mirror   │      │  - Items           │
│  - Cart / checkout  │      │  - Offline sales  │      │  - Customers       │
│  - Customer search  │      │  - Sync engine    │      │  - Sales Orders    │
│  - Receipt view     │      │  - Cron scheduler │      │  - Tax / Payments  │
│  - Admin dashboard  │      │  - Export queue    │      │  - Inventory       │
└─────────────────────┘      └──────────────────┘      └────────────────────┘
```

## Key Features

- **Offline-first**: POS terminal never calls BC during checkout
- **Local mirror**: All BC data (items, customers, taxes) stored in SQLite
- **Scheduled sync**: Automatic import from BC every 15 minutes (configurable)
- **Export to BC**: One-click export of unsynced sales as Sales Orders
- **Idempotent sync**: Uses `externalDocumentNumber` to prevent duplicate exports
- **Safe ID resolution**: Export always resolves BC customer numbers and item GUIDs from the local mirror — never sends local DB UUIDs to BC
- **Barcode scanning**: Quick product lookup by GTIN barcode
- **Admin dashboard**: Connection status, sync logs, force sync buttons

## Quick Start

### 1. Configure BC credentials

```bash
cd pos-system/backend
cp .env.example .env
# Edit .env with your Azure/BC credentials
```

### 2. Install dependencies

```bash
# Backend
cd pos-system/backend
npm install

# Frontend
cd ../frontend
npm install
```

### 3. Initialize database

```bash
cd pos-system/backend

# Create tables
npm run migrate

# (Optional) Load demo data for testing
npm run seed
```

### 4. Start the app

```bash
# Terminal 1: Backend
cd pos-system/backend
npm run dev

# Terminal 2: Frontend
cd pos-system/frontend
npm run dev
```

The POS terminal will be available at `http://localhost:5173`

### 5. Import from Business Central

1. Go to the **Admin** tab
2. Click **Test Connection** to verify BC credentials
3. Click **Import All from BC** to pull products, customers, and tax data
4. Products will now appear in the POS terminal

### 6. Export sales to Business Central

1. Make sales in the POS terminal (they're saved locally with `sync_status: pending`)
2. Go to the **Admin** tab
3. Click **Export to BC** to create Sales Orders in Business Central
4. Check the **Sales** tab to verify sync status per transaction

## Project Structure

```
pos-system/
├── backend/
│   ├── data/                  # SQLite database (created at runtime)
│   ├── src/
│   │   ├── config/
│   │   │   └── database.js    # SQLite connection
│   │   ├── models/
│   │   │   ├── migrate.js     # Database schema
│   │   │   └── seed.js        # Demo data
│   │   ├── routes/
│   │   │   ├── products.js    # Product API (local DB)
│   │   │   ├── customers.js   # Customer API (local DB)
│   │   │   ├── sales.js       # POS transactions
│   │   │   └── sync.js        # BC import/export
│   │   ├── services/
│   │   │   ├── bcClient.js    # BC OAuth2 + API client
│   │   │   └── syncService.js # Import/export logic
│   │   └── server.js          # Express app entry
│   ├── .env.example
│   └── package.json
├── frontend/
│   ├── src/
│   │   ├── services/
│   │   │   └── api.js         # Backend API client
│   │   ├── App.jsx            # Full POS UI
│   │   └── main.jsx           # React entry
│   ├── index.html
│   ├── vite.config.js
│   └── package.json
└── README.md
```

## API Endpoints

### Products (offline, local DB)
- `GET /api/products` - List products (search, filter, paginate)
- `GET /api/products/barcode/:code` - Barcode lookup
- `GET /api/products/categories/list` - Category list

### Customers (offline, local DB)
- `GET /api/customers` - List customers (search, paginate)

### Sales (offline, local DB)
- `POST /api/sales` - Create sale (checkout)
- `GET /api/sales` - List sales (filter by status/sync)
- `GET /api/sales/:id` - Sale detail with lines
- `POST /api/sales/:id/void` - Void a sale
- `GET /api/sales/stats/today` - Today's summary

### Sync (BC connection required)
- `POST /api/sync/import/all` - Full BC import
- `POST /api/sync/import/items` - Import items only
- `POST /api/sync/import/customers` - Import customers only
- `POST /api/sync/export/all` - Export all pending sales to BC
- `POST /api/sync/export/:saleId` - Export single sale
- `GET /api/sync/status` - Dashboard status
- `GET /api/sync/logs` - Sync history
- `POST /api/sync/test-connection` - Test BC connection

## Checkout: Cash Payment & Receipt

When the cashier clicks **"Paid in Cash"**, the sale is finalised and an on-screen receipt is shown immediately. The receipt must include:

- Store name, sale number, and date/time
- Customer name (or "Walk-in Customer" if no customer selected)
- Itemised lines: product name, quantity, unit price, line total
- Subtotal, tax amount, total
- Amount tendered and change due
- A **Print** button (triggers `window.print()` on the receipt element)

The receipt modal should be dismissible and also accessible afterwards from the sales history view — every completed sale in the sales list must have a **"View Receipt"** button that reopens the same receipt modal.

**No BC call is made at this point.** The sale is written to the local database with `sync_status: pending` and exported later via the Admin dashboard.

### Viewing the invoice after export

Once a sale has been exported to BC and a Posted Sales Invoice exists (`bc_posted_invoice_number` is set), the sales history view must show a **"View Invoice"** button alongside the receipt. Clicking it opens a read-only invoice summary modal showing:

- BC invoice number and date
- Customer name and email
- Same itemised lines as the receipt
- Total amount and tax
- A note that the invoice has been sent to BC and (if applicable) emailed to the customer

This gives the cashier confirmation that the full export + invoice flow completed successfully.

---

## Checkout: Customer Selection & Creation

When the cashier presses **"Checkout"**, a step-by-step wizard opens (modal or full-screen overlay) before the sale is finalised. The wizard has two steps:

**Step 1 — Customer**
**Step 2 — Payment**

The cashier cannot skip to payment without passing through the customer step first. A "Skip" or "Walk-in" option must be clearly available on Step 1 for anonymous sales.

The checkout flow should include:

### Selecting an existing customer
- A search field that filters the local `customers` table by name, email, or phone (no BC call)
- Results shown as a scrollable list below the search field — the list must have a fixed max height with overflow scroll so it works with large customer databases
- The currently selected customer is highlighted in the list
- Walk-in sales (no customer) must always be allowed — selecting a customer is optional

### Creating a new customer inline
- A **"+ New Customer"** button opens a modal/drawer without leaving the checkout
- Required field: **Name** (`displayName`)
- Optional fields: Email, Phone, Address Line 1, City, Country, Postal Code
- On save:
  1. `POST /customers` to Business Central with the entered data (see `resources/customers/customers.md` — `displayName` is the only required field)
  2. Write the BC response (including the assigned BC `id` and `number`) into the local `customers` table immediately
  3. Auto-select the new customer on the current sale so checkout can proceed
- If BC is offline when the cashier tries to create a customer, show a clear error and allow the sale to continue as walk-in

### Data stored on the sale
Both `customer_id` (BC GUID) and `customer_number` (e.g. `"10000"`) must be saved on the sale record so the export can resolve the correct BC identifier later. See **Known Export Pitfalls** below.

---

## Export Flow: Sales Invoice → Post → Send

When the cashier clicks "Export to BC", each pending sale goes through these steps automatically. **Use `salesInvoices` directly — not `salesOrders` + `shipAndInvoice`.** The Sales Order approach has proven unreliable with the BC API actions; the invoice approach is simpler and more predictable.

**1. Idempotency check** — before creating anything, query BC to see if an invoice already exists for this sale:
```
GET /salesInvoices?$filter=externalDocumentNumber eq '{saleNumber}'
```
If a result is found (any status), skip creation entirely. This prevents duplicates on retry. Also check for already-posted invoices if your platform uses a separate `postedSalesInvoices` endpoint.

**2. Create draft Sales Invoice** — `POST /salesInvoices`
Use the BC customer number (resolved from local mirror) and the POS sale number as `externalDocumentNumber`.

**3. Add invoice lines** — `POST /salesInvoices({id})/salesInvoiceLines`
One line per item, identified by BC item GUID (`itemId`). BC auto-fills the item number and description from its own item master. Only works while the invoice is in `Draft` status.

**4. Post and Send** — `POST /salesInvoices({id})/Microsoft.NAV.postAndSend`
Posts the invoice and emails it to the customer in one step. Returns `204 No Content`. If the customer has no email, use `Microsoft.NAV.post` instead to post without sending. See `resources/sales/sales-invoices.md` for both actions.

**5. Post-and-verify** — BC sometimes returns an error response even when the invoice actually posted successfully. After any failure on step 4, always query BC to check the real state before marking the export as failed:
```
GET /salesInvoices?$filter=externalDocumentNumber eq '{saleNumber}'
```
If the invoice exists and status is not `Draft`, it was posted successfully — mark the sale as synced regardless of the error response.

**6. Retry** — the export must pick up both `pending` and `failed` status transactions so failed exports are automatically retried on the next export run.

**Result in BC:** A Posted Sales Invoice in status `Open`, emailed to the customer — all from a single button click on the POS.

The local `sales` table stores `bc_posted_invoice_id` and `bc_posted_invoice_number` for full traceability.

---

## Known Export Pitfalls

**1. Wrong customer identifier**
`customerNumber` must be the BC customer number (e.g. `"10000"`), not a local UUID. Always look it up from the local `customers` table using `customer_id` before creating the invoice.

**2. Wrong item identifier on invoice lines**
Lines must use `itemId` set to the BC item GUID — never a local database UUID. BC uses the GUID to auto-fill the item number and description. A local UUID produces a line with no item.

**3. Company name in URLs breaks with special characters**
Never embed the company name directly in API URL paths. Names like `CRONUS Danmark A/S` contain `/` which breaks the URL path even after `encodeURIComponent` — proxies decode `%2F` back to `/`. Always resolve the company GUID first via `GET /companies?$filter=name eq '...'` and use the GUID in all subsequent calls.

**4. BC returns errors even when posting succeeds**
The `postAndSend` action sometimes returns an error response even though the invoice was actually posted. Always query `GET /salesInvoices?$filter=externalDocumentNumber eq '{saleNumber}'` after any failure to check the real state before marking the export as failed.

**5. Duplicate exports on retry**
Always check for an existing invoice by `externalDocumentNumber` before creating a new one. Check both draft and posted invoices. If one exists, skip creation entirely.

**6. Non-JSON error responses from BC**
BC occasionally returns HTML error pages instead of JSON (e.g. during outages or proxy errors). Your `bcFetch()` wrapper must check `Content-Type` before calling `.json()` — otherwise the JSON parse error silently swallows the real error message.

**7. Failed exports not retried**
The export queue must pick up both `pending` and `failed` status transactions. If only `pending` is queried, failed exports are never retried.

## BC API Reference

This project uses the Business Central REST API v2.0 with OAuth2 client credentials.
See the [bc-api-reference](../) repository for full endpoint documentation.
