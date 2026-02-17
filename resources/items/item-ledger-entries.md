# Item Ledger Entries

Item ledger entries are read-only records showing all inventory transactions (purchases, sales, transfers, adjustments). Each entry affects the quantity and value of an item in inventory.

---

## Base Endpoint

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/itemLedgerEntries
```

---

## Operations

### 1. List All Item Ledger Entries

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/itemLedgerEntries
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "value": [
    {
      "id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
      "entryNumber": 1001,
      "itemNumber": "1896-S",
      "postingDate": "2024-06-15",
      "entryType": "Purchase",
      "sourceNumber": "10000",
      "sourceType": "Vendor",
      "documentNumber": "PI-1001",
      "documentType": "Purchase Receipt",
      "description": "ATHENS Desk",
      "quantity": 10.0,
      "salesAmountActual": 0.0,
      "costAmountActual": 2500.00,
      "lastModifiedDateTime": "2024-06-15T10:00:00Z"
    },
    {
      "id": "b2c3d4e5-f6a7-8901-bcde-f23456789012",
      "entryNumber": 1002,
      "itemNumber": "1896-S",
      "postingDate": "2024-06-16",
      "entryType": "Sale",
      "sourceNumber": "10000",
      "sourceType": "Customer",
      "documentNumber": "SI-2001",
      "documentType": "Sales Shipment",
      "description": "ATHENS Desk",
      "quantity": -3.0,
      "salesAmountActual": 1200.00,
      "costAmountActual": -750.00,
      "lastModifiedDateTime": "2024-06-16T14:00:00Z"
    }
  ]
}
```

> This endpoint is **read-only** (GET only).

---

## Field Definitions

| Field                    | Type     | Description                                       |
|--------------------------|----------|---------------------------------------------------|
| `id`                     | GUID     | Unique identifier                                 |
| `entryNumber`            | integer  | Entry number                                      |
| `itemNumber`             | string   | Item number                                       |
| `postingDate`            | date     | Posting date                                      |
| `entryType`              | enum     | `Purchase`, `Sale`, `Positive Adjmt.`, `Negative Adjmt.`, `Transfer`, `Consumption`, `Output` |
| `sourceNumber`           | string   | Source record number (customer/vendor number)      |
| `sourceType`             | enum     | `Customer`, `Vendor`, `Item`, ` `                 |
| `documentNumber`         | string   | Document number                                   |
| `documentType`           | enum     | `Sales Shipment`, `Sales Invoice`, `Sales Return Receipt`, `Sales Credit Memo`, `Purchase Receipt`, `Purchase Invoice`, `Purchase Return Shipment`, `Purchase Credit Memo`, `Transfer Shipment`, `Transfer Receipt` |
| `description`            | string   | Description                                       |
| `quantity`               | decimal  | Quantity (negative for outbound)                  |
| `salesAmountActual`      | decimal  | Sales amount                                      |
| `costAmountActual`       | decimal  | Cost amount                                       |
| `lastModifiedDateTime`   | datetime | Last modified timestamp                           |

---

## OData Query Examples

### Filter by item

```
$filter=itemNumber eq '1896-S'
```

### Filter by entry type

```
$filter=entryType eq 'Sale'
$filter=entryType eq 'Purchase'
```

### Filter by date range

```
$filter=postingDate ge 2024-06-01 and postingDate le 2024-06-30
```

### Filter by document

```
$filter=documentNumber eq 'SI-2001'
```

### Get recent entries

```
$orderby=postingDate desc&$top=50
```

---

## Common Errors

| Status | Error                  | Cause & Resolution                                                                 |
|--------|------------------------|------------------------------------------------------------------------------------|
| `401`  | Unauthorized           | Access token is missing, expired, or invalid. Request a new token.                 |
| `404`  | Not Found              | The entry does not exist. Verify the ID.                                           |

---

## Important Notes

- This endpoint is **read-only** — entries are created by posting documents (purchase orders, sales invoices, etc.)
- Negative quantities indicate outbound movements (sales, negative adjustments)
- Use `$filter` on `postingDate` and `itemNumber` for efficient queries — the table can be very large
