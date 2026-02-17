# Purchase Orders

Purchase orders represent orders placed with vendors for goods or services. They support line items, vendor details, receiving, and invoicing. Purchase orders can be received and invoiced using bound actions.

---

## Base Endpoint

```
{{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/purchaseOrders
```

---

## Operations

### 1. List All Purchase Orders

```http
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/purchaseOrders
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "@odata.context": "https://api.businesscentral.dynamics.com/v2.0/.../purchaseOrders",
  "value": [
    {
      "@odata.etag": "W/\"JzE5OzExO1BPMTsn\"",
      "id": "a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa",
      "number": "PO-1001",
      "orderDate": "2025-02-10",
      "documentDate": "2025-02-10",
      "dueDate": "2025-03-12",
      "vendorId": "a1a2a3a4-b5b6-c7c8-d9d0-e1e2e3e4e5e6",
      "vendorNumber": "10000",
      "vendorName": "Fabrikam Supplies",
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
      "shipmentMethodId": "f6f6f6f6-a7a7-b8b8-c9c9-d0d0d0d0d0d0",
      "purchaser": "EM",
      "pricesIncludeTax": false,
      "discountAmount": 0.00,
      "discountAppliedBeforeTax": true,
      "totalAmountExcludingTax": 24000.00,
      "totalTaxAmount": 1800.00,
      "totalAmountIncludingTax": 25800.00,
      "status": "Open",
      "fullyReceived": false,
      "lastModifiedDateTime": "2025-02-10T09:00:00Z"
    }
  ]
}
```

---

### 2. Get a Single Purchase Order

```http
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/purchaseOrders(a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa)
Authorization: Bearer {access_token}
Accept: application/json
```

---

### 3. Create a Purchase Order

```http
POST {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/purchaseOrders
Authorization: Bearer {access_token}
Content-Type: application/json
```

**Request Body:**

```json
{
  "vendorNumber": "10000",
  "orderDate": "2025-02-17",
  "dueDate": "2025-03-17",
  "purchaser": "EM",
  "currencyCode": "USD"
}
```

**Response (201 Created):**

```json
{
  "@odata.etag": "W/\"JzIwOzIyO1BPMjsn\"",
  "id": "b2b2b2b2-2222-2222-2222-bbbbbbbbbbbb",
  "number": "PO-1002",
  "orderDate": "2025-02-17",
  "documentDate": "2025-02-17",
  "dueDate": "2025-03-17",
  "vendorId": "a1a2a3a4-b5b6-c7c8-d9d0-e1e2e3e4e5e6",
  "vendorNumber": "10000",
  "vendorName": "Fabrikam Supplies",
  "payToName": "Fabrikam Supplies",
  "payToVendorId": "a1a2a3a4-b5b6-c7c8-d9d0-e1e2e3e4e5e6",
  "payToVendorNumber": "10000",
  "shipToName": "CRONUS USA, Inc.",
  "shipToContact": "",
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
  "shipmentMethodId": "00000000-0000-0000-0000-000000000000",
  "purchaser": "EM",
  "pricesIncludeTax": false,
  "discountAmount": 0.00,
  "discountAppliedBeforeTax": true,
  "totalAmountExcludingTax": 0.00,
  "totalTaxAmount": 0.00,
  "totalAmountIncludingTax": 0.00,
  "status": "Open",
  "fullyReceived": false,
  "lastModifiedDateTime": "2025-02-17T10:00:00Z"
}
```

---

### 4. Update a Purchase Order

```http
PATCH {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/purchaseOrders(a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa)
Authorization: Bearer {access_token}
Content-Type: application/json
If-Match: W/"JzE5OzExO1BPMTsn"
```

**Request Body:**

```json
{
  "dueDate": "2025-04-01",
  "purchaser": "RK"
}
```

---

### 5. Delete a Purchase Order

```http
DELETE {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/purchaseOrders(a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa)
Authorization: Bearer {access_token}
If-Match: W/"JzE5OzExO1BPMTsn"
```

**Response:** `204 No Content`

---

## Header Fields

