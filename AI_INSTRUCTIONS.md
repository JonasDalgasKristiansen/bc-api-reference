# INSTRUCTIONS FOR AI CODING TOOLS

> **This file is written FOR the AI tool (Lovable, Bolt, Cursor, Copilot, etc.) — not for a human developer.** If you are an AI reading this repo as context, follow these instructions before generating any code.

---

## RULE 1: NEVER BUILD A MOCK / PLACEHOLDER APP

This repository contains a **real, production API reference** for Microsoft Dynamics 365 Business Central. Every endpoint, field name, and example is verified against the live API.

- **NEVER** generate fake/mock data, placeholder API responses, or simulated endpoints
- **NEVER** create a UI that shows hardcoded sample data instead of real API calls
- **ALWAYS** generate code that makes real HTTP calls to the Business Central API
- **ALWAYS** use environment variables / platform secrets for all credentials — never hardcode them

If you cannot make real API calls (e.g., running in a browser without a backend), generate a proper backend service or API proxy that handles authentication and API calls server-side.

---

## RULE 2: ASK THE USER FOR CREDENTIALS BEFORE BUILDING

Before generating any Business Central integration code, you MUST confirm the user has configured these 5 required credentials. **Ask the user for each value** if they have not been provided:

| Variable Name     | What It Is                                          | Example Value                                |
|-------------------|-----------------------------------------------------|----------------------------------------------|
| `BC_TENANT_ID`    | Azure / Entra ID Tenant ID (GUID)                   | `164d3b0c-8dca-45a9-9300-17a0e8bc3325`       |
| `BC_CLIENT_ID`    | Azure App Registration Client ID (GUID)              | `a1b2c3d4-e5f6-7890-abcd-ef1234567890`       |
| `BC_CLIENT_SECRET` | Azure App Registration Client Secret (string)       | `aBc~dEf...` (generated in Azure Portal)     |
| `BC_ENVIRONMENT`  | BC environment name                                  | `Production` or `Sandbox`                     |
| `BC_COMPANY_NAME` | The exact company display name in Business Central   | `CRONUS Danmark A/S`                          |

### How to ask

When the user asks you to build a BC integration feature, respond with something like:

> "Before I build this, I need your Business Central credentials. Please provide or confirm these 5 values (or add them as platform secrets if using Lovable/Bolt):
> 1. **BC_TENANT_ID** — your Azure Tenant ID (GUID)
> 2. **BC_CLIENT_ID** — your App Registration Client ID (GUID)  
> 3. **BC_CLIENT_SECRET** — your App Registration Client Secret
> 4. **BC_ENVIRONMENT** — your BC environment name (e.g., `Production` or `Sandbox`)
> 5. **BC_COMPANY_NAME** — your exact company name in BC (e.g., `CRONUS Danmark A/S`)
>
> In Lovable: add these as Secrets (Settings → Secrets).
> In Bolt: add as Environment Variables."

**Only proceed with code generation after the user has confirmed their credentials are configured.**

---

## RULE 3: AUTHENTICATION PATTERN

Every app you build MUST use this exact OAuth2 Client Credentials flow:

```
POST https://login.microsoftonline.com/{BC_TENANT_ID}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials
&client_id={BC_CLIENT_ID}
&client_secret={BC_CLIENT_SECRET}
&scope=https://api.businesscentral.dynamics.com/.default
```

The token expires after **1 hour**. Cache it and refresh automatically.

### Base URLs (constructed from credentials)

```
Standard API:   https://api.businesscentral.dynamics.com/v2.0/{BC_TENANT_ID}/{BC_ENVIRONMENT}/api/v2.0
Automation API: https://api.businesscentral.dynamics.com/v2.0/{BC_TENANT_ID}/{BC_ENVIRONMENT}/api/microsoft/automation/v2.0
```

### Company context

Almost all endpoints require a company context. Always use the company **name** (not a GUID):

```
/companies(name='{BC_COMPANY_NAME}')/customers
/companies(name='{BC_COMPANY_NAME}')/salesInvoices
/companies(name='{BC_COMPANY_NAME}')/items
... etc.
```

---

## RULE 4: USE THE EXACT FIELD NAMES FROM THIS REPO

The resource `.md` files in this repo document the **exact** field names, data types, and JSON shapes returned by the BC API. Use them exactly as documented.

