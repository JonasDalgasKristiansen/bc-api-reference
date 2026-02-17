# Items

Items represent the products, services, and non-inventory goods that your Business Central company buys and sells. The items endpoint supports full CRUD operations and provides access to inventory levels, categories, default dimensions, and pictures.

---

## Base Endpoint

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/items
```

---

## Operations

### 1. List All Items

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/items
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "@odata.context": "https://api.businesscentral.dynamics.com/v2.0/.../items",
  "value": [
    {
      "@odata.etag": "W/\"JzE5OzExMTExMTExMTE7SVRFTTE7Jw==\"",
      "id": "b1c2d3e4-f5a6-7890-abcd-111111111111",
      "number": "1000",
      "displayName": "Bicycle",
      "displayName2": "",
      "type": "Inventory",
      "itemCategoryId": "c2d3e4f5-a6b7-8901-2345-222222222222",
      "itemCategoryCode": "MISC",
      "blocked": false,
      "gtin": "0614141999996",
      "inventory": 125.00,
      "unitPrice": 1500.00,
      "priceIncludesTax": false,
      "unitCost": 800.00,
      "taxGroupId": "d3e4f5a6-b7c8-9012-3456-333333333333",
      "taxGroupCode": "TAXABLE",
      "baseUnitOfMeasureId": "e4f5a6b7-c8d9-0123-4567-444444444444",
      "baseUnitOfMeasureCode": "PCS",
      "generalProductPostingGroupId": "f5a6b7c8-d9e0-1234-5678-555555555555",
      "generalProductPostingGroupCode": "RETAIL",
      "inventoryPostingGroupId": "a6b7c8d9-e0f1-2345-6789-666666666666",
      "inventoryPostingGroupCode": "RESALE",
      "lastModifiedDateTime": "2025-02-10T14:00:00Z"
    },
    {
      "@odata.etag": "W/\"JzE5OzIyMjIyMjIyMjI7SVRFTTI7Jw==\"",
      "id": "c2d3e4f5-a6b7-8901-bcde-777777777777",
      "number": "1001",
      "displayName": "ATHENS Desk",
      "displayName2": "",
      "type": "Inventory",
      "itemCategoryId": "d3e4f5a6-b7c8-9012-3456-888888888888",
      "itemCategoryCode": "FURNITURE",
      "blocked": false,
      "gtin": "0614141999989",
      "inventory": 37.00,
      "unitPrice": 850.00,
      "priceIncludesTax": false,
      "unitCost": 425.00,
      "taxGroupId": "d3e4f5a6-b7c8-9012-3456-333333333333",
      "taxGroupCode": "TAXABLE",
      "baseUnitOfMeasureId": "e4f5a6b7-c8d9-0123-4567-444444444444",
      "baseUnitOfMeasureCode": "PCS",
      "generalProductPostingGroupId": "f5a6b7c8-d9e0-1234-5678-555555555555",
      "generalProductPostingGroupCode": "RETAIL",
      "inventoryPostingGroupId": "a6b7c8d9-e0f1-2345-6789-666666666666",
      "inventoryPostingGroupCode": "RESALE",
      "lastModifiedDateTime": "2025-02-12T09:30:00Z"
    },
    {
      "@odata.etag": "W/\"JzE5OzMzMzMzMzMzMzM7SVRFTTN7Jw==\"",
      "id": "d3e4f5a6-b7c8-9012-cdef-999999999999",
      "number": "2000",
      "displayName": "Installation Service",
      "displayName2": "",
      "type": "Service",
      "itemCategoryId": "00000000-0000-0000-0000-000000000000",
      "itemCategoryCode": "",
      "blocked": false,
      "gtin": "",
      "inventory": 0.00,
      "unitPrice": 150.00,
      "priceIncludesTax": false,
      "unitCost": 75.00,
      "taxGroupId": "d3e4f5a6-b7c8-9012-3456-333333333333",
      "taxGroupCode": "TAXABLE",
      "baseUnitOfMeasureId": "f5a6b7c8-d9e0-1234-5678-aaaaaaaaaaaa",
      "baseUnitOfMeasureCode": "HOUR",
      "generalProductPostingGroupId": "a6b7c8d9-e0f1-2345-6789-bbbbbbbbbbbb",
      "generalProductPostingGroupCode": "SERVICES",
      "inventoryPostingGroupId": "00000000-0000-0000-0000-000000000000",
      "inventoryPostingGroupCode": "",
      "lastModifiedDateTime": "2025-01-20T11:00:00Z"
    }
  ]
}
```

