# Sales Credit Memos

Sales credit memos are used to reverse or correct sales invoices. They can be created manually or generated automatically when canceling an invoice. Draft credit memos can be posted using a bound action.

---

## Base Endpoint

```
{{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/salesCreditMemos
```

---

## Operations

### 1. List All Sales Credit Memos

```http
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/salesCreditMemos
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "@odata.context": "https://api.businesscentral.dynamics.com/v2.0/.../salesCreditMemos",
  "value": [
    {
      "@odata.etag": "W/\"JzE5OzExO1NDTTE7Jw==\"",
      "id": "a1b1c1d1-1111-2222-3333-444444444444",
      "number": "SCM-1001",
      "externalDocumentNumber": "RMA-2025-001",
      "creditMemoDate": "2025-02-15",
      "dueDate": "2025-03-15",
      "documentDate": "2025-02-15",
      "customerId": "d1e2f3a4-b5c6-7890-1234-567890abcdef",
      "customerNumber": "10000",
      "customerName": "Adatum Corporation",
      "billToName": "Adatum Corporation",
      "billToCustomerId": "d1e2f3a4-b5c6-7890-1234-567890abcdef",
      "billToCustomerNumber": "10000",
      "shipToName": "Adatum Corporation",
      "shipToContact": "Robert Townes",
      "shipToAddressLine1": "192 Market Square",
      "shipToAddressLine2": "Suite 300",
      "shipToCity": "Atlanta",
      "shipToState": "GA",
      "shipToCountry": "US",
      "shipToPostCode": "31772",
      "currencyId": "00000000-0000-0000-0000-000000000000",
      "currencyCode": "USD",
      "paymentTermsId": "a1a1a1a1-b2b2-c3c3-d4d4-e5e5e5e5e5e5",
      "salesperson": "JR",
      "pricesIncludeTax": false,
      "discountAmount": 0.00,
      "discountAppliedBeforeTax": true,
      "totalAmountExcludingTax": 1500.00,
      "totalTaxAmount": 112.50,
      "totalAmountIncludingTax": 1612.50,
      "status": "Open",
      "lastModifiedDateTime": "2025-02-15T14:30:00Z"
    }
  ]
}
```

---

### 2. Get a Single Sales Credit Memo

```http
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/salesCreditMemos(a1b1c1d1-1111-2222-3333-444444444444)
Authorization: Bearer {access_token}
Accept: application/json
```

---

### 3. Create a Sales Credit Memo

```http
POST {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/salesCreditMemos
Authorization: Bearer {access_token}
Content-Type: application/json
```

**Request Body:**

```json
{
  "customerNumber": "10000",
  "externalDocumentNumber": "RMA-2025-002",
  "creditMemoDate": "2025-02-17",
  "salesperson": "JR",
  "currencyCode": "USD"
}
```

**Response (201 Created):**

```json
{
  "@odata.etag": "W/\"JzIwOzIyO1NDTTI7Jw==\"",
  "id": "b2c2d2e2-2222-3333-4444-555555555555",
  "number": "SCM-DRAFT-001",
  "externalDocumentNumber": "RMA-2025-002",
  "creditMemoDate": "2025-02-17",
  "dueDate": "2025-03-17",
  "documentDate": "2025-02-17",
  "customerId": "d1e2f3a4-b5c6-7890-1234-567890abcdef",
  "customerNumber": "10000",
  "customerName": "Adatum Corporation",
  "billToName": "Adatum Corporation",
  "billToCustomerId": "d1e2f3a4-b5c6-7890-1234-567890abcdef",
  "billToCustomerNumber": "10000",
  "shipToName": "Adatum Corporation",
  "shipToContact": "",
  "shipToAddressLine1": "192 Market Square",
  "shipToAddressLine2": "Suite 300",
  "shipToCity": "Atlanta",
  "shipToState": "GA",
  "shipToCountry": "US",
  "shipToPostCode": "31772",
  "currencyId": "00000000-0000-0000-0000-000000000000",
  "currencyCode": "USD",
  "paymentTermsId": "a1a1a1a1-b2b2-c3c3-d4d4-e5e5e5e5e5e5",
  "salesperson": "JR",
  "pricesIncludeTax": false,
  "discountAmount": 0.00,
  "discountAppliedBeforeTax": true,
  "totalAmountExcludingTax": 0.00,
  "totalTaxAmount": 0.00,
  "totalAmountIncludingTax": 0.00,
  "status": "Draft",
  "lastModifiedDateTime": "2025-02-17T10:00:00Z"
}
```

---

### 4. Update a Sales Credit Memo

```http
PATCH {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/salesCreditMemos(b2c2d2e2-2222-3333-4444-555555555555)
Authorization: Bearer {access_token}
Content-Type: application/json
If-Match: W/"JzIwOzIyO1NDTTI7Jw=="
```

**Request Body:**

```json
{
  "externalDocumentNumber": "RMA-2025-002-REV1"
}
```

---

### 5. Delete a Sales Credit Memo

```http
DELETE {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/salesCreditMemos(b2c2d2e2-2222-3333-4444-555555555555)
Authorization: Bearer {access_token}
If-Match: W/"JzIwOzIyO1NDTTI7Jw=="
```

**Response:** `204 No Content`

---

## Header Fields

