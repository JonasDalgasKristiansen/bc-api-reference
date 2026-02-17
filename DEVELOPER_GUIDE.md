# Developer Guide — Technical Reference

This document contains the full technical details for working with the Business Central REST API v2.0. If you're a developer building an integration manually (without AI tools), this is your starting point.

> **Using an AI tool like Lovable or Bolt?** You don't need to read this — the AI reads the `resources/` folder directly. See the [README](README.md) for setup steps and [AI_INSTRUCTIONS.md](AI_INSTRUCTIONS.md) for AI-specific rules.

---

## Authentication

Business Central API uses **OAuth2 Client Credentials** flow. Follow these steps to get set up.

### Step 1: Register an App in Azure Entra ID

1. Go to the [Azure Portal](https://portal.azure.com)
2. Navigate to **Microsoft Entra ID** → **App registrations** → **New registration**
3. Enter a name (e.g., `BC API Integration`)
4. Set **Supported account types** to: `Accounts in this organizational directory only (Single tenant)`
5. Leave **Redirect URI** blank (not needed for client credentials flow)
6. Click **Register**
7. Copy the **Application (client) ID** — this is your `BC_CLIENT_ID`
8. Copy the **Directory (tenant) ID** — this is your `BC_TENANT_ID`

### Step 2: Create a Client Secret

1. In your app registration, go to **Certificates & secrets** → **Client secrets** → **New client secret**
2. Enter a description (e.g., `BC API Secret`) and choose an expiry period
3. Click **Add**
4. **Immediately copy the secret Value** (it will only be shown once) — this is your `BC_CLIENT_SECRET`

### Step 3: Grant API Permissions

1. In your app registration, go to **API permissions** → **Add a permission**
2. Select **Dynamics 365 Business Central**
3. Choose **Application permissions**
4. Check **Financials.ReadWrite.All**
5. Click **Add permissions**
6. Click **Grant admin consent for [your organization]** (requires Global Admin or Privileged Role Admin)
7. Confirm the status shows a green checkmark

### Step 4: Register the App in Business Central

1. Open **Business Central**
2. Search for **Microsoft Entra Applications** (or "Azure Active Directory Applications" in older versions)
3. Click **New**
4. Paste your **Application (client) ID** into the **Client ID** field
5. Enter a **Description** (e.g., `API Integration App`)
6. Set **State** to **Enabled**
7. In the **User Permission Sets** section, add the permission sets your app needs:
   - `D365 FULL ACCESS` for full access, or
   - More granular sets like `D365 READ`, `D365 BUS FULL ACCESS`, etc.
8. Click **OK** / close the page

### Step 5: Find Your Company Name

After completing steps 1–4, you can verify your company name with this API call:

```http
GET {{BC_BASE_URL}}/companies
Authorization: Bearer {access_token}
```

Response:

```json
{
  "value": [
    {
      "id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
      "name": "CRONUS USA, Inc.",
      "displayName": "CRONUS USA, Inc.",
      "businessProfileId": ""
    }
  ]
}
```

You can use the `displayName` value directly as your `BC_COMPANY_NAME` secret.
The API uses it in URLs like: `companies(name='My Company Name')`.

---

## Getting an Access Token

Every API call requires a Bearer token. Here is how to obtain one:

```http
POST https://login.microsoftonline.com/{{BC_TENANT_ID}}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials
&client_id={{BC_CLIENT_ID}}
&client_secret={{BC_CLIENT_SECRET}}
&scope=https://api.businesscentral.dynamics.com/.default
```

**Response:**

```json
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "ext_expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik..."
}
```

**Usage notes:**

- Tokens are valid for **1 hour** (3599 seconds)
- Your application should **cache the token** and refresh it before expiry
- Include the token on every API request as: `Authorization: Bearer {access_token}`
- If you receive a `401 Unauthorized` response, request a new token

---

## Base URL Pattern

All API calls follow these patterns:

```
Standard API:   {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/{{endpoint}}
Automation API: {{BC_AUTOMATION_URL}}/companies(name='{{BC_COMPANY_NAME}}')/{{endpoint}}
```

**Expanded examples:**

```
GET https://api.businesscentral.dynamics.com/v2.0/{tenant}/{environment}/api/v2.0/companies(name='{companyName}')/customers
GET https://api.businesscentral.dynamics.com/v2.0/{tenant}/{environment}/api/microsoft/automation/v2.0/companies(name='{companyName}')/users
```

The only root-level endpoint (no company name required):

```
GET {{BC_BASE_URL}}/companies
GET {{BC_AUTOMATION_URL}}/companies
```

---

## OData Query Parameters

All list endpoints support OData query parameters:

| Parameter   | Description                          | Example                                                              |
|-------------|--------------------------------------|----------------------------------------------------------------------|
| `$top`      | Limit number of results              | `?$top=10`                                                           |
| `$skip`     | Skip N results (for pagination)      | `?$skip=20`                                                          |
| `$filter`   | Filter results by field values       | `?$filter=displayName eq 'Contoso'`                                  |
| `$select`   | Return only specific fields          | `?$select=id,number,displayName`                                     |
| `$expand`   | Include related entities inline      | `?$expand=salesInvoiceLines`                                         |
| `$orderby`  | Sort results by a field              | `?$orderby=displayName asc`                                         |

**Combining parameters:**

```
?$filter=balance gt 0&$select=number,displayName,balance&$top=50&$orderby=balance desc
```

---

## Bound Actions

Bound actions are **POST calls** that trigger business logic in Business Central. Unlike standard CRUD operations, these execute server-side processes like posting documents, sending emails, or converting document types.

**Usage pattern:**

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/{{resource}}({{id}})/Microsoft.NAV.{{action}}
Authorization: Bearer {access_token}
```

No request body is required for most bound actions (send the POST with an empty body).

### All Available Bound Actions

| Resource               | Action             | Endpoint                                                              | Description                          |
|------------------------|--------------------|-----------------------------------------------------------------------|--------------------------------------|
| Sales Quotes           | `makeInvoice`      | `salesQuotes({id})/Microsoft.NAV.makeInvoice`                         | Convert quote to sales invoice       |
| Sales Quotes           | `makeOrder`        | `salesQuotes({id})/Microsoft.NAV.makeOrder`                           | Convert quote to sales order         |
| Sales Quotes           | `send`             | `salesQuotes({id})/Microsoft.NAV.send`                                | Send quote via email                 |
| Sales Orders           | `shipAndInvoice`   | `salesOrders({id})/Microsoft.NAV.shipAndInvoice`                      | Ship and invoice the order           |
| Sales Invoices         | `post`             | `salesInvoices({id})/Microsoft.NAV.post`                              | Post a draft invoice                 |
| Sales Invoices         | `send`             | `salesInvoices({id})/Microsoft.NAV.send`                              | Send posted invoice via email        |
| Sales Invoices         | `cancelAndSend`    | `salesInvoices({id})/Microsoft.NAV.cancelAndSend`                     | Cancel invoice and send cancellation |
| Sales Credit Memos     | `post`             | `salesCreditMemos({id})/Microsoft.NAV.post`                           | Post a draft credit memo             |
| Sales Credit Memos     | `send`             | `salesCreditMemos({id})/Microsoft.NAV.send`                           | Send credit memo via email           |
| Purchase Orders        | `receive`          | `purchaseOrders({id})/Microsoft.NAV.receive`                          | Receive items on the order           |
| Purchase Orders        | `receiveAndInvoice`| `purchaseOrders({id})/Microsoft.NAV.receiveAndInvoice`                | Receive and invoice in one step      |
| Purchase Invoices      | `post`             | `purchaseInvoices({id})/Microsoft.NAV.post`                           | Post a draft purchase invoice        |
| Purchase Credit Memos  | `post`             | `purchaseCreditMemos({id})/Microsoft.NAV.post`                        | Post a draft purchase credit memo    |
| Purchase Credit Memos  | `cancel`           | `purchaseCreditMemos({id})/Microsoft.NAV.cancel`                      | Cancel purchase credit memo          |
| Journals               | `post`             | `journals({id})/Microsoft.NAV.post`                                   | Post all lines in a journal batch    |
| Cust. Payment Journals | `post`             | `customerPaymentJournals({id})/Microsoft.NAV.post`                    | Post customer payment journal        |
| Vendor Payment Journals| `post`             | `vendorPaymentJournals({id})/Microsoft.NAV.post`                      | Post vendor payment journal          |

---

## HTTP Status Codes

| Code  | Meaning                | Description                                                                                    |
|-------|------------------------|------------------------------------------------------------------------------------------------|
| `200` | OK                     | Request succeeded. Response body contains the requested data.                                  |
| `201` | Created                | Resource was successfully created. Response body contains the new resource.                    |
| `204` | No Content             | Request succeeded with no response body (e.g., DELETE, bound actions).                        |
| `400` | Bad Request            | The request body is malformed or contains invalid field values.                                |
| `401` | Unauthorized           | The access token is missing, expired, or invalid. Request a new token.                        |
| `403` | Forbidden              | The authenticated app does not have permission for this operation.                             |
| `404` | Not Found              | The resource or endpoint does not exist. Check the ID or URL.                                 |
| `409` | Conflict               | ETag mismatch — the record was modified since you last retrieved it. GET it again and retry.  |
| `429` | Too Many Requests      | Rate limit exceeded. Wait and retry. Check `Retry-After` header for delay in seconds.         |
| `500` | Internal Server Error  | A server-side error occurred. Retry after a delay; if persistent, check BC admin center.      |

---

## Pagination

List endpoints return paginated results. The default page size is **100** records (configurable up to **20,000** with `$top`).

When more results are available, the response includes an `@odata.nextLink` property:

```json
{
  "@odata.context": "...",
  "value": [ ... ],
  "@odata.nextLink": "https://api.businesscentral.dynamics.com/v2.0/.../customers?$skip=100"
}
```

**How to paginate:**

1. Make your initial GET request
2. Check if the response contains `@odata.nextLink`
3. If yes, make a GET request to the `@odata.nextLink` URL (it includes the `$skip` parameter automatically)
4. Repeat until there is no `@odata.nextLink` in the response

**Example pagination logic (pseudocode):**

```
url = "{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/customers?$top=50"
allRecords = []

while url is not null:
    response = GET(url)
    allRecords.append(response.value)
    url = response["@odata.nextLink"] or null
```

---

## Webhook Support

Business Central can send real-time notifications to your application when data changes. See [resources/webhooks/subscriptions.md](resources/webhooks/subscriptions.md) for the full guide.

**Supported resources for webhooks:**

accounts, companyInformation, countriesRegions, currencies, customerPaymentJournals, customers, dimensions, employees, generalLedgerEntries, itemCategories, items, journals, paymentMethods, paymentTerms, purchaseInvoices, salesCreditMemos, salesInvoices, salesOrders, salesQuotes, shipmentMethods, unitsOfMeasure, vendors

---

## Resources Index

For the complete list of all endpoints, fields, and examples, see:

- [Master Endpoint Reference](resources/_overview.md) — all endpoints in one table
- [AI Instructions](AI_INSTRUCTIONS.md) — rules for AI coding tools
- [How to Use with AI Tools](HOW_TO_USE_WITH_AI.md) — practical guide for Lovable, Bolt, Cursor, etc.

Individual resource files are in the `resources/` folder, organized by category.

---

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.
