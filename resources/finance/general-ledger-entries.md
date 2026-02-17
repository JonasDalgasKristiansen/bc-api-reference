# General Ledger Entries

General ledger entries are the posted transactions in the general ledger. These are **read-only** — they are created automatically when journals are posted, invoices are posted, or other business transactions occur in Business Central.

---

## Base Endpoint

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/generalLedgerEntries
```

---

## Operations

### 1. List General Ledger Entries

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/generalLedgerEntries
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "@odata.context": "https://api.businesscentral.dynamics.com/v2.0/.../generalLedgerEntries",
  "value": [
    {
      "id": "a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa",
      "entryNumber": 1001,
      "postingDate": "2025-02-17",
      "documentNumber": "GJ-001",
      "documentType": "  ",
      "accountId": "a1b2c3d4-e5f6-7890-abcd-666666666666",
      "accountNumber": "60100",
      "description": "Monthly office rent",
      "debitAmount": 5000.00,
      "creditAmount": 0.00,
      "lastModifiedDateTime": "2025-02-17T09:45:00Z"
    },
    {
      "id": "b2b2b2b2-2222-2222-2222-bbbbbbbbbbbb",
      "entryNumber": 1002,
      "postingDate": "2025-02-17",
      "documentNumber": "GJ-001",
      "documentType": "  ",
      "accountId": "a1b2c3d4-e5f6-7890-abcd-111111111111",
      "accountNumber": "10100",
      "description": "Monthly office rent",
      "debitAmount": 0.00,
      "creditAmount": 5000.00,
      "lastModifiedDateTime": "2025-02-17T09:45:00Z"
    },
    {
      "id": "c3c3c3c3-3333-3333-3333-cccccccccccc",
      "entryNumber": 1003,
      "postingDate": "2025-02-17",
      "documentNumber": "SI-1001",
      "documentType": "Invoice",
      "accountId": "a1b2c3d4-e5f6-7890-abcd-444444444444",
      "accountNumber": "40100",
      "description": "Sales invoice: Adatum Corporation",
      "debitAmount": 0.00,
      "creditAmount": 15000.00,
      "lastModifiedDateTime": "2025-02-17T11:00:00Z"
    }
  ]
}
```

---

### 2. Get a Single Entry

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/generalLedgerEntries(a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa)
Authorization: Bearer {access_token}
Accept: application/json
```

---

## Fields

| Field                    | Type     | Description                                                      |
|--------------------------|----------|------------------------------------------------------------------|
| `id`                     | GUID     | Unique identifier                                                |
| `entryNumber`            | integer  | Sequential entry number                                          |
| `postingDate`            | date     | Date the entry was posted                                        |
| `documentNumber`         | string   | Document number (e.g., journal number, invoice number)           |
| `documentType`           | string   | `Payment`, `Invoice`, `Credit Memo`, `Finance Charge Memo`, `Reminder`, `Refund`, or blank |
| `accountId`              | GUID     | G/L account ID                                                   |
| `accountNumber`          | string   | G/L account number                                               |
| `description`            | string   | Entry description                                                |
| `debitAmount`            | decimal  | Debit amount (0 if credit)                                       |
| `creditAmount`           | decimal  | Credit amount (0 if debit)                                       |
| `lastModifiedDateTime`   | datetime | Last modification timestamp                                      |

---

## Document Type Values

| documentType              | Description                           |
|---------------------------|---------------------------------------|
| ` ` (blank)               | General journal entry                 |
| `Payment`                 | Payment transaction                   |
| `Invoice`                 | Posted invoice                        |
| `Credit Memo`             | Posted credit memo                    |
| `Finance Charge Memo`     | Finance charge                        |
| `Reminder`                | Reminder entry                        |
| `Refund`                  | Refund transaction                    |

---

## Filter Examples

```http
# Entries for a specific date
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/generalLedgerEntries?$filter=postingDate eq 2025-02-17

# Entries for a date range
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/generalLedgerEntries?$filter=postingDate ge 2025-02-01 and postingDate le 2025-02-28

# Entries for a specific G/L account
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/generalLedgerEntries?$filter=accountNumber eq '60100'

# Entries for a specific document
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/generalLedgerEntries?$filter=documentNumber eq 'GJ-001'

# Only invoice-related entries
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/generalLedgerEntries?$filter=documentType eq 'Invoice'

# Debit entries only
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/generalLedgerEntries?$filter=debitAmount gt 0

# Large transactions
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/generalLedgerEntries?$filter=debitAmount gt 10000 or creditAmount gt 10000

# Combined: invoices for a specific account in a date range
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/generalLedgerEntries?$filter=accountNumber eq '40100' and documentType eq 'Invoice' and postingDate ge 2025-01-01

# Order by posting date descending
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/generalLedgerEntries?$orderby=postingDate desc

# Top 50 most recent entries
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/generalLedgerEntries?$orderby=entryNumber desc&$top=50
```

---

## Pagination

General ledger entries can contain thousands of records. Use `$top` and `$skip` or follow `@odata.nextLink` to paginate:

```http
# First page (100 entries)
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/generalLedgerEntries?$top=100

# Next page
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/generalLedgerEntries?$top=100&$skip=100
```

Or follow `@odata.nextLink` from the response:

```json
{
  "@odata.nextLink": "https://api.businesscentral.dynamics.com/v2.0/.../generalLedgerEntries?$skiptoken=..."
}
```

---

## Important Notes

- General ledger entries are **read-only**. You cannot create, update, or delete them directly.
- To create new G/L entries, use the [Journals](journals.md) resource to post journal lines.
- Each posted transaction creates at least two entries (debit and credit) to maintain double-entry bookkeeping.
- The `documentNumber` links related debit and credit entries from the same transaction.
- Use `$select` to retrieve only needed fields when querying large datasets for better performance.

---

## Common Errors

| Status | Error                  | Cause & Resolution                                                                 |
|--------|------------------------|------------------------------------------------------------------------------------|
| `401`  | Unauthorized           | Access token is missing, expired, or invalid. Request a new token.                 |
| `404`  | Not Found              | The entry ID does not exist.                                                       |
| `405`  | Method Not Allowed     | Attempting POST/PATCH/DELETE — entries are read-only.                               |