Common mistakes to avoid:
- ❌ `jobNo` → ✅ `jobNumber`
- ❌ `jobTaskNo` → ✅ `jobTaskNumber`  
- ❌ `lastModifiedDateTime` → ✅ `lastModfiedDateTime` (Microsoft's typo — use it as-is)
- ❌ `commissionPercent` → ✅ `commisionPercent` (Microsoft's typo — use it as-is)
- ❌ `companies({guid})` → ✅ `companies(name='{BC_COMPANY_NAME}')`
- ❌ `/jobs` → ✅ `/projects` (v22.0+) or OData `/Jobs` web service (all versions)
- ❌ POST to `/employees({id})/timeRegistrationEntries` → ✅ POST to `/timeRegistrationEntries` (top-level)
- ❌ `employeeId` in POST body → ✅ `employeeNumber` in POST body (short code like `"MH"`, not a GUID)

### Time Registration Entries — CRITICAL

When creating time entries:
- **ALWAYS** POST to the **top-level** endpoint: `/companies(name='...')/timeRegistrationEntries`
- **NEVER** POST to the employee sub-resource: `/employees({id})/timeRegistrationEntries`
- **ALWAYS** use `employeeNumber` (the short employee code like `"MH"` or `"R0010"`) in the request body
- **NEVER** use `employeeId` (a GUID) in the request body — BC will error with: *"The Employee does not exist"*
- The employee sub-resource endpoint is **GET-only** — use it only to read entries for a specific employee
- **The Type field is auto-determined** — you don't set it. BC infers it: `jobNumber` → Job, `absence` → Absence, neither → Resource
- **These Time Sheet fields are NOT in the API:** Description, Chargeable, and Work Code. Do not try to set them — they are not exposed in the standard v2.0 `timeRegistrationEntries` entity.

### Projects / Jobs — 404 Warning

The `/projects` endpoint was added in BC v22.0 (2023 wave 1). On older versions it returns **404**.

**Always implement a fallback:** Try `/projects` first. If it returns 404, fall back to the OData web service at:
```
https://api.businesscentral.dynamics.com/v2.0/{BC_TENANT_ID}/{BC_ENVIRONMENT}/ODataV4/Company('{BC_COMPANY_NAME}')/Jobs
```
See `resources/projects/projects.md` for the full fallback pattern with code example.

---

## RULE 5: ARCHITECTURE FOR WEB APPS

When building a web application (e.g., in Lovable or Bolt), you MUST use a backend-for-frontend pattern:

```
[Browser / React App]
        ↓
[Backend API / Edge Function / Supabase Function]
        ↓ (OAuth2 token + API calls happen here)
[Business Central API]
```

**Why:** The BC OAuth2 Client Credentials flow requires a `client_secret`, which must NEVER be exposed to the browser. All BC API calls must happen server-side.

### In Lovable specifically:
- Use **Supabase Edge Functions** for server-side BC API calls
- Store BC credentials as Lovable Secrets (they become available as `Deno.env.get('BC_TENANT_ID')` in Edge Functions)
- The React frontend calls your Edge Function, which calls the BC API

### In Bolt specifically:
- Use server-side API routes or serverless functions
- Access credentials via `process.env.BC_TENANT_ID` etc.

---

## RULE 6: WHERE TO FIND API DETAILS

| What you need                            | Where to look                                        |
|------------------------------------------|------------------------------------------------------|
| Full list of all endpoints               | `resources/_overview.md` — master endpoint table     |
| Specific resource fields and examples    | `resources/{category}/{resource}.md`                 |
| OData query patterns                     | `resources/_overview.md` — OData Cheat Sheet section |
| Bound actions (post, send, etc.)         | `resources/_overview.md` — Bound Actions table       |
| Authentication setup                     | `README.md` — Setup Guide + `DEVELOPER_GUIDE.md` — full technical details |
| Environment variable names               | `.env.example`                                       |
| Time registration / time sheets          | `resources/time-tracking/time-registration-entries.md` — READ THE WARNINGS about Time Sheet headers vs lines |
| Projects / Jobs                          | `resources/projects/projects.md` — ⚠️ `/projects` may 404 on older BC; has OData fallback |
| Automation (users, extensions)           | `resources/automation/_overview.md`                  |

---

## RULE 7: COMMON PATTERNS TO IMPLEMENT

### Token caching
```typescript
let cachedToken: string | null = null;
let tokenExpiry: number = 0;

async function getToken(): Promise<string> {
  if (cachedToken && Date.now() < tokenExpiry) return cachedToken;
  
  const resp = await fetch(
    `https://login.microsoftonline.com/${BC_TENANT_ID}/oauth2/v2.0/token`,
    {
      method: 'POST',
      headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
      body: new URLSearchParams({
        grant_type: 'client_credentials',
        client_id: BC_CLIENT_ID,
        client_secret: BC_CLIENT_SECRET,
        scope: 'https://api.businesscentral.dynamics.com/.default',
      }),
    }
  );
  const data = await resp.json();
  cachedToken = data.access_token;
  tokenExpiry = Date.now() + (data.expires_in - 300) * 1000; // refresh 5 min early
  return cachedToken;
}
```

### Making a BC API call
```typescript
const BASE_URL = `https://api.businesscentral.dynamics.com/v2.0/${BC_TENANT_ID}/${BC_ENVIRONMENT}/api/v2.0`;

async function bcGet(path: string): Promise<any> {
  const token = await getToken();
  const resp = await fetch(`${BASE_URL}/companies(name='${BC_COMPANY_NAME}')${path}`, {
    headers: {
      'Authorization': `Bearer ${token}`,
      'Accept': 'application/json',
    },
  });
  if (!resp.ok) throw new Error(`BC API error: ${resp.status} ${resp.statusText}`);
  return resp.json();
}

// Usage:
const customers = await bcGet("/customers?$select=number,displayName,email,balance&$top=50");
```

### ETag handling for PATCH/DELETE
```typescript
async function bcPatch(path: string, etag: string, body: object): Promise<any> {
  const token = await getToken();
  const resp = await fetch(`${BASE_URL}/companies(name='${BC_COMPANY_NAME}')${path}`, {
    method: 'PATCH',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json',
      'If-Match': etag,
    },
    body: JSON.stringify(body),
  });
  if (!resp.ok) throw new Error(`BC API error: ${resp.status} ${resp.statusText}`);
  return resp.json();
}
```

---

## SUMMARY

1. **Ask for credentials first** — never build without them
2. **Make real API calls** — never mock data
3. **Use server-side code** for authentication — never expose client_secret to browsers
4. **Use exact field names** from the resource `.md` files
5. **Use company name** in URLs, not GUIDs: `companies(name='{BC_COMPANY_NAME}')`
6. **Cache tokens** and refresh before expiry
7. **Read the specific resource file** for the endpoint you're working with — it has all the fields, examples, and gotchas
