# How to Use This Repo with AI Vibe-Coding Tools

This repository is specifically structured so that AI coding tools — **Lovable**, **Bolt**, **Cursor**, **GitHub Copilot**, **Claude**, **ChatGPT**, and others — can read it as context and generate **fully working Business Central integrations** without needing any additional documentation.

Every resource file contains complete HTTP examples, realistic JSON payloads, all field definitions with data types, OData query patterns, and error handling guidance. The `.env.example` file defines every required secret / environment variable your app will need.

> **This is a public repo.** It contains NO credentials. All sensitive values must be added as **platform secrets** (Lovable, Bolt) or a local `.env` file (never committed).

> **`AI_INSTRUCTIONS.md`** contains machine-readable rules for AI tools. When connecting this repo to Lovable/Bolt, the AI will read that file and know to ask you for credentials, use real API calls (not mocks), and use the correct authentication pattern. If the AI still builds a mock app, paste the contents of `AI_INSTRUCTIONS.md` directly into your prompt.

---

## General Approach

The workflow is the same regardless of which AI tool you use:

1. **Connect this repo** as a knowledge source (Lovable/Bolt) or clone it locally
2. **Add your secrets** — add each variable from `.env.example` as a platform secret (Lovable: Settings → Secrets) or copy `.env.example` to `.env` locally
3. **Open the relevant resource `.md` file(s)** for the feature you want to build (e.g., `resources/sales/sales-invoices.md`)
4. **Paste the file contents as context** to your AI tool (or let it read the workspace)
5. **Write your prompt** describing what you want to build
6. The AI generates code that reads credentials from environment secrets, matches the exact API shapes, and handles authentication correctly

---

## Using with Cursor or VS Code Copilot

These tools can read your open workspace as context automatically.

```
1. Open this repo as a workspace in Cursor or VS Code
2. Open the resource file(s) relevant to your feature
   (e.g., resources/sales/sales-invoices.md)
3. In Copilot Chat or Cursor Chat, write:

   "@workspace I want to build a feature that lists all open sales invoices
    and lets the user post them.
    Use the BC API structure defined in this repo.
    Read .env.example for the config variables."

4. The AI will generate code that matches the exact API shapes,
   uses your .env variables, and handles auth correctly.
```

**Pro tip:** For complex features that span multiple resources, explicitly reference the files:

```
"@workspace Using the API definitions in resources/customers/customers.md
 and resources/sales/sales-invoices.md, build a customer detail page
 that shows the customer info and all their invoices.
 Read .env.example for config."
```

---

## Using with Lovable or Bolt

These tools can connect to a GitHub repo as a knowledge source.

### Setup (one-time) — YOU MUST DO THIS BEFORE PROMPTING

> **If you skip this step, Lovable will build a fake mock app with placeholder data instead of a real BC integration.**

1. In Lovable/Bolt, connect this GitHub repo as a knowledge source
2. Go to **Settings → Secrets** (Lovable) or **Environment Variables** (Bolt)
3. **Add ALL 5 secrets below with your real values** — the AI will not work without them:

| Secret Name         | Where to find it                                              | Example                                 |
|---------------------|---------------------------------------------------------------|-----------------------------------------|
| `BC_TENANT_ID`      | Azure Portal → Entra ID → Overview → Tenant ID               | `164d3b0c-8dca-45a9-9300-17a0e8bc3325`  |
| `BC_CLIENT_ID`      | Azure Portal → App Registrations → Your App → Client ID      | `a1b2c3d4-...`                          |
| `BC_CLIENT_SECRET`  | Azure Portal → App Registrations → Certificates & Secrets    | `aBc~dEf...`                            |
| `BC_ENVIRONMENT`    | Usually `Production` or `Sandbox`                             | `Sandbox`                               |
| `BC_COMPANY_NAME`   | BC → Settings → Company Information → display name            | `CRONUS Danmark A/S`                    |

4. **Verify:** After adding secrets, confirm in Settings → Secrets that all 5 appear in the list

### Prompting — ALWAYS include the credential reminder

When prompting Lovable, **always** include a line about credentials. Example:

```
"Build a customer list page that fetches customers from the
 Business Central API. Use the API structure from the connected repo.
 Read AI_INSTRUCTIONS.md for the rules.
 Credentials are stored as Lovable Secrets (BC_TENANT_ID, BC_CLIENT_ID,
 BC_CLIENT_SECRET, BC_ENVIRONMENT, BC_COMPANY_NAME) — access them via
 Deno.env.get() in Supabase Edge Functions. Never hardcode credentials.
 Never use mock data — make real API calls to Business Central.
 Show: customer number, name, email, balance, and a link to view details."
```

**For multi-page apps:**

```
"Using the BC API reference repo as context, build a sales management app.
 Read AI_INSTRUCTIONS.md first — follow all rules.
 All BC credentials are configured as Lovable Secrets.
 Use Supabase Edge Functions for server-side API calls (client_secret
 must never be exposed to the browser).
 Build:
 - A dashboard showing open quotes, orders, and invoices counts
 - A sales invoices list page with status filters
 - A detail page for each invoice showing line items
 - A 'Post Invoice' button that calls the bound action
 All API shapes are defined in the connected repo's resource files.
 All credentials come from platform secrets (never hardcoded)."
```

