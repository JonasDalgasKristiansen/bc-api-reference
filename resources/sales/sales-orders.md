# Sales Orders

Sales orders represent confirmed customer orders that will be shipped and invoiced. They support line items, shipping details, and a bound action to ship and invoice in a single step. Sales orders can also be created from sales quotes.

---

## Base Endpoint

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesOrders
```

---

## Operations

### 1. List All Sales Orders

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesOrders
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "@odata.context": "https://api.businesscentral.dynamics.com/v2.0/.../salesOrders",
  "value": [
    {
      "@odata.etag": "W/\"JzE5OzExO1NPMTsn\"",
      "id": "a1a1b1b1-c1c1-d1d1-e1e1-f1f1f1f1f1f1",
      "number": "SO-1001",
      "externalDocumentNumber": "PO-CUST-5678",
      "orderDate": "2025-02-10",
      "documentDate": "2025-02-10",
      "dueDate": "2025-03-12",
      "requestedDeliveryDate": "2025-02-28",
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
      "totalAmountExcludingTax": 7500.00,
      "totalTaxAmount": 562.50,
      "totalAmountIncludingTax": 8062.50,
      "status": "Open",
      "fullyShipped": false,
      "fullyInvoiced": false,
      "lastModifiedDateTime": "2025-02-10T14:00:00Z"
    }
  ]
}
```

---

### 2. Get a Single Sales Order

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesOrders(a1a1b1b1-c1c1-d1d1-e1e1-f1f1f1f1f1f1)
Authorization: Bearer {access_token}
Accept: application/json
```

---

### 3. Create a Sales Order

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesOrders
Authorization: Bearer {access_token}
Content-Type: application/json
```

**Request Body:**

```json
{
  "customerNumber": "10000",
  "externalDocumentNumber": "PO-CUST-9999",
  "orderDate": "2025-02-17",
  "requestedDeliveryDate": "2025-03-15",
  "salesperson": "JR",
  "currencyCode": "USD"
}
```

**Response (201 Created):**

```json
{
  "@odata.etag": "W/\"JzIwOzIyO1NPMjsn\"",
  "id": "b2b2c2c2-d2d2-e2e2-f2f2-a2a2a2a2a2a2",
  "number": "SO-1002",
  "externalDocumentNumber": "PO-CUST-9999",
  "orderDate": "2025-02-17",
  "documentDate": "2025-02-17",
  "dueDate": "2025-03-17",
  "requestedDeliveryDate": "2025-03-15",
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
  "status": "Open",
  "fullyShipped": false,
  "fullyInvoiced": false,
  "lastModifiedDateTime": "2025-02-17T10:00:00Z"
}
```

---

### 4. Update a Sales Order

```http
PATCH {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesOrders(a1a1b1b1-c1c1-d1d1-e1e1-f1f1f1f1f1f1)
Authorization: Bearer {access_token}
Content-Type: application/json
If-Match: W/"JzE5OzExO1NPMTsn"
```

**Request Body:**

```json
{
  "requestedDeliveryDate": "2025-03-20",
  "externalDocumentNumber": "PO-CUST-5678-REV1"
}
```

---

### 5. Delete a Sales Order

```http
DELETE {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesOrders(a1a1b1b1-c1c1-d1d1-e1e1-f1f1f1f1f1f1)
Authorization: Bearer {access_token}
If-Match: W/"JzE5OzExO1NPMTsn"
```

**Response:** `204 No Content`

---

## Header Fields

