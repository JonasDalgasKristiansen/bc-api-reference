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

## Export Flow: Sales Order → Ship & Invoice → Email

When the cashier clicks "Export to BC", each pending sale goes through these steps automatically:

**1. Create Sales Order** — `POST /salesOrders`
The sale is posted to BC as a Sales Order using the BC customer number and the POS sale number as `externalDocumentNumber` (used for idempotency).

**2. Add order lines** — `POST /salesOrders({id})/salesOrderLines`
One line per item, identified by BC item GUID (`itemId`). BC auto-fills the item number and description from its own item master.

**3. Ship and Invoice** — `POST /salesOrders({id})/Microsoft.NAV.shipAndInvoice`
This single action ships all items and posts the invoice in one step. The Sales Order is consumed — BC creates a Sales Shipment and a Posted Sales Invoice automatically. Returns `204 No Content`.

**4. Find the Posted Invoice** — `GET /salesInvoices?$filter=externalDocumentNumber eq '{saleNumber}'`
Because `shipAndInvoice` returns no body, we query for the resulting Posted Sales Invoice by `externalDocumentNumber` to get its ID and number for local storage.

**5. Send invoice by email** — `POST /salesInvoices({id})/Microsoft.NAV.send`
If the customer has an email address on their BC record, BC sends the posted invoice automatically using its own SMTP setup and invoice template. Walk-in / cash sales with no customer are silently skipped. This step is non-fatal — if it fails, the sale remains marked as synced.

**Result in BC:** A Posted Sales Invoice in status `Open`, inventory adjusted, shipment recorded, and invoice emailed to the customer — all from a single button click on the POS.

The local `sales` table stores both `bc_sales_order_id` / `bc_sales_order_number` and `bc_posted_invoice_id` / `bc_posted_invoice_number` for full traceability.

---

## Known Export Pitfalls

Two bugs that commonly break the BC export — both fixed in `syncService.js`:

**1. Wrong customer identifier on Sales Order header**
The `customerNumber` sent to BC must be the BC customer number (e.g. `"10000"`), not a GUID or a stale cached value. Always look it up from the local `customers` table using `customer_id` (the BC GUID) before calling `bcPost('/salesOrders', ...)`.

**2. Wrong item identifier on Sales Order Lines**
Order lines must be created with `itemId` set to the BC item GUID — never a local database UUID. `items.id` in this schema stores the BC GUID directly. Always pass `itemId: line.item_id` (with a number-based fallback via the `items` table) so BC can auto-fill the item number and description. Sending an unrecognised UUID results in a line with no item.

**Pattern:** before any export, resolve BC identifiers (GUIDs / numbers) from the local mirror. Never trust IDs stored at transaction time.

## BC API Reference

This project uses the Business Central REST API v2.0 with OAuth2 client credentials.
See the [bc-api-reference](../) repository for full endpoint documentation.
