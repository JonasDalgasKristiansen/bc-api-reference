# Purchase Invoices

Purchase invoices represent bills received from vendors for goods or services. Draft invoices can be edited and then posted using a bound action. Once posted, purchase invoices become read-only.

---

## Base Endpoint

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/purchaseInvoices
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

---

## Operations

### 1. List All Purchase Invoices

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/purchaseInvoices
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "@odata.context": "https://api.businesscentral.dynamics.com/v2.0/.../purchaseInvoices",
  "value": [
    {
      "@odata.etag": "W/\"JzE5OzExO1BJMTsn\"",
      "id": "a1a1a1a1-aaaa-bbbb-cccc-111111111111",
      "number": "PI-5001",
      "invoiceDate": "2025-02-10",
      "dueDate": "2025-03-12",
      "documentDate": "2025-02-10",
      "vendorId": "a1a2a3a4-b5b6-c7c8-d9d0-e1e2e3e4e5e6",
      "vendorNumber": "10000",
      "vendorName": "Fabrikam Supplies",
      "vendorInvoiceNumber": "FSI-2025-0456",
      "payToName": "Fabrikam Supplies",
      "payToVendorId": "a1a2a3a4-b5b6-c7c8-d9d0-e1e2e3e4e5e6",
      "payToVendorNumber": "10000",
      "shipToName": "CRONUS USA, Inc.",
      "shipToContact": "Warehouse Manager",
      "shipToAddressLine1": "7122 South Ashford Street",
      "shipToAddressLine2": "",
      "shipToCity": "Atlanta",
      "shipToState": "GA",
      "shipToCountry": "US",
      "shipToPostCode": "31772",
      "buyFromAddressLine1": "789 Industrial Blvd",
      "buyFromAddressLine2": "Unit 12",
      "buyFromCity": "Detroit",
      "buyFromState": "MI",
      "buyFromCountry": "US",
      "buyFromPostCode": "48201",
      "currencyId": "00000000-0000-0000-0000-000000000000",
      "currencyCode": "USD",
      "paymentTermsId": "a1a1a1a1-b2b2-c3c3-d4d4-e5e5e5e5e5e5",
      "purchaser": "EM",
      "pricesIncludeTax": false,
      "discountAmount": 0.00,
      "discountAppliedBeforeTax": true,
      "totalAmountExcludingTax": 12500.00,
      "totalTaxAmount": 937.50,
      "totalAmountIncludingTax": 13437.50,
      "status": "Open",
      "lastModifiedDateTime": "2025-02-10T11:00:00Z"
    }
  ]
}
```

---

### 2. Get a Single Purchase Invoice

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/purchaseInvoices(a1a1a1a1-aaaa-bbbb-cccc-111111111111)
Authorization: Bearer {access_token}
Accept: application/json
```

---