---

### 2. Get a Single Item

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/items(b1c2d3e4-f5a6-7890-abcd-111111111111)
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:** Same structure as a single item in the list above, with `@odata.etag`.

---

### 3. Create an Item

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/items
Authorization: Bearer {access_token}
Content-Type: application/json
```

**Request Body:**

```json
{
  "displayName": "Standing Desk Pro",
  "type": "Inventory",
  "itemCategoryCode": "FURNITURE",
  "unitPrice": 1200.00,
  "unitCost": 600.00,
  "taxGroupCode": "TAXABLE",
  "baseUnitOfMeasureCode": "PCS",
  "generalProductPostingGroupCode": "RETAIL",
  "inventoryPostingGroupCode": "RESALE",
  "gtin": "0614141888882"
}
```

**Response (201 Created):**

```json
{
  "@odata.etag": "W/\"JzE5OzQ0NDQ0NDQ0NDQ7SVRFTTQ7Jw==\"",
  "id": "e4f5a6b7-c8d9-0123-def0-cccccccccccc",
  "number": "1002",
  "displayName": "Standing Desk Pro",
  "displayName2": "",
  "type": "Inventory",
  "itemCategoryId": "d3e4f5a6-b7c8-9012-3456-888888888888",
  "itemCategoryCode": "FURNITURE",
  "blocked": false,
  "gtin": "0614141888882",
  "inventory": 0.00,
  "unitPrice": 1200.00,
  "priceIncludesTax": false,
  "unitCost": 600.00,
  "taxGroupId": "d3e4f5a6-b7c8-9012-3456-333333333333",
  "taxGroupCode": "TAXABLE",
  "baseUnitOfMeasureId": "e4f5a6b7-c8d9-0123-4567-444444444444",
  "baseUnitOfMeasureCode": "PCS",
  "generalProductPostingGroupId": "f5a6b7c8-d9e0-1234-5678-555555555555",
  "generalProductPostingGroupCode": "RETAIL",
  "inventoryPostingGroupId": "a6b7c8d9-e0f1-2345-6789-666666666666",
  "inventoryPostingGroupCode": "RESALE",
  "lastModifiedDateTime": "2025-02-17T10:00:00Z"
}
```

---

### 4. Update an Item

```http
PATCH {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/items(b1c2d3e4-f5a6-7890-abcd-111111111111)
Authorization: Bearer {access_token}
Content-Type: application/json
If-Match: W/"JzE5OzExMTExMTExMTE7SVRFTTE7Jw=="
```

**Request Body:**

```json
{
  "unitPrice": 1600.00,
  "displayName": "Bicycle - Premium Edition"
}
```

**Response (200 OK):** Returns the full updated item object with a new `@odata.etag`.

---

### 5. Delete an Item

```http
DELETE {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/items(b1c2d3e4-f5a6-7890-abcd-111111111111)
Authorization: Bearer {access_token}
If-Match: W/"JzE5OzExMTExMTExMTE7SVRFTTE7Jw=="
```

**Response:** `204 No Content`

---

## Fields

| Field                             | Type     | Writable | Description                                              |
|-----------------------------------|----------|----------|----------------------------------------------------------|
| `id`                              | GUID     | No       | Unique identifier                                        |
| `number`                          | string   | Yes*     | Item number (auto-generated if omitted on POST)          |
| `displayName`                     | string   | Yes      | Item name / description                                  |
| `displayName2`                    | string   | Yes      | Additional description field                             |
| `type`                            | string   | Yes      | `Inventory`, `Service`, or `Non-Inventory`               |
| `itemCategoryId`                  | GUID     | Yes      | Item category ID                                         |
| `itemCategoryCode`                | string   | Yes      | Item category code                                       |
| `blocked`                         | boolean  | Yes      | Whether the item is blocked                              |
| `gtin`                            | string   | Yes      | Global Trade Item Number (barcode)                       |
| `inventory`                       | decimal  | No       | Current inventory quantity (read-only)                   |
| `unitPrice`                       | decimal  | Yes      | Default selling price                                    |
| `priceIncludesTax`                | boolean  | Yes      | Whether unit price includes tax                          |
| `unitCost`                        | decimal  | Yes      | Unit cost                                                |
| `taxGroupId`                      | GUID     | Yes      | Tax group identifier                                     |
| `taxGroupCode`                    | string   | Yes      | Tax group code                                           |
| `baseUnitOfMeasureId`             | GUID     | Yes      | Base unit of measure ID                                  |
| `baseUnitOfMeasureCode`           | string   | Yes      | Base unit of measure code (e.g., `PCS`, `HOUR`)          |
| `generalProductPostingGroupId`    | GUID     | Yes      | General product posting group ID                         |
| `generalProductPostingGroupCode`  | string   | Yes      | General product posting group code                       |
| `inventoryPostingGroupId`         | GUID     | Yes      | Inventory posting group ID                               |
| `inventoryPostingGroupCode`       | string   | Yes      | Inventory posting group code                             |
| `lastModifiedDateTime`            | datetime | No       | Last modification timestamp                              |

---

## Sub-Resources

### Item Inventory (Read-Only)

Get inventory details per location:

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/items(b1c2d3e4-f5a6-7890-abcd-111111111111)/itemInventory
Authorization: Bearer {access_token}
```

---

## Expand Options

```http
# Include item category details
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/items?$expand=itemCategory

# Include inventory per location
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/items?$expand=inventory

# Include default dimensions
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/items?$expand=defaultDimensions

# Include picture
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/items?$expand=picture

# Include base unit of measure
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/items?$expand=baseUnitOfMeasure

# Multiple expands
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/items?$expand=itemCategory,inventory,defaultDimensions
```

---

## Filter Examples

```http
# Only inventory-type items
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/items?$filter=type eq 'Inventory'

# Only service-type items
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/items?$filter=type eq 'Service'

# Non-blocked items only
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/items?$filter=blocked eq false

# Items in stock
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/items?$filter=inventory gt 0

# Search by name
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/items?$filter=contains(displayName,'Widget')

# Filter by category
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/items?$filter=itemCategoryCode eq 'FURNITURE'

# Items above a certain price
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/items?$filter=unitPrice gt 500

# Combined: in-stock inventory items, select key fields, sorted
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/items?$filter=type eq 'Inventory' and blocked eq false and inventory gt 0&$select=number,displayName,inventory,unitPrice,itemCategoryCode&$orderby=displayName asc&$top=50
```

---

## ETag / If-Match

When updating (PATCH) or deleting (DELETE) an item, you **must** include the `If-Match` header:

1. **GET** the item to obtain the `@odata.etag` from the response
2. Pass it as: `If-Match: W/"JzE5OzExMTExMTExMTE7SVRFTTE7Jw=="`
3. If the record was modified between your GET and update, you'll receive `409 Conflict`
4. On conflict: GET the item again, get the new ETag, and retry

---

## Common Errors

| Status | Error                  | Cause & Resolution                                                                 |
|--------|------------------------|------------------------------------------------------------------------------------|
| `401`  | Unauthorized           | Access token is missing, expired, or invalid. Request a new token.                 |
| `404`  | Not Found              | The item ID does not exist. Verify the GUID.                                       |
| `400`  | Bad Request            | Invalid field values in POST/PATCH body. Check posting groups and UOM codes exist. |
| `409`  | Conflict               | ETag mismatch on PATCH/DELETE. GET the record again and retry with the new ETag.   |
