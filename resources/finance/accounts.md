# Accounts (Chart of Accounts / G/L Accounts)

General ledger (G/L) accounts represent the chart of accounts in Business Central. These are read-only via the standard API — accounts are typically managed through the Business Central web client.

---

## Base Endpoint

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/accounts
```

---

## Operations

### 1. List All Accounts

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/accounts
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "@odata.context": "https://api.businesscentral.dynamics.com/v2.0/.../accounts",
  "value": [
    {
      "id": "a1b2c3d4-e5f6-7890-abcd-111111111111",
      "number": "10100",
      "displayName": "Cash",
      "category": "Assets",
      "subCategory": "Cash",
      "blocked": false,
      "accountType": "Posting",
      "directPosting": true,
      "netChange": 125000.00,
      "lastModifiedDateTime": "2025-01-15T08:00:00Z"
    },
    {
      "id": "a1b2c3d4-e5f6-7890-abcd-222222222222",
      "number": "10200",
      "displayName": "Accounts Receivable",
      "category": "Assets",
      "subCategory": "Accounts Receivable",
      "blocked": false,
      "accountType": "Posting",
      "directPosting": false,
      "netChange": 87500.00,
      "lastModifiedDateTime": "2025-01-15T08:00:00Z"
    },
    {
      "id": "a1b2c3d4-e5f6-7890-abcd-333333333333",
      "number": "20100",
      "displayName": "Accounts Payable",
      "category": "Liabilities",
      "subCategory": "Current Liabilities",
      "blocked": false,
      "accountType": "Posting",
      "directPosting": false,
      "netChange": -62000.00,
      "lastModifiedDateTime": "2025-01-15T08:00:00Z"
    },
    {
      "id": "a1b2c3d4-e5f6-7890-abcd-444444444444",
      "number": "40100",
      "displayName": "Sales Revenue",
      "category": "Income",
      "subCategory": "Income, Product Sales",
      "blocked": false,
      "accountType": "Posting",
      "directPosting": true,
      "netChange": 350000.00,
      "lastModifiedDateTime": "2025-01-15T08:00:00Z"
    },
    {
      "id": "a1b2c3d4-e5f6-7890-abcd-555555555555",
      "number": "50100",
      "displayName": "Cost of Goods Sold",
      "category": "Cost of Goods Sold",
      "subCategory": "Cost of Goods Sold",
      "blocked": false,
      "accountType": "Posting",
      "directPosting": true,
      "netChange": 175000.00,
      "lastModifiedDateTime": "2025-01-15T08:00:00Z"
    },
    {
      "id": "a1b2c3d4-e5f6-7890-abcd-666666666666",
      "number": "60100",
      "displayName": "Salaries Expense",
      "category": "Expense",
      "subCategory": "Payroll Expense",
      "blocked": false,
      "accountType": "Posting",
      "directPosting": true,
      "netChange": 95000.00,
      "lastModifiedDateTime": "2025-01-15T08:00:00Z"
    }
  ]
}
```

---

### 2. Get a Single Account

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/accounts(a1b2c3d4-e5f6-7890-abcd-111111111111)
Authorization: Bearer {access_token}
Accept: application/json
```

---

## Fields

| Field                    | Type     | Description                                                      |
|--------------------------|----------|------------------------------------------------------------------|
| `id`                     | GUID     | Unique identifier                                                |
| `number`                 | string   | Account number (e.g., `10100`)                                   |
| `displayName`            | string   | Account name                                                     |
| `category`               | string   | Account category: `Assets`, `Liabilities`, `Equity`, `Income`, `Cost of Goods Sold`, `Expense` |
| `subCategory`            | string   | Sub-category (e.g., `Cash`, `Accounts Receivable`)               |
| `blocked`                | boolean  | Whether the account is blocked for posting                       |
| `accountType`            | string   | `Posting`, `Heading`, `Total`, `Begin-Total`, `End-Total`        |
| `directPosting`          | boolean  | Whether direct posting is allowed                                |
| `netChange`              | decimal  | Net change for the current fiscal period                         |
| `lastModifiedDateTime`   | datetime | Last modification timestamp                                      |

---

## Category Values

| Category               | Description                        |
|------------------------|------------------------------------|
| `Assets`               | Balance sheet assets               |
| `Liabilities`          | Balance sheet liabilities          |
| `Equity`               | Shareholders' equity               |
| `Income`               | Revenue / income accounts          |
| `Cost of Goods Sold`   | COGS / direct cost accounts        |
| `Expense`              | Operating expense accounts         |

---

## Filter Examples

```http
# All income accounts
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/accounts?$filter=category eq 'Income'

# Posting accounts only (exclude headings/totals)
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/accounts?$filter=accountType eq 'Posting'

# Active accounts that allow direct posting
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/accounts?$filter=blocked eq false and directPosting eq true

# Accounts starting with "1" (assets)
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/accounts?$filter=startswith(number, '1')

# Search by name
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/accounts?$filter=contains(displayName, 'Cash')
```

---

## Important Notes

- Accounts are **read-only** through the standard API v2.0. Create/update/delete must be done through the Business Central web client.
- The `netChange` field reflects the balance for the current fiscal period filter, not all-time balance.
- Use `directPosting eq true` to find accounts suitable for journal line entries.
- Heading and Total accounts cannot receive posted transactions.

---

## Common Errors

| Status | Error                  | Cause & Resolution                                                                 |
|--------|------------------------|------------------------------------------------------------------------------------|
| `401`  | Unauthorized           | Access token is missing, expired, or invalid. Request a new token.                 |
| `404`  | Not Found              | The account ID does not exist.                                                     |
| `405`  | Method Not Allowed     | Attempting POST/PATCH/DELETE — accounts are read-only via the API.                  |
