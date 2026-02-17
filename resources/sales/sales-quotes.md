# Sales Quotes

Sales quotes are preliminary offers sent to customers before an order or invoice is created. Quotes can be converted to sales orders or sales invoices using bound actions. They support line items, customer details, and all standard sales document fields.

---

## Base Endpoint

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesQuotes
```

---

## Operations

### 1. List All Sales Quotes

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesQuotes
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "@odata.context": "https://api.businesscentral.dynamics.com/v2.0/.../salesQuotes",
  "value": [
    {
      "@odata.etag": "W/\"JzE5OzExMTExMTsxMDAwO1NRMTsn\"",
      "id": "a1b1c1d1-e1f1-1111-2222-333333333333",
      "number": "SQ-1001",
      "externalDocumentNumber": "RFQ-2025-001",
      "documentDate": "2025-02-10",
      "dueDate": "2025-03-12",
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
      "shipmentMethodId": "f6f6f6f6-a7a7-b8b8-c9c9-d0d0d0d0d0d0",
      "salesperson": "JR",
      "pricesIncludeTax": false,
      "discountAmount": 0.00,
      "discountAppliedBeforeTax": true,
      "totalAmountExcludingTax": 4500.00,
      "totalTaxAmount": 337.50,
      "totalAmountIncludingTax": 4837.50,
      "status": "Draft",
      "sentDate": "0001-01-01T00:00:00Z",
      "validUntilDate": "2025-03-12",
      "acceptedDate": "0001-01-01",
      "lastModifiedDateTime": "2025-02-10T14:00:00Z"
    }
  ]
}
```

---

### 2. Get a Single Sales Quote

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesQuotes(a1b1c1d1-e1f1-1111-2222-333333333333)
Authorization: Bearer {access_token}
Accept: application/json
```

---

### 3. Create a Sales Quote

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesQuotes
Authorization: Bearer {access_token}
Content-Type: application/json
```

**Request Body:**

```json
{
  "customerNumber": "10000",
  "externalDocumentNumber": "RFQ-2025-002",
  "documentDate": "2025-02-17",
  "dueDate": "2025-03-17",
  "salesperson": "JR",
  "currencyCode": "USD"
}
```

**Response (201 Created):**

```json
{
  "@odata.etag": "W/\"JzIwOzIyMjIyMjsyMDAwO1NRMjsn\"",
  "id": "b2c2d2e2-f2a2-2222-3333-444444444444",
  "number": "SQ-1002",
  "externalDocumentNumber": "RFQ-2025-002",
  "documentDate": "2025-02-17",
  "dueDate": "2025-03-17",
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
  "shipmentMethodId": "f6f6f6f6-a7a7-b8b8-c9c9-d0d0d0d0d0d0",
  "salesperson": "JR",
  "pricesIncludeTax": false,
  "discountAmount": 0.00,
  "discountAppliedBeforeTax": true,
  "totalAmountExcludingTax": 0.00,
  "totalTaxAmount": 0.00,
  "totalAmountIncludingTax": 0.00,
  "status": "Draft",
  "sentDate": "0001-01-01T00:00:00Z",
  "validUntilDate": "2025-03-17",
  "acceptedDate": "0001-01-01",
  "lastModifiedDateTime": "2025-02-17T10:00:00Z"
}
```

---

### 4. Update a Sales Quote

```http
PATCH {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesQuotes(a1b1c1d1-e1f1-1111-2222-333333333333)
Authorization: Bearer {access_token}
Content-Type: application/json
If-Match: W/"JzE5OzExMTExMTsxMDAwO1NRMTsn"
```

**Request Body:**

```json
{
  "dueDate": "2025-04-10",
  "salesperson": "PS"
}
```

---

### 5. Delete a Sales Quote

```http
DELETE {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesQuotes(a1b1c1d1-e1f1-1111-2222-333333333333)
Authorization: Bearer {access_token}
If-Match: W/"JzE5OzExMTExMTsxMDAwO1NRMTsn"
```

**Response:** `204 No Content`

---

## Header Fields