| Field                        | Type     | Writable | Description                                    |
|------------------------------|----------|----------|------------------------------------------------|
| `id`                         | GUID     | No       | Unique identifier                              |
| `number`                     | string   | Yes*     | Credit memo number                             |
| `externalDocumentNumber`     | string   | Yes      | External reference (e.g., RMA number)          |
| `creditMemoDate`             | date     | Yes      | Credit memo date                               |
| `dueDate`                    | date     | Yes      | Due date                                       |
| `documentDate`               | date     | Yes      | Document date                                  |
| `customerId`                 | GUID     | Yes      | Customer ID                                    |
| `customerNumber`             | string   | Yes      | Customer number                                |
| `customerName`               | string   | No       | Customer name (read-only)                      |
| `billToName`                 | string   | Yes      | Bill-to name                                   |
| `billToCustomerId`           | GUID     | Yes      | Bill-to customer ID                            |
| `billToCustomerNumber`       | string   | Yes      | Bill-to customer number                        |
| `shipToName`                 | string   | Yes      | Ship-to name                                   |
| `shipToContact`              | string   | Yes      | Ship-to contact                                |
| `shipToAddressLine1`         | string   | Yes      | Ship-to address                                |
| `shipToAddressLine2`         | string   | Yes      | Ship-to address line 2                         |
| `shipToCity`                 | string   | Yes      | Ship-to city                                   |
| `shipToState`                | string   | Yes      | Ship-to state                                  |
| `shipToCountry`              | string   | Yes      | Ship-to country                                |
| `shipToPostCode`             | string   | Yes      | Ship-to postal code                            |
| `currencyId`                 | GUID     | Yes      | Currency ID                                    |
| `currencyCode`               | string   | Yes      | Currency code                                  |
| `paymentTermsId`             | GUID     | Yes      | Payment terms ID                               |
| `salesperson`                | string   | Yes      | Salesperson code                               |
| `pricesIncludeTax`           | boolean  | Yes      | Whether prices include tax                     |
| `discountAmount`             | decimal  | Yes      | Document discount amount                       |
| `discountAppliedBeforeTax`   | boolean  | No       | Whether discount is pre-tax                    |
| `totalAmountExcludingTax`    | decimal  | No       | Total excl. tax (read-only)                    |
| `totalTaxAmount`             | decimal  | No       | Total tax (read-only)                          |
| `totalAmountIncludingTax`    | decimal  | No       | Total incl. tax (read-only)                    |
| `status`                     | string   | No       | `Draft`, `In Review`, `Open`, `Canceled`       |
| `lastModifiedDateTime`       | datetime | No       | Last modification timestamp                    |

---

## Sales Credit Memo Lines

### List Lines

```http
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/salesCreditMemos(a1b1c1d1-1111-2222-3333-444444444444)/salesCreditMemoLines
Authorization: Bearer {access_token}
```

**Response:**

```json
{
  "value": [
    {
      "@odata.etag": "W/\"JzE5O1NDTUwxOw==\"",
      "id": "e5e5e5e5-f5f5-a5a5-b5b5-c5c5c5c5c5c5",
      "documentId": "a1b1c1d1-1111-2222-3333-444444444444",
      "sequence": 10000,
      "itemId": "b1c2d3e4-f5a6-7890-abcd-111111111111",
      "accountId": "00000000-0000-0000-0000-000000000000",
      "lineType": "Item",
      "lineObjectNumber": "1000",
      "description": "Bicycle",
      "unitOfMeasureId": "e4f5a6b7-c8d9-0123-4567-444444444444",
      "unitOfMeasureCode": "PCS",
      "unitPrice": 1500.00,
      "quantity": 1,
      "discountAmount": 0.00,
      "discountPercent": 0,
      "discountAppliedBeforeTax": true,
      "amountExcludingTax": 1500.00,
      "taxCode": "TAXABLE",
      "taxPercent": 7.5,
      "totalTaxAmount": 112.50,
      "amountIncludingTax": 1612.50
    }
  ]
}
```

### Create / Update / Delete Lines

```
POST   .../salesCreditMemos({id})/salesCreditMemoLines
PATCH  .../salesCreditMemos({id})/salesCreditMemoLines({lineId})   (with If-Match)
DELETE .../salesCreditMemos({id})/salesCreditMemoLines({lineId})   (with If-Match)
```

---

## Bound Actions

### Post a Draft Credit Memo

```http
POST {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/salesCreditMemos(b2c2d2e2-2222-3333-4444-555555555555)/Microsoft.NAV.post
Authorization: Bearer {access_token}
```

**Response:** `204 No Content`

### Send Credit Memo by Email

```http
POST {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/salesCreditMemos(a1b1c1d1-1111-2222-3333-444444444444)/Microsoft.NAV.send
Authorization: Bearer {access_token}
```

**Response:** `204 No Content`

---

## Filter Examples

```http
# Draft credit memos
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/salesCreditMemos?$filter=status eq 'Draft'

# Posted (open) credit memos
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/salesCreditMemos?$filter=status eq 'Open'

# For a specific customer
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/salesCreditMemos?$filter=customerNumber eq '10000'
```

---

## ETag / If-Match

When updating (PATCH) or deleting (DELETE) draft credit memos or their lines, include the `If-Match` header with the `@odata.etag` from a prior GET.

---

## Common Errors

| Status | Error                  | Cause & Resolution                                                                 |
|--------|------------------------|------------------------------------------------------------------------------------|
| `401`  | Unauthorized           | Access token is missing, expired, or invalid. Request a new token.                 |
| `404`  | Not Found              | The credit memo or line ID does not exist.                                         |
| `400`  | Bad Request            | Invalid field values, or attempting to modify a posted credit memo.                 |
| `409`  | Conflict               | ETag mismatch. GET the record again and retry with the new ETag.                   |