> **If Lovable still creates a mock app:** Copy the full contents of `AI_INSTRUCTIONS.md` and paste it directly into your Lovable prompt. This forces the AI to read the rules even if it didn't pick them up from the repo connection.

---

## Using with Claude or ChatGPT (Direct Paste)

If your AI tool does not support workspace or repo connections, copy-paste the relevant files:

1. Copy the contents of `.env.example` and paste it into the conversation
2. Copy the contents of `resources/_overview.md` for general API context
3. Copy the specific resource file(s) for your feature
4. Write your prompt

```
"Here is my BC API configuration [paste .env.example].
 Here is the API reference for sales invoices [paste sales-invoices.md].
 Build a Next.js API route that:
 1. Gets an OAuth2 token using the client credentials from .env
 2. Fetches all draft sales invoices
 3. Returns them as JSON
 Handle token caching and errors."
```

---

## Prompt Templates

Here are 5 ready-to-use prompts. Copy any of these and adjust to your needs:

### 1. Sales Order Dashboard

```
List all open sales orders from Business Central. Group by customer.
Allow filtering by date. Use the API structure from this repo.
Config variables come from .env. Handle OAuth2 token refresh automatically.
```

### 2. Vendor Lookup

```
Build a vendor lookup page. Search vendors by name using OData $filter
with the contains() function. Show address, payment terms, and balance.
Use the vendor API structure from resources/vendors/vendors.md.
All config from .env.
```

### 3. Invoice Posting

```
Create a page to post a sales invoice by its ID. Show the invoice details
first with a confirmation dialog before posting. Call the Microsoft.NAV.post
bound action to post it. Handle errors (401, 404, 409) with user-friendly
messages. Use the API structure from resources/sales/sales-invoices.md.
```

### 4. Item Browser

```
Build an item browser that shows items with their inventory levels and
item category. Use $expand=itemCategory,inventory on the items endpoint.
Allow filtering by type (Inventory/Service) and searching by name.
Use the API structure from resources/items/items.md.
```

### 5. Financial Dashboard

```
Create a dashboard showing:
- Total open sales invoices (count and sum of amounts)
- Total open purchase orders (count and sum of amounts)
- Journal entries pending posting
Use the respective API endpoints from the resources/ folder.
Config from .env. Cache the OAuth2 token for 55 minutes.
```

---

## Tips for Best Results

1. **Always include the relevant resource `.md` file as context.** The more specific API documentation the AI has, the more accurate its code will be.

2. **Mention that all config comes from secrets / environment variables.** Tell the AI explicitly: "Never hardcode credentials. All configuration comes from platform secrets (Lovable/Bolt) or environment variables. Variable names are defined in `.env.example`."

3. **Tell the AI to handle token expiry.** Include this in your prompt: "The OAuth2 token has a 1-hour TTL. Cache it and refresh automatically before expiry."

4. **For multi-resource features, include multiple `.md` files.** If your feature touches customers and sales invoices, include both resource files as context.

5. **Reference the `.env.example` variable names.** The AI should use the exact variable names from `.env.example` (e.g., `BC_BASE_URL`, `BC_COMPANY_NAME`) so the generated code works with your config file.

6. **Ask for error handling explicitly.** Add to your prompt: "Handle HTTP errors: 401 (refresh token), 404 (show not found), 409 (ETag conflict — retry with fresh data), 429 (rate limit — retry after delay)."

7. **For bound actions, mention them by name.** Instead of "post the invoice", say "call the `Microsoft.NAV.post` bound action on the sales invoice endpoint."

8. **Use the overview file for broad features.** If you are building something that spans many resources, include `resources/_overview.md` — it has a complete endpoint table the AI can reference.

---

## Example: Full Working Prompt for a Sales App

Here is a complete prompt that would generate a working application:

```
Using the BC API reference files I have provided as context, build a
Next.js application with the following features:

1. OAuth2 authentication using client credentials flow
   - Token endpoint, client ID, secret, and scope from .env
   - Cache token in memory, refresh when expired (1h TTL)

2. Customer list page
   - GET /customers with $select=number,displayName,email,balance
   - Search by name using $filter=contains(displayName,'...')
   - Paginate using @odata.nextLink
   - Click a customer to see details

3. Customer detail page
   - GET /customers({id}) with full field display
   - Show related sales invoices: GET /salesInvoices?$filter=customerId eq {id}
   - For each invoice, show a "Post" button if status is Draft

4. Post invoice action
   - Call Microsoft.NAV.post bound action
   - Show confirmation modal before posting
   - Handle 409 ETag conflict by re-fetching and retrying

All API base URLs, company name, and credentials come from .env variables
as defined in .env.example. Never hardcode any values.
```

This prompt, combined with the relevant resource files from this repo, gives the AI everything it needs to generate a complete, working application.
