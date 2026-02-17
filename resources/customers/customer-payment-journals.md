# Customer Payment Journals

Customer payment journals are used to record payments received from customers. Each journal contains journal lines, and the entire journal can be posted as a batch using a bound action. This is a two-level resource: journals contain journal lines.

---

## Base Endpoint

```
{{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/customerPaymentJournals
```

---

## Customer Payment Journals (Parent)

### List All Customer Payment Journals

```http
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/customerPaymentJournals
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "@odata.context": "https://api.businesscentral.dynamics.com/v2.0/.../customerPaymentJournals",
  "value": [
    {
      "@odata.etag": "W/\"JzE5OzExMTExMTExMTE7Q1BKOw==\"",
      "id": "b1c2d3e4-f5a6-7890-1234-567890abcdef",
      "code": "CASHRCPT",
      "displayName": "Cash Receipts",
      "balancingAccountId": "a9b8c7d6-e5f4-3210-fedc-ba9876543210",
      "balancingAccountNumber": "10100",
      "lastModifiedDateTime": "2025-02-10T08:00:00Z"
    },
    {
      "@odata.etag": "W/\"JzE5OzIyMjIyMjIyMjI7Q1BKOw==\"",
      "id": "c2d3e4f5-a6b7-8901-2345-67890abcdef1",
      "code": "PAYMENT",
      "displayName": "Payment Journal",
      "balancingAccountId": "a9b8c7d6-e5f4-3210-fedc-ba9876543210",
      "balancingAccountNumber": "10100",
      "lastModifiedDateTime": "2025-02-12T14:30:00Z"
    }
  ]
}
```

### Get a Single Customer Payment Journal

```http
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/customerPaymentJournals(b1c2d3e4-f5a6-7890-1234-567890abcdef)
Authorization: Bearer {access_token}
Accept: application/json
```

### Create a Customer Payment Journal

```http
POST {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/customerPaymentJournals
Authorization: Bearer {access_token}
Content-Type: application/json
```

**Request Body:**

```json
{
  "code": "WEBPAY",
  "displayName": "Web Payments",
  "balancingAccountNumber": "10100"
}
```

**Response (201 Created):**

```json
{
  "@odata.etag": "W/\"JzE5OzMzMzMzMzMzMzM7Q1BKOw==\"",
  "id": "d3e4f5a6-b7c8-9012-3456-7890abcdef12",
  "code": "WEBPAY",
  "displayName": "Web Payments",
  "balancingAccountId": "a9b8c7d6-e5f4-3210-fedc-ba9876543210",
  "balancingAccountNumber": "10100",
  "lastModifiedDateTime": "2025-02-17T10:00:00Z"
}
```

### Delete a Customer Payment Journal

```http
DELETE {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/customerPaymentJournals(b1c2d3e4-f5a6-7890-1234-567890abcdef)
Authorization: Bearer {access_token}
If-Match: W/"JzE5OzExMTExMTExMTE7Q1BKOw=="
```

**Response:** `204 No Content`

---

## Journal Fields

| Field                     | Type     | Writable | Description                                        |
|---------------------------|----------|----------|----------------------------------------------------|
| `id`                      | GUID     | No       | Unique identifier                                  |
| `code`                    | string   | Yes      | Journal batch code                                 |
| `displayName`             | string   | Yes      | Display name for the journal                       |
| `balancingAccountId`      | GUID     | Yes      | Balancing G/L account ID                           |
| `balancingAccountNumber`  | string   | Yes      | Balancing G/L account number                       |
| `lastModifiedDateTime`    | datetime | No       | Last modification timestamp                        |

---

## Customer Payment Journal Lines (Sub-Resource)

### List Lines for a Journal

```http
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/customerPaymentJournals(b1c2d3e4-f5a6-7890-1234-567890abcdef)/customerPaymentJournalLines
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "@odata.context": "https://api.businesscentral.dynamics.com/v2.0/.../customerPaymentJournalLines",
  "value": [
    {
      "@odata.etag": "W/\"JzE5OzQ0NDQ0NDQ0NDQ7Q1BKTDE7Jw==\"",
      "id": "e4f5a6b7-c8d9-0123-4567-890abcdef123",
      "journalDisplayName": "Cash Receipts",
      "lineNumber": 10000,
      "customerNumber": "10000",
      "customerId": "d1e2f3a4-b5c6-7890-1234-567890abcdef",
      "contactId": "00000000-0000-0000-0000-000000000000",
      "postingDate": "2025-02-17",
      "documentNumber": "PMT-001",
      "externalDocumentNumber": "CHK-98765",
      "amount": 5000.00,
      "appliesToInvoiceId": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
      "appliesToInvoiceNumber": "SI-102345",
      "description": "Payment for Invoice SI-102345",
      "comment": "",
      "lastModifiedDateTime": "2025-02-17T10:15:00Z"
    },
    {
      "@odata.etag": "W/\"JzE5OzU1NTU1NTU1NTU7Q1BKTDI7Jw==\"",
      "id": "f5a6b7c8-d9e0-1234-5678-90abcdef1234",
      "journalDisplayName": "Cash Receipts",
      "lineNumber": 20000,
      "customerNumber": "20000",
      "customerId": "e2f3a4b5-c6d7-8901-2345-67890abcdef1",
      "contactId": "00000000-0000-0000-0000-000000000000",
      "postingDate": "2025-02-17",
      "documentNumber": "PMT-002",
      "externalDocumentNumber": "WIRE-44321",
      "amount": 8450.50,
      "appliesToInvoiceId": "b2c3d4e5-f6a7-8901-bcde-f23456789012",
      "appliesToInvoiceNumber": "SI-102400",
      "description": "Wire payment from Trey Research",
      "comment": "",
      "lastModifiedDateTime": "2025-02-17T10:20:00Z"
    }
  ]
}
```