### 3. Create a Purchase Invoice (Draft)

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/purchaseInvoices
Authorization: Bearer {access_token}
Content-Type: application/json
```

**Request Body:**

```json
{
  "vendorNumber": "20000",
  "vendorInvoiceNumber": "CE-INV-2025-789",
  "invoiceDate": "2025-02-17",
  "dueDate": "2025-03-17",
  "purchaser": "EM",
  "currencyCode": "USD"
}
```

**Response (201 Created):**

```json
{
  "@odata.etag": "W/\"JzIwOzIyO1BJMjsn\"",
  "id": "b2b2b2b2-bbbb-cccc-dddd-222222222222",
  "number": "PI-DRAFT-001",
  "invoiceDate": "2025-02-17",
  "dueDate": "2025-03-17",
  "documentDate": "2025-02-17",
  "vendorId": "b2b3b4b5-c6c7-d8d9-e0e1-f2f3f4f5f6f7",
  "vendorNumber": "20000",
  "vendorName": "Contoso Electronics",
  "vendorInvoiceNumber": "CE-INV-2025-789",
  "payToName": "Contoso Electronics",
  "payToVendorId": "b2b3b4b5-c6c7-d8d9-e0e1-f2f3f4f5f6f7",
  "payToVendorNumber": "20000",
  "shipToName": "CRONUS USA, Inc.",
  "shipToContact": "",
  "shipToAddressLine1": "7122 South Ashford Street",
  "shipToAddressLine2": "",
  "shipToCity": "Atlanta",
  "shipToState": "GA",
  "shipToCountry": "US",
  "shipToPostCode": "31772",
  "buyFromAddressLine1": "456 Tech Park Drive",
  "buyFromAddressLine2": "",
  "buyFromCity": "San Jose",
  "buyFromState": "CA",
  "buyFromCountry": "US",
  "buyFromPostCode": "95110",
  "currencyId": "00000000-0000-0000-0000-000000000000",
  "currencyCode": "USD",
  "paymentTermsId": "a1a1a1a1-b2b2-c3c3-d4d4-e5e5e5e5e5e5",
  "purchaser": "EM",
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

### 4. Update a Purchase Invoice

```http
PATCH {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/purchaseInvoices(b2b2b2b2-bbbb-cccc-dddd-222222222222)
Authorization: Bearer {access_token}
Content-Type: application/json
If-Match: W/"JzIwOzIyO1BJMjsn"
```

**Request Body:**

```json
{
  "vendorInvoiceNumber": "CE-INV-2025-789-REV",
  "dueDate": "2025-04-01"
}
```

---

### 5. Delete a Purchase Invoice

```http
DELETE {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/purchaseInvoices(b2b2b2b2-bbbb-cccc-dddd-222222222222)
Authorization: Bearer {access_token}
If-Match: W/"JzIwOzIyO1BJMjsn"
```

**Response:** `204 No Content`

---

## Header Fields

| Field                        | Type     | Writable | Description                                    |
|------------------------------|----------|----------|------------------------------------------------|
| `id`                         | GUID     | No       | Unique identifier                              |
| `number`                     | string   | Yes*     | Invoice number                                 |
| `invoiceDate`                | date     | Yes      | Invoice date                                   |
| `dueDate`                    | date     | Yes      | Payment due date                               |
| `documentDate`               | date     | Yes      | Document date                                  |
| `vendorId`                   | GUID     | Yes      | Vendor ID                                      |
| `vendorNumber`               | string   | Yes      | Vendor number                                  |
| `vendorName`                 | string   | No       | Vendor name (read-only)                        |
| `vendorInvoiceNumber`        | string   | Yes      | Vendor's invoice number                        |
| `payToName`                  | string   | Yes      | Pay-to name                                    |
| `payToVendorId`              | GUID     | Yes      | Pay-to vendor ID                               |
| `payToVendorNumber`          | string   | Yes      | Pay-to vendor number                           |
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
| `purchaser`                  | string   | Yes      | Purchaser code                                 |
| `pricesIncludeTax`           | boolean  | Yes      | Whether prices include tax                     |
| `discountAmount`             | decimal  | Yes      | Invoice discount amount                        |
| `discountAppliedBeforeTax`   | boolean  | No       | Whether discount is pre-tax                    |
| `totalAmountExcludingTax`    | decimal  | No       | Total excl. tax (read-only)                    |
| `totalTaxAmount`             | decimal  | No       | Total tax (read-only)                          |
| `totalAmountIncludingTax`    | decimal  | No       | Total incl. tax (read-only)                    |
| `status`                     | string   | No       | `Draft`, `In Review`, `Open`, `Paid`, `Canceled` |
| `lastModifiedDateTime`       | datetime | No       | Last modification timestamp                    |

---

## Purchase Invoice Lines (Sub-Resource)

### List Lines

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/purchaseInvoices(a1a1a1a1-aaaa-bbbb-cccc-111111111111)/purchaseInvoiceLines
Authorization: Bearer {access_token}
```

**Response:**

```json
{
  "value": [
    {
      "@odata.etag": "W/\"JzE5O1BJTDEn\"",
      "id": "d4d4d4d4-dddd-eeee-ffff-444444444444",
      "documentId": "a1a1a1a1-aaaa-bbbb-cccc-111111111111",
      "sequence": 10000,
      "itemId": "b1c2d3e4-f5a6-7890-abcd-111111111111",
      "accountId": "00000000-0000-0000-0000-000000000000",
      "lineType": "Item",
      "lineObjectNumber": "1000",
      "description": "Bicycle",
      "unitOfMeasureId": "e4f5a6b7-c8d9-0123-4567-444444444444",
      "unitOfMeasureCode": "PCS",
      "directUnitCost": 800.00,
      "quantity": 15,
      "discountAmount": 0.00,
      "discountPercent": 0,
      "discountAppliedBeforeTax": true,
      "amountExcludingTax": 12000.00,
      "taxCode": "TAXABLE",
      "taxPercent": 7.5,
      "totalTaxAmount": 900.00,
      "amountIncludingTax": 12900.00,
      "expectedReceiptDate": "2025-02-28"
    }
  ]
}
```

### Create / Update / Delete Lines

```
POST   .../purchaseInvoices({id})/purchaseInvoiceLines
PATCH  .../purchaseInvoices({id})/purchaseInvoiceLines({lineId})   (with If-Match)
DELETE .../purchaseInvoices({id})/purchaseInvoiceLines({lineId})   (with If-Match)
```

---

## Bound Action: Post

Post a draft purchase invoice:

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/purchaseInvoices(b2b2b2b2-bbbb-cccc-dddd-222222222222)/Microsoft.NAV.post
Authorization: Bearer {access_token}
```

**Response:** `204 No Content`

After posting, the invoice status changes from `Draft` to `Open`, a posted invoice number is assigned, and the invoice becomes read-only.

---

## Filter Examples

```http
# Draft invoices
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/purchaseInvoices?$filter=status eq 'Draft'

# Open (posted, unpaid) invoices
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/purchaseInvoices?$filter=status eq 'Open'

# For a specific vendor
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/purchaseInvoices?$filter=vendorNumber eq '10000'

# High-value invoices
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/purchaseInvoices?$filter=totalAmountExcludingTax gt 5000
```

---

## ETag / If-Match

When updating (PATCH) or deleting (DELETE) draft purchase invoices or their lines, include the `If-Match` header with the `@odata.etag` from a prior GET. Posted invoices cannot be modified.

---

## Common Errors

| Status | Error                  | Cause & Resolution                                                                 |
|--------|------------------------|------------------------------------------------------------------------------------|
| `401`  | Unauthorized           | Access token is missing, expired, or invalid. Request a new token.                 |
| `404`  | Not Found              | The purchase invoice or line ID does not exist.                                    |
| `400`  | Bad Request            | Invalid field values, or attempting to modify a posted invoice.                     |
| `409`  | Conflict               | ETag mismatch. GET the record again and retry with the new ETag.                   |
