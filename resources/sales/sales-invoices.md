# Sales Invoices

Sales invoices represent bills sent to customers for goods or services. Draft invoices can be edited and then posted using a bound action. Once posted, invoices become read-only and can be sent to customers by email or canceled. This is one of the most important API resources for BC integrations.

---

## Base Endpoint

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesInvoices
```

---

## Status Values

| Status        | Description                                          |
|---------------|------------------------------------------------------|
| `Draft`       | Invoice is being prepared, editable                  |
| `In Review`   | Invoice is under approval workflow                   |
| `Open`        | Invoice is posted and awaiting payment               |
| `Paid`        | Invoice has been fully paid                          |
| `Canceled`    | Invoice has been canceled                            |
| `Corrective`  | Invoice is a corrective document                     |

---

## Operations

### 1. List All Sales Invoices

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesInvoices
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "@odata.context": "https://api.businesscentral.dynamics.com/v2.0/.../salesInvoices",
  "value": [
    {
      "@odata.etag": "W/\"JzE5OzExO1NJMTsn\"",
      "id": "a1a1a1a1-b1b1-c1c1-d1d1-e1e1e1e1e1e1",
      "number": "SI-103001",
      "externalDocumentNumber": "PO-ADT-2025-100",
      "invoiceDate": "2025-02-10",
      "dueDate": "2025-03-12",
      "documentDate": "2025-02-10",
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
      "status": "Open",
      "remainingAmount": 4837.50,
      "lastModifiedDateTime": "2025-02-10T16:00:00Z"
    },
    {
      "@odata.etag": "W/\"JzE5OzIyO1NJMjsn\"",
      "id": "b2b2b2b2-c2c2-d2d2-e2e2-f2f2f2f2f2f2",
      "number": "SI-DRAFT-001",
      "externalDocumentNumber": "",
      "invoiceDate": "2025-02-17",
      "dueDate": "2025-03-17",
      "documentDate": "2025-02-17",
      "customerId": "e2f3a4b5-c6d7-8901-2345-67890abcdef1",
      "customerNumber": "20000",
      "customerName": "Trey Research",
      "billToName": "Trey Research",
      "billToCustomerId": "e2f3a4b5-c6d7-8901-2345-67890abcdef1",
      "billToCustomerNumber": "20000",
      "shipToName": "Trey Research",
      "shipToContact": "",
      "shipToAddressLine1": "1 Main Street",
      "shipToAddressLine2": "",
      "shipToCity": "Dallas",
      "shipToState": "TX",
      "shipToCountry": "US",
      "shipToPostCode": "74532",
      "currencyId": "00000000-0000-0000-0000-000000000000",
      "currencyCode": "USD",
      "paymentTermsId": "a1a1a1a1-b2b2-c3c3-d4d4-e5e5e5e5e5e5",
      "shipmentMethodId": "00000000-0000-0000-0000-000000000000",
      "salesperson": "PS",
      "pricesIncludeTax": false,
      "discountAmount": 0.00,
      "discountAppliedBeforeTax": true,
      "totalAmountExcludingTax": 1700.00,
      "totalTaxAmount": 127.50,
      "totalAmountIncludingTax": 1827.50,
      "status": "Draft",
      "remainingAmount": 0.00,
      "lastModifiedDateTime": "2025-02-17T09:00:00Z"
    }
  ]
}
```

---

### 2. Get a Single Sales Invoice

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesInvoices(a1a1a1a1-b1b1-c1c1-d1d1-e1e1e1e1e1e1)
Authorization: Bearer {access_token}
Accept: application/json
```

---

### 3. Create a Sales Invoice (Draft)

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesInvoices
Authorization: Bearer {access_token}
Content-Type: application/json
```

**Request Body:**

```json
{
  "customerNumber": "10000",
  "externalDocumentNumber": "PO-ADT-2025-200",
  "invoiceDate": "2025-02-17",
  "dueDate": "2025-03-17",
  "salesperson": "JR",
  "currencyCode": "USD"
}
```