### Create a Journal Line

```http
POST {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/customerPaymentJournals(b1c2d3e4-f5a6-7890-1234-567890abcdef)/customerPaymentJournalLines
Authorization: Bearer {access_token}
Content-Type: application/json
```

**Request Body:**

```json
{
  "customerNumber": "10000",
  "postingDate": "2025-02-17",
  "documentNumber": "PMT-003",
  "externalDocumentNumber": "CHK-99001",
  "amount": 2500.00,
  "appliesToInvoiceNumber": "SI-102500",
  "description": "Payment for Invoice SI-102500"
}
```

**Response (201 Created):**

```json
{
  "@odata.etag": "W/\"JzE5OzY2NjY2NjY2NjY7Q1BKTDk7Jw==\"",
  "id": "a6b7c8d9-e0f1-2345-6789-0abcdef12345",
  "journalDisplayName": "Cash Receipts",
  "lineNumber": 30000,
  "customerNumber": "10000",
  "customerId": "d1e2f3a4-b5c6-7890-1234-567890abcdef",
  "contactId": "00000000-0000-0000-0000-000000000000",
  "postingDate": "2025-02-17",
  "documentNumber": "PMT-003",
  "externalDocumentNumber": "CHK-99001",
  "amount": 2500.00,
  "appliesToInvoiceId": "c3d4e5f6-a7b8-9012-cdef-345678901234",
  "appliesToInvoiceNumber": "SI-102500",
  "description": "Payment for Invoice SI-102500",
  "comment": "",
  "lastModifiedDateTime": "2025-02-17T10:30:00Z"
}
```

### Update a Journal Line

```http
PATCH {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/customerPaymentJournals(b1c2d3e4-f5a6-7890-1234-567890abcdef)/customerPaymentJournalLines(e4f5a6b7-c8d9-0123-4567-890abcdef123)
Authorization: Bearer {access_token}
Content-Type: application/json
If-Match: W/"JzE5OzQ0NDQ0NDQ0NDQ7Q1BKTDE7Jw=="
```

**Request Body:**

```json
{
  "amount": 5500.00,
  "description": "Updated payment amount for Invoice SI-102345"
}
```

### Delete a Journal Line

```http
DELETE {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/customerPaymentJournals(b1c2d3e4-f5a6-7890-1234-567890abcdef)/customerPaymentJournalLines(e4f5a6b7-c8d9-0123-4567-890abcdef123)
Authorization: Bearer {access_token}
If-Match: W/"JzE5OzQ0NDQ0NDQ0NDQ7Q1BKTDE7Jw=="
```

**Response:** `204 No Content`

---

## Journal Line Fields

| Field                      | Type     | Writable | Description                                           |
|----------------------------|----------|----------|-------------------------------------------------------|
| `id`                       | GUID     | No       | Unique identifier                                     |
| `journalDisplayName`       | string   | No       | Parent journal display name                           |
| `lineNumber`               | integer  | No       | Auto-assigned line number                             |
| `customerNumber`           | string   | Yes      | Customer number                                       |
| `customerId`               | GUID     | Yes      | Customer ID                                           |
| `contactId`                | GUID     | Yes      | Contact ID (if applicable)                            |
| `postingDate`              | date     | Yes      | Date the payment is posted                            |
| `documentNumber`           | string   | Yes      | Document number for the payment                       |
| `externalDocumentNumber`   | string   | Yes      | External reference (e.g., check number)               |
| `amount`                   | decimal  | Yes      | Payment amount                                        |
| `appliesToInvoiceId`       | GUID     | Yes      | ID of the invoice this payment applies to             |
| `appliesToInvoiceNumber`   | string   | Yes      | Number of the invoice this payment applies to         |
| `description`              | string   | Yes      | Line description                                      |
| `comment`                  | string   | Yes      | Additional comments                                   |
| `lastModifiedDateTime`     | datetime | No       | Last modification timestamp                           |

---

## Bound Action: Post Journal

Post all lines in a customer payment journal:

```http
POST {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/customerPaymentJournals(b1c2d3e4-f5a6-7890-1234-567890abcdef)/Microsoft.NAV.post
Authorization: Bearer {access_token}
```

**Response:** `204 No Content`

After posting:
- All journal lines are applied as customer ledger entries
- The journal lines are removed from the journal
- The payments appear in the customer's ledger

> **Important:** Make sure all lines have valid customer numbers, posting dates, and amounts before calling post. Invalid lines will cause the entire batch to fail.

---

## ETag / If-Match

When updating (PATCH) or deleting (DELETE) journals or journal lines, you **must** include the `If-Match` header with the `@odata.etag` value from a prior GET response.

---

## Common Errors

| Status | Error                  | Cause & Resolution                                                                 |
|--------|------------------------|------------------------------------------------------------------------------------|
| `401`  | Unauthorized           | Access token is missing, expired, or invalid. Request a new token.                 |
| `404`  | Not Found              | The journal or line ID does not exist. Verify the GUIDs.                           |
| `400`  | Bad Request            | Invalid field values. Check customer numbers, dates, and amounts.                  |
| `409`  | Conflict               | ETag mismatch. GET the resource again and retry with the new ETag.                 |
