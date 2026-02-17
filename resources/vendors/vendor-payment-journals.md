# Vendor Payment Journals

Vendor payment journals are used to record and post payments to vendors. The structure mirrors customer payment journals — a journal header with payment lines.

---

## Base Endpoint

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/vendorPaymentJournals
```

---

## Bound Actions

| Action | Endpoint                                                              | Description                   |
|--------|-----------------------------------------------------------------------|-------------------------------|
| `post` | `POST .../vendorPaymentJournals({id})/Microsoft.NAV.post`             | Post all lines in the journal |

---

## Operations

### 1. List All Vendor Payment Journals

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/vendorPaymentJournals
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "value": [
    {
      "@odata.etag": "W/\"JzE5OzEyMzQ1Njc4OTA7Jw==\"",
      "id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
      "code": "PAYMENT",
      "displayName": "Payment Journal",
      "balancingAccountId": "d1e2f3a4-b5c6-7890-1234-567890abcdef",
      "balancingAccountNumber": "2910",
      "lastModifiedDateTime": "2024-06-15T10:00:00Z"
    }
  ]
}
```

### 2. Create a Vendor Payment Journal

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/vendorPaymentJournals
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "code": "VENDPMT",
  "displayName": "Vendor Payments"
}
```

### 3. Post a Vendor Payment Journal

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/vendorPaymentJournals({id})/Microsoft.NAV.post
Authorization: Bearer {access_token}
```

**Response:** `204 No Content`

---

## Journal Field Definitions

| Field                     | Type     | Editable | Description                         |
|---------------------------|----------|----------|-------------------------------------|
| `id`                      | GUID     | No       | Unique identifier                   |
| `code`                    | string   | Yes      | Journal batch code                  |
| `displayName`             | string   | Yes      | Journal display name                |
| `balancingAccountId`      | GUID     | Yes      | Default balancing account ID        |
| `balancingAccountNumber`  | string   | Yes      | Default balancing account number    |
| `lastModifiedDateTime`    | datetime | No       | Last modified timestamp             |

---

## Vendor Payments (Lines)

### Endpoint

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/vendorPaymentJournals({vjId})/vendorPayments
```

### Create a Vendor Payment

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/vendorPaymentJournals({vjId})/vendorPayments
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "vendorNumber": "10000",
  "postingDate": "2024-06-17",
  "amount": 1500.00,
  "appliesToInvoiceNumber": "PI-1001",
  "description": "Payment for invoice PI-1001"
}
```

**Response:** `201 Created`

```json
{
  "@odata.etag": "W/\"JzIwOzEyMzQ1Njc4OTA7Jw==\"",
  "id": "b2c3d4e5-f6a7-8901-bcde-f23456789012",
  "journalId": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "journalDisplayName": "Payment Journal",
  "lineNumber": 10000,
  "vendorId": "c3d4e5f6-a7b8-9012-cdef-345678901234",
  "vendorNumber": "10000",
  "postingDate": "2024-06-17",
  "documentNumber": "PMT-001",
  "externalDocumentNumber": "",
  "amount": 1500.00,
  "appliesToInvoiceId": "d4e5f6a7-b8c9-0123-defa-456789012345",
  "appliesToInvoiceNumber": "PI-1001",
  "description": "Payment for invoice PI-1001",
  "comment": "",
  "lastModifiedDateTime": "2024-06-17T09:00:00Z"
}
```

### Vendor Payment Field Definitions

| Field                     | Type     | Editable | Description                              |
|---------------------------|----------|----------|------------------------------------------|
| `id`                      | GUID     | No       | Unique identifier                        |
| `journalId`               | GUID     | No       | Parent journal ID                        |
| `journalDisplayName`      | string   | No       | Parent journal name                      |
| `lineNumber`              | integer  | No       | Line number (auto-assigned)              |
| `vendorId`                | GUID     | Yes      | Vendor identifier                        |
| `vendorNumber`            | string   | Yes      | Vendor number                            |
| `postingDate`             | date     | Yes      | Posting date                             |
| `documentNumber`          | string   | Yes      | Document number                          |
| `externalDocumentNumber`  | string   | Yes      | External document number                 |
| `amount`                  | decimal  | Yes      | Payment amount                           |
| `appliesToInvoiceId`      | GUID     | Yes      | ID of the invoice this applies to        |
| `appliesToInvoiceNumber`  | string   | Yes      | Number of the invoice this applies to    |
| `description`             | string   | Yes      | Payment description                      |
| `comment`                 | string   | Yes      | Comment                                  |
| `lastModifiedDateTime`    | datetime | No       | Last modified timestamp                  |

---

## Sub-Resources on Vendor Payments

| Sub-Resource       | Path                                                    | Methods                    |
|--------------------|---------------------------------------------------------|----------------------------|
| dimensionSetLines  | `.../vendorPayments({id})/dimensionSetLines`            | GET, POST, PATCH, DELETE   |
| applyVendorEntries | `.../vendorPayments({id})/applyVendorEntries`           | GET, PATCH                 |

---

## Common Errors

| Status | Error                  | Cause & Resolution                                                                 |
|--------|------------------------|------------------------------------------------------------------------------------|
| `401`  | Unauthorized           | Access token is missing, expired, or invalid. Request a new token.                 |
| `404`  | Not Found              | The journal or payment line does not exist.                                        |
| `400`  | Bad Request            | Invalid field values. Check vendor number, dates, and amounts.                     |
| `409`  | Conflict               | ETag mismatch — the record was modified since your last GET. Fetch and retry.      |