| Field                        | Type     | Writable | Description                                    |
|------------------------------|----------|----------|------------------------------------------------|
| `id`                         | GUID     | No       | Unique identifier                              |
| `number`                     | string   | Yes*     | Quote number (auto-generated if omitted)       |
| `externalDocumentNumber`     | string   | Yes      | External reference number                      |
| `documentDate`               | date     | Yes      | Document date                                  |
| `dueDate`                    | date     | Yes      | Due date                                       |
| `customerId`                 | GUID     | Yes      | Sell-to customer ID                            |
| `customerNumber`             | string   | Yes      | Sell-to customer number                        |
| `customerName`               | string   | No       | Sell-to customer name (read-only)              |
| `billToName`                 | string   | Yes      | Bill-to name                                   |
| `billToCustomerId`           | GUID     | Yes      | Bill-to customer ID                            |
| `billToCustomerNumber`       | string   | Yes      | Bill-to customer number                        |
| `shipToName`                 | string   | Yes      | Ship-to name                                   |
| `shipToContact`              | string   | Yes      | Ship-to contact person                         |
| `shipToAddressLine1`         | string   | Yes      | Ship-to address line 1                         |
| `shipToAddressLine2`         | string   | Yes      | Ship-to address line 2                         |
| `shipToCity`                 | string   | Yes      | Ship-to city                                   |
| `shipToState`                | string   | Yes      | Ship-to state                                  |
| `shipToCountry`              | string   | Yes      | Ship-to country                                |
| `shipToPostCode`             | string   | Yes      | Ship-to postal code                            |
| `currencyId`                 | GUID     | Yes      | Currency ID                                    |
| `currencyCode`               | string   | Yes      | Currency code                                  |
| `paymentTermsId`             | GUID     | Yes      | Payment terms ID                               |
| `shipmentMethodId`           | GUID     | Yes      | Shipment method ID                             |
| `salesperson`                | string   | Yes      | Salesperson code                               |
| `pricesIncludeTax`           | boolean  | Yes      | Whether prices include tax                     |
| `discountAmount`             | decimal  | Yes      | Invoice discount amount                        |
| `discountAppliedBeforeTax`   | boolean  | No       | Whether discount is applied before tax         |
| `totalAmountExcludingTax`    | decimal  | No       | Total excl. tax (read-only)                    |
| `totalTaxAmount`             | decimal  | No       | Total tax amount (read-only)                   |
| `totalAmountIncludingTax`    | decimal  | No       | Total incl. tax (read-only)                    |
| `status`                     | string   | No       | `Draft`, `Sent`                                |
| `sentDate`                   | datetime | No       | Date the quote was sent                        |
| `validUntilDate`             | date     | Yes      | Quote valid until date                         |
| `acceptedDate`               | date     | No       | Date the quote was accepted                    |
| `lastModifiedDateTime`       | datetime | No       | Last modification timestamp                    |

---

## Sales Quote Lines (Sub-Resource)

### List Lines

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesQuotes(a1b1c1d1-e1f1-1111-2222-333333333333)/salesQuoteLines
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "value": [
    {
      "@odata.etag": "W/\"JzE5O1NRTDEn\"",
      "id": "c3d3e3f3-a3b3-3333-4444-555555555555",
      "documentId": "a1b1c1d1-e1f1-1111-2222-333333333333",
      "sequence": 10000,
      "itemId": "b1c2d3e4-f5a6-7890-abcd-111111111111",
      "accountId": "00000000-0000-0000-0000-000000000000",
      "lineType": "Item",
      "lineObjectNumber": "1000",
      "description": "Bicycle",
      "unitOfMeasureId": "e4f5a6b7-c8d9-0123-4567-444444444444",
      "unitOfMeasureCode": "PCS",
      "unitPrice": 1500.00,
      "quantity": 3,
      "discountAmount": 0.00,
      "discountPercent": 0,
      "discountAppliedBeforeTax": true,
      "amountExcludingTax": 4500.00,
      "taxCode": "TAXABLE",
      "taxPercent": 7.5,
      "totalTaxAmount": 337.50,
      "amountIncludingTax": 4837.50,
      "netAmount": 4500.00,
      "netTaxAmount": 337.50,
      "netAmountIncludingTax": 4837.50,
      "shipmentDate": "2025-03-01"
    }
  ]
}
```

### Create a Quote Line

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesQuotes(a1b1c1d1-e1f1-1111-2222-333333333333)/salesQuoteLines
Authorization: Bearer {access_token}
Content-Type: application/json
```