**Response (201 Created):**

```json
{
  "@odata.etag": "W/\"JzIwOzMzO1NJMzsn\"",
  "id": "c3c3c3c3-d3d3-e3e3-f3f3-a3a3a3a3a3a3",
  "number": "SI-DRAFT-002",
  "externalDocumentNumber": "PO-ADT-2025-200",
  "invoiceDate": "2025-02-17",
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
  "shipmentMethodId": "f6f6f6f6-a7a7-b8b8-c9c9-d0d0d0d0d0d0",
  "salesperson": "JR",
  "pricesIncludeTax": false,
  "discountAmount": 0.00,
  "discountAppliedBeforeTax": true,
  "totalAmountExcludingTax": 0.00,
  "totalTaxAmount": 0.00,
  "totalAmountIncludingTax": 0.00,
  "status": "Draft",
  "remainingAmount": 0.00,
  "lastModifiedDateTime": "2025-02-17T10:00:00Z"
}
```

---

### 4. Update a Sales Invoice

> **Note:** Only **Draft** invoices can be updated. Posted invoices are read-only.

```http
PATCH {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesInvoices(b2b2b2b2-c2c2-d2d2-e2e2-f2f2f2f2f2f2)
Authorization: Bearer {access_token}
Content-Type: application/json
If-Match: W/"JzE5OzIyO1NJMjsn"
```

**Request Body:**

```json
{
  "externalDocumentNumber": "PO-TREY-2025-050",
  "dueDate": "2025-04-15"
}
```

---

### 5. Delete a Sales Invoice

> **Note:** Only **Draft** invoices can be deleted. Posted invoices cannot be deleted — use `cancelAndSend` instead.

```http
DELETE {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesInvoices(b2b2b2b2-c2c2-d2d2-e2e2-f2f2f2f2f2f2)
Authorization: Bearer {access_token}
If-Match: W/"JzE5OzIyO1NJMjsn"
```

**Response:** `204 No Content`

---

## Header Fields

| Field                        | Type     | Writable | Description                                    |
|------------------------------|----------|----------|------------------------------------------------|
| `id`                         | GUID     | No       | Unique identifier                              |
| `number`                     | string   | Yes*     | Invoice number (auto-generated if omitted)     |
| `externalDocumentNumber`     | string   | Yes      | External reference (e.g., customer PO)         |
| `invoiceDate`                | date     | Yes      | Invoice date                                   |
| `dueDate`                    | date     | Yes      | Payment due date                               |
| `documentDate`               | date     | Yes      | Document date                                  |
| `customerId`                 | GUID     | Yes      | Customer ID                                    |
| `customerNumber`             | string   | Yes      | Customer number                                |
| `customerName`               | string   | No       | Customer name (read-only)                      |
| `billToName`                 | string   | Yes      | Bill-to name                                   |
| `billToCustomerId`           | GUID     | Yes      | Bill-to customer ID                            |
| `billToCustomerNumber`       | string   | Yes      | Bill-to customer number                        |
| `shipToName`                 | string   | Yes      | Ship-to name                                   |
| `shipToContact`              | string   | Yes      | Ship-to contact                                |
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
| `discountAppliedBeforeTax`   | boolean  | No       | Whether discount is pre-tax                    |
| `totalAmountExcludingTax`    | decimal  | No       | Total excl. tax (read-only)                    |
| `totalTaxAmount`             | decimal  | No       | Total tax (read-only)                          |
| `totalAmountIncludingTax`    | decimal  | No       | Total incl. tax (read-only)                    |
| `status`                     | string   | No       | `Draft`, `In Review`, `Open`, `Paid`, `Canceled`, `Corrective` |
| `remainingAmount`            | decimal  | No       | Amount remaining to be paid (read-only)        |
| `lastModifiedDateTime`       | datetime | No       | Last modification timestamp                    |

---

## Sales Invoice Lines (Sub-Resource)