| Field                        | Type     | Writable | Description                                    |
|------------------------------|----------|----------|------------------------------------------------|
| `id`                         | GUID     | No       | Unique identifier                              |
| `number`                     | string   | Yes*     | Order number (auto-generated if omitted)       |
| `externalDocumentNumber`     | string   | Yes      | External reference (e.g., customer PO)         |
| `orderDate`                  | date     | Yes      | Order date                                     |
| `documentDate`               | date     | Yes      | Document date                                  |
| `dueDate`                    | date     | Yes      | Due date                                       |
| `requestedDeliveryDate`      | date     | Yes      | Requested delivery date                        |
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
| `status`                     | string   | No       | `Open`, `Released`, `Pending Approval`         |
| `fullyShipped`               | boolean  | No       | Whether all lines are fully shipped            |
| `fullyInvoiced`              | boolean  | No       | Whether all lines are fully invoiced           |
| `lastModifiedDateTime`       | datetime | No       | Last modification timestamp                    |

---

## Sales Order Lines (Sub-Resource)

### List Order Lines

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesOrders(a1a1b1b1-c1c1-d1d1-e1e1-f1f1f1f1f1f1)/salesOrderLines
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "value": [
    {
      "@odata.etag": "W/\"JzE5O1NPTDEn\"",
      "id": "d3d3e3e3-f3f3-a3a3-b3b3-c3c3c3c3c3c3",
      "documentId": "a1a1b1b1-c1c1-d1d1-e1e1-f1f1f1f1f1f1",
      "sequence": 10000,
      "itemId": "b1c2d3e4-f5a6-7890-abcd-111111111111",
      "accountId": "00000000-0000-0000-0000-000000000000",
      "lineType": "Item",
      "lineObjectNumber": "1000",
      "description": "Bicycle",
      "unitOfMeasureId": "e4f5a6b7-c8d9-0123-4567-444444444444",
      "unitOfMeasureCode": "PCS",
      "unitPrice": 1500.00,
      "quantity": 5,
      "discountAmount": 0.00,
      "discountPercent": 0,
      "discountAppliedBeforeTax": true,
      "amountExcludingTax": 7500.00,
      "taxCode": "TAXABLE",
      "taxPercent": 7.5,
      "totalTaxAmount": 562.50,
      "amountIncludingTax": 8062.50,
      "shipmentDate": "2025-02-28",
      "shippedQuantity": 0,
      "invoicedQuantity": 0,
      "shipQuantity": 5
    }
  ]
}
```

### Create / Update / Delete Order Lines

Same patterns as sales quote lines. Use POST, PATCH (with If-Match), and DELETE on:

```
POST   .../salesOrders({id})/salesOrderLines
PATCH  .../salesOrders({id})/salesOrderLines({lineId})
DELETE .../salesOrders({id})/salesOrderLines({lineId})
```

---

## Bound Actions

### Ship and Invoice

Ships all items and creates an invoice in a single action:

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesOrders(a1a1b1b1-c1c1-d1d1-e1e1-f1f1f1f1f1f1)/Microsoft.NAV.shipAndInvoice
Authorization: Bearer {access_token}
```

**Response:** `204 No Content`

> **Note:** After this action, the order's `fullyShipped` and `fullyInvoiced` fields will be `true`, and a posted sales invoice and sales shipment will be created.

---

## Filter Examples

```http
# Open orders only
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesOrders?$filter=status eq 'Open'

# Not fully shipped
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesOrders?$filter=fullyShipped eq false

# Orders for a specific customer
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesOrders?$filter=customerNumber eq '10000'

# Orders due this month
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesOrders?$filter=dueDate ge 2025-02-01 and dueDate le 2025-02-28
```

---

## ETag / If-Match

When updating (PATCH) or deleting (DELETE) orders or lines, include the `If-Match` header with the `@odata.etag` from a prior GET.

---

## Common Errors

| Status | Error                  | Cause & Resolution                                                                 |
|--------|------------------------|------------------------------------------------------------------------------------|
| `401`  | Unauthorized           | Access token is missing, expired, or invalid. Request a new token.                 |
| `404`  | Not Found              | The order or line ID does not exist.                                               |
| `400`  | Bad Request            | Invalid field values or missing required fields.                                   |
| `409`  | Conflict               | ETag mismatch. GET the record again and retry with the new ETag.                   |