**Request Body:**

```json
{
  "lineType": "Item",
  "lineObjectNumber": "1001",
  "quantity": 2,
  "unitPrice": 850.00
}
```

### Update a Quote Line

```http
PATCH {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesQuotes(a1b1c1d1-e1f1-1111-2222-333333333333)/salesQuoteLines(c3d3e3f3-a3b3-3333-4444-555555555555)
Authorization: Bearer {access_token}
Content-Type: application/json
If-Match: W/"JzE5O1NRTDEn"
```

**Request Body:**

```json
{
  "quantity": 5,
  "discountPercent": 10
}
```

### Delete a Quote Line

```http
DELETE {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesQuotes(a1b1c1d1-e1f1-1111-2222-333333333333)/salesQuoteLines(c3d3e3f3-a3b3-3333-4444-555555555555)
Authorization: Bearer {access_token}
If-Match: W/"JzE5O1NRTDEn"
```

---

## Line Fields

| Field                        | Type     | Writable | Description                                    |
|------------------------------|----------|----------|------------------------------------------------|
| `id`                         | GUID     | No       | Unique line identifier                         |
| `documentId`                 | GUID     | No       | Parent quote ID                                |
| `sequence`                   | integer  | No       | Line sequence number                           |
| `itemId`                     | GUID     | Yes      | Item ID                                        |
| `accountId`                  | GUID     | Yes      | G/L Account ID (for account-type lines)        |
| `lineType`                   | string   | Yes      | `Item`, `Account`, `Resource`, `Charge`, `Fixed Asset`, `Comment` |
| `lineObjectNumber`           | string   | Yes      | Item/account number                            |
| `description`                | string   | Yes      | Line description                               |
| `unitOfMeasureId`            | GUID     | Yes      | Unit of measure ID                             |
| `unitOfMeasureCode`          | string   | Yes      | Unit of measure code                           |
| `unitPrice`                  | decimal  | Yes      | Unit price                                     |
| `quantity`                   | decimal  | Yes      | Quantity                                       |
| `discountAmount`             | decimal  | Yes      | Line discount amount                           |
| `discountPercent`            | decimal  | Yes      | Line discount percentage                       |
| `discountAppliedBeforeTax`   | boolean  | No       | Whether discount is pre-tax                    |
| `amountExcludingTax`         | decimal  | No       | Line amount excl. tax (read-only)              |
| `taxCode`                    | string   | Yes      | Tax group code                                 |
| `taxPercent`                 | decimal  | No       | Tax percentage (read-only)                     |
| `totalTaxAmount`             | decimal  | No       | Line tax amount (read-only)                    |
| `amountIncludingTax`         | decimal  | No       | Line amount incl. tax (read-only)              |
| `shipmentDate`               | date     | Yes      | Expected shipment date                         |

---

## Bound Actions

### Convert Quote to Invoice

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesQuotes(a1b1c1d1-e1f1-1111-2222-333333333333)/Microsoft.NAV.makeInvoice
Authorization: Bearer {access_token}
```

**Response:** `204 No Content` — A new sales invoice is created from this quote.

### Convert Quote to Order

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesQuotes(a1b1c1d1-e1f1-1111-2222-333333333333)/Microsoft.NAV.makeOrder
Authorization: Bearer {access_token}
```

**Response:** `204 No Content` — A new sales order is created from this quote.

### Send Quote by Email

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesQuotes(a1b1c1d1-e1f1-1111-2222-333333333333)/Microsoft.NAV.send
Authorization: Bearer {access_token}
```

**Response:** `204 No Content` — The quote is sent to the customer's email.

---

## ETag / If-Match

When updating (PATCH) or deleting (DELETE) quotes or lines, include the `If-Match` header with the `@odata.etag` from a prior GET.

---

## Common Errors

| Status | Error                  | Cause & Resolution                                                                 |
|--------|------------------------|------------------------------------------------------------------------------------|
| `401`  | Unauthorized           | Access token is missing, expired, or invalid. Request a new token.                 |
| `404`  | Not Found              | The quote or line ID does not exist.                                               |
| `400`  | Bad Request            | Invalid field values or missing required fields.                                   |
| `409`  | Conflict               | ETag mismatch. GET the record again and retry with the new ETag.                   |
