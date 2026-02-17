# Payment Methods

Payment methods define how payments are made (e.g., bank transfer, check, cash). They are assigned to customers, vendors, and documents.

---

## Base Endpoint

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/paymentMethods
```

---

## Operations

### 1. List All Payment Methods

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/paymentMethods
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "value": [
    {
      "@odata.etag": "W/\"JzE5O1BNMTsn\"",
      "id": "a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa",
      "code": "BANK",
      "displayName": "Bank Transfer",
      "lastModifiedDateTime": "2025-01-01T08:00:00Z"
    },
    {
      "@odata.etag": "W/\"JzIwO1BNMjsn\"",
      "id": "b2b2b2b2-2222-2222-2222-bbbbbbbbbbbb",
      "code": "CHECK",
      "displayName": "Check",
      "lastModifiedDateTime": "2025-01-01T08:00:00Z"
    },
    {
      "@odata.etag": "W/\"JzIxO1BNMzsn\"",
      "id": "c3c3c3c3-3333-3333-3333-cccccccccccc",
      "code": "CASH",
      "displayName": "Cash",
      "lastModifiedDateTime": "2025-01-01T08:00:00Z"
    },
    {
      "@odata.etag": "W/\"JzIyO1BNNDsn\"",
      "id": "d4d4d4d4-4444-4444-4444-dddddddddddd",
      "code": "CARD",
      "displayName": "Credit Card",
      "lastModifiedDateTime": "2025-01-01T08:00:00Z"
    }
  ]
}
```

---

### 2. Get a Single Payment Method

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/paymentMethods(a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa)
Authorization: Bearer {access_token}
```

---

### 3. Create a Payment Method

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/paymentMethods
Authorization: Bearer {access_token}
Content-Type: application/json
```

```json
{
  "code": "ACH",
  "displayName": "ACH Transfer"
}
```

---

### 4. Update a Payment Method

```http
PATCH {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/paymentMethods(a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa)
Authorization: Bearer {access_token}
Content-Type: application/json
If-Match: W/"JzE5O1BNMTsn"
```

```json
{
  "displayName": "Bank Transfer (Wire)"
}
```

---

### 5. Delete a Payment Method

```http
DELETE {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/paymentMethods(a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa)
Authorization: Bearer {access_token}
If-Match: W/"JzE5O1BNMTsn"
```

**Response:** `204 No Content`

---

## Fields

| Field                    | Type     | Writable | Description                                    |
|--------------------------|----------|----------|------------------------------------------------|
| `id`                     | GUID     | No       | Unique identifier                              |
| `code`                   | string   | Yes      | Payment method code (e.g., `BANK`)             |
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
| `404`  | Not Found              | Payment method ID does not exist.                                                  |
| `409`  | Conflict               | ETag mismatch. Re-fetch and retry.                                                 |