### List Invoice Lines

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesInvoices(a1a1a1a1-b1b1-c1c1-d1d1-e1e1e1e1e1e1)/salesInvoiceLines
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "value": [
    {
      "@odata.etag": "W/\"JzE5O1NJTDEn\"",
      "id": "d4d4d4d4-e4e4-f4f4-a4a4-b4b4b4b4b4b4",
      "documentId": "a1a1a1a1-b1b1-c1c1-d1d1-e1e1e1e1e1e1",
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
      "shipmentDate": "2025-02-15"
    }
  ]
}
```

### Create an Invoice Line

> Only works on **Draft** invoices.

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesInvoices(b2b2b2b2-c2c2-d2d2-e2e2-f2f2f2f2f2f2)/salesInvoiceLines
Authorization: Bearer {access_token}
Content-Type: application/json
```

**Request Body:**

```json
{
  "lineType": "Item",
  "lineObjectNumber": "1001",
  "description": "ATHENS Desk",
  "quantity": 2,
  "unitPrice": 850.00
}
```

### Update / Delete Invoice Lines

Same PATCH (with If-Match) and DELETE patterns as other sales documents:

```
PATCH  .../salesInvoices({id})/salesInvoiceLines({lineId})
DELETE .../salesInvoices({id})/salesInvoiceLines({lineId})
```

---

## Bound Actions

### Post a Draft Invoice

Transitions a Draft invoice to Open (posted):

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesInvoices(b2b2b2b2-c2c2-d2d2-e2e2-f2f2f2f2f2f2)/Microsoft.NAV.post
Authorization: Bearer {access_token}
```

**Response:** `204 No Content`

After posting, the invoice status changes from `Draft` to `Open`, a posted invoice number is assigned, and the invoice becomes read-only.

### Send Invoice by Email

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesInvoices(a1a1a1a1-b1b1-c1c1-d1d1-e1e1e1e1e1e1)/Microsoft.NAV.send
Authorization: Bearer {access_token}
```

**Response:** `204 No Content`

### Cancel and Send Cancellation

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesInvoices(a1a1a1a1-b1b1-c1c1-d1d1-e1e1e1e1e1e1)/Microsoft.NAV.cancelAndSend
Authorization: Bearer {access_token}
```

**Response:** `204 No Content` — Creates a corrective credit memo and sends the cancellation to the customer.

---

## Filter Examples

```http
# Draft invoices only
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesInvoices?$filter=status eq 'Draft'

# Open (posted, unpaid) invoices
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesInvoices?$filter=status eq 'Open'

# Paid invoices
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesInvoices?$filter=status eq 'Paid'

# Invoices with remaining balance
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesInvoices?$filter=remainingAmount gt 0

# Invoices for a specific customer
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesInvoices?$filter=customerNumber eq '10000'

# Overdue invoices
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesInvoices?$filter=status eq 'Open' and dueDate lt 2025-02-17

# Invoices with line details
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesInvoices?$expand=salesInvoiceLines&$filter=status eq 'Open'

# Combined: open invoices, key fields, sorted by due date
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesInvoices?$filter=status eq 'Open'&$select=number,customerName,invoiceDate,dueDate,totalAmountIncludingTax,remainingAmount&$orderby=dueDate asc
```

---

## ETag / If-Match

When updating (PATCH) or deleting (DELETE) **draft** invoices or their lines, include the `If-Match` header with the `@odata.etag` from a prior GET.

> **Posted invoices cannot be edited or deleted.** Use the `cancelAndSend` action instead.

---

## Common Errors

| Status | Error                  | Cause & Resolution                                                                         |
|--------|------------------------|-------------------------------------------------------------------------------------------|
| `401`  | Unauthorized           | Access token is missing, expired, or invalid. Request a new token.                         |
| `404`  | Not Found              | The invoice ID does not exist.                                                             |
| `400`  | Bad Request            | Invalid field values, or attempting to edit a posted invoice.                               |
| `409`  | Conflict               | ETag mismatch. GET the record again and retry with the new ETag.                           |