| Field                        | Type     | Writable | Description                                    |
|------------------------------|----------|----------|------------------------------------------------|
| `id`                         | GUID     | No       | Unique identifier                              |
| `number`                     | string   | Yes*     | PO number (auto-generated if omitted)          |
| `orderDate`                  | date     | Yes      | Order date                                     |
| `documentDate`               | date     | Yes      | Document date                                  |
| `dueDate`                    | date     | Yes      | Payment due date                               |
| `vendorId`                   | GUID     | Yes      | Vendor ID                                      |
| `vendorNumber`               | string   | Yes      | Vendor number                                  |
| `vendorName`                 | string   | No       | Vendor name (read-only)                        |
| `payToName`                  | string   | Yes      | Pay-to name                                    |
| `payToVendorId`              | GUID     | Yes      | Pay-to vendor ID                               |
| `payToVendorNumber`          | string   | Yes      | Pay-to vendor number                           |
| `shipToName`                 | string   | Yes      | Ship-to name (your receiving location)         |
| `shipToContact`              | string   | Yes      | Ship-to contact                                |
| `shipToAddressLine1`         | string   | Yes      | Ship-to address line 1                         |
| `shipToAddressLine2`         | string   | Yes      | Ship-to address line 2                         |
| `shipToCity`                 | string   | Yes      | Ship-to city                                   |
| `shipToState`                | string   | Yes      | Ship-to state                                  |
| `shipToCountry`              | string   | Yes      | Ship-to country                                |
| `shipToPostCode`             | string   | Yes      | Ship-to postal code                            |
| `buyFromAddressLine1`        | string   | No       | Vendor address line 1 (read-only)              |
| `buyFromAddressLine2`        | string   | No       | Vendor address line 2 (read-only)              |
| `buyFromCity`                | string   | No       | Vendor city (read-only)                        |
| `buyFromState`               | string   | No       | Vendor state (read-only)                       |
| `buyFromCountry`             | string   | No       | Vendor country (read-only)                     |
| `buyFromPostCode`            | string   | No       | Vendor postal code (read-only)                 |
| `currencyId`                 | GUID     | Yes      | Currency ID                                    |
| `currencyCode`               | string   | Yes      | Currency code                                  |
| `paymentTermsId`             | GUID     | Yes      | Payment terms ID                               |
| `shipmentMethodId`           | GUID     | Yes      | Shipment method ID                             |
| `purchaser`                  | string   | Yes      | Purchaser/buyer code                           |
| `pricesIncludeTax`           | boolean  | Yes      | Whether prices include tax                     |
| `discountAmount`             | decimal  | Yes      | Invoice discount amount                        |
| `discountAppliedBeforeTax`   | boolean  | No       | Whether discount is pre-tax                    |
| `totalAmountExcludingTax`    | decimal  | No       | Total excl. tax (read-only)                    |
| `totalTaxAmount`             | decimal  | No       | Total tax (read-only)                          |
| `totalAmountIncludingTax`    | decimal  | No       | Total incl. tax (read-only)                    |
| `status`                     | string   | No       | `Open`, `Released`, `Pending Approval`         |
| `fullyReceived`              | boolean  | No       | Whether all lines are fully received           |
| `lastModifiedDateTime`       | datetime | No       | Last modification timestamp                    |

---

## Purchase Order Lines (Sub-Resource)

### List Lines

```http
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/purchaseOrders(a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa)/purchaseOrderLines
Authorization: Bearer {access_token}
```

**Response:**

```json
{
  "value": [
    {
      "@odata.etag": "W/\"JzE5O1BPTDEn\"",
      "id": "c3c3c3c3-3333-3333-3333-cccccccccccc",
      "documentId": "a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa",
      "sequence": 10000,
      "itemId": "b1c2d3e4-f5a6-7890-abcd-111111111111",
      "accountId": "00000000-0000-0000-0000-000000000000",
      "lineType": "Item",
      "lineObjectNumber": "1000",
      "description": "Bicycle",
      "unitOfMeasureId": "e4f5a6b7-c8d9-0123-4567-444444444444",
      "unitOfMeasureCode": "PCS",
      "directUnitCost": 800.00,
      "quantity": 30,
      "discountAmount": 0.00,
      "discountPercent": 0,
      "discountAppliedBeforeTax": true,
      "amountExcludingTax": 24000.00,
      "taxCode": "TAXABLE",
      "taxPercent": 7.5,
      "totalTaxAmount": 1800.00,
      "amountIncludingTax": 25800.00,
      "expectedReceiptDate": "2025-02-28",
      "receivedQuantity": 0,
      "invoicedQuantity": 0
    }
  ]
}
```

### Create / Update / Delete Lines

```
POST   .../purchaseOrders({id})/purchaseOrderLines
PATCH  .../purchaseOrders({id})/purchaseOrderLines({lineId})   (with If-Match)
DELETE .../purchaseOrders({id})/purchaseOrderLines({lineId})   (with If-Match)
```

**Create Line Example:**

```json
{
  "lineType": "Item",
  "lineObjectNumber": "1001",
  "description": "ATHENS Desk",
  "quantity": 10,
  "directUnitCost": 425.00
}
```

---

## Bound Actions

### Receive Items

Records the receipt of items on the purchase order:

```http
POST {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/purchaseOrders(a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa)/Microsoft.NAV.receive
Authorization: Bearer {access_token}
```

**Response:** `204 No Content`

### Receive and Invoice

Receives items and creates a posted purchase invoice in a single step:

```http
POST {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/purchaseOrders(a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa)/Microsoft.NAV.receiveAndInvoice
Authorization: Bearer {access_token}
```

**Response:** `204 No Content`

> **Note:** After receive and invoice, a purchase receipt and posted purchase invoice are created. The `fullyReceived` field reflects the receipt status.

---

## Related Read-Only Resources

### Purchase Receipts

After receiving items, purchase receipts are created automatically:

```http
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/purchaseReceipts
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/purchaseReceipts({id})/purchaseReceiptLines
```

These are read-only and cannot be created or modified via the API.

---

## Filter Examples

```http
# Open purchase orders
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/purchaseOrders?$filter=status eq 'Open'

# Not fully received
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/purchaseOrders?$filter=fullyReceived eq false

# For a specific vendor
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/purchaseOrders?$filter=vendorNumber eq '10000'

# Due this month
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/purchaseOrders?$filter=dueDate ge 2025-02-01 and dueDate le 2025-02-28

# High-value orders
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/purchaseOrders?$filter=totalAmountExcludingTax gt 10000
```

---

## ETag / If-Match

When updating (PATCH) or deleting (DELETE) purchase orders or lines, include the `If-Match` header with the `@odata.etag` from a prior GET.

---

## Common Errors

| Status | Error                  | Cause & Resolution                                                                 |
|--------|------------------------|------------------------------------------------------------------------------------|
| `401`  | Unauthorized           | Access token is missing, expired, or invalid. Request a new token.                 |
| `404`  | Not Found              | The purchase order or line ID does not exist.                                      |
| `400`  | Bad Request            | Invalid field values or missing required fields.                                   |
| `409`  | Conflict               | ETag mismatch. GET the record again and retry with the new ETag.                   |
