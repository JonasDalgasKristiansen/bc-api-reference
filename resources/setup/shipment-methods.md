# Shipment Methods

Shipment methods define how goods are shipped to customers or received from vendors (e.g., FOB, CIF, Express).

---

## Base Endpoint

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/shipmentMethods
```

---

## Operations

### 1. List All Shipment Methods

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/shipmentMethods
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "value": [
    {
      "@odata.etag": "W/\"JzE5O1NNMTsn\"",
      "id": "a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa",
      "code": "FOB",
      "displayName": "Free on Board",
      "lastModifiedDateTime": "2025-01-01T08:00:00Z"
    },
    {
      "@odata.etag": "W/\"JzIwO1NNMjsn\"",
      "id": "b2b2b2b2-2222-2222-2222-bbbbbbbbbbbb",
      "code": "CIF",
      "displayName": "Cost, Insurance, Freight",
      "lastModifiedDateTime": "2025-01-01T08:00:00Z"
    },
    {
      "@odata.etag": "W/\"JzIxO1NNMzsn\"",
      "id": "c3c3c3c3-3333-3333-3333-cccccccccccc",
      "code": "EXW",
      "displayName": "Ex Works",
      "lastModifiedDateTime": "2025-01-01T08:00:00Z"
    },
    {
      "@odata.etag": "W/\"JzIyO1NNNDsn\"",
      "id": "d4d4d4d4-4444-4444-4444-dddddddddddd",
      "code": "DDP",
      "displayName": "Delivered Duty Paid",
      "lastModifiedDateTime": "2025-01-01T08:00:00Z"
    }
  ]
}
```

---

### 2. Get a Single Shipment Method

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/shipmentMethods(a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa)
Authorization: Bearer {access_token}
```

---

### 3. Create a Shipment Method

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/shipmentMethods
Authorization: Bearer {access_token}
Content-Type: application/json
```

```json
{
  "code": "EXPRESS",
  "displayName": "Express Shipping"
}
```

---

### 4. Update a Shipment Method

```http
PATCH {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/shipmentMethods(a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa)
Authorization: Bearer {access_token}
Content-Type: application/json
If-Match: W/"JzE5O1NNMTsn"
```

```json
{
  "displayName": "Free on Board (Origin)"
}
```

---

### 5. Delete a Shipment Method

```http
DELETE {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/shipmentMethods(a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa)
Authorization: Bearer {access_token}
If-Match: W/"JzE5O1NNMTsn"
```

**Response:** `204 No Content`

---

## Fields

| Field                    | Type     | Writable | Description                                    |
|--------------------------|----------|----------|------------------------------------------------|
| `id`                     | GUID     | No       | Unique identifier                              |
| `code`                   | string   | Yes      | Shipment method code (e.g., `FOB`)             |
| `displayName`            | string   | Yes      | Display name                                   |
| `lastModifiedDateTime`   | datetime | No       | Last modification timestamp                    |

---

## ETag / If-Match

Required on PATCH and DELETE operations.

---

## Common Errors

| Status | Error                  | Cause & Resolution                                                                 |
|--------|------------------------|------------------------------------------------------------------------------------|
| `401`  | Unauthorized           | Access token is missing, expired, or invalid.                                      |
| `404`  | Not Found              | Shipment method ID does not exist.                                                 |
| `409`  | Conflict               | ETag mismatch. Re-fetch and retry.                                                 |
