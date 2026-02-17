# Currencies

Currencies define the monetary units used in Business Central. The local currency (LCY) is configured in the General Ledger Setup, and additional currencies can be set up for foreign transactions.

---

## Base Endpoint

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/currencies
```

---

## Operations

### 1. List All Currencies

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/currencies
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "value": [
    {
      "@odata.etag": "W/\"JzE5O0MxOyc=\"",
      "id": "a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa",
      "code": "USD",
      "displayName": "US Dollar",
      "symbol": "$",
      "amountDecimalPlaces": "2:2",
      "amountRoundingPrecision": 0.01,
      "lastModifiedDateTime": "2025-01-01T08:00:00Z"
    },
    {
      "@odata.etag": "W/\"JzIwO0MyOyc=\"",
      "id": "b2b2b2b2-2222-2222-2222-bbbbbbbbbbbb",
      "code": "EUR",
      "displayName": "Euro",
      "symbol": "€",
      "amountDecimalPlaces": "2:2",
      "amountRoundingPrecision": 0.01,
      "lastModifiedDateTime": "2025-01-01T08:00:00Z"
    },
    {
      "@odata.etag": "W/\"JzIxO0MzOyc=\"",
      "id": "c3c3c3c3-3333-3333-3333-cccccccccccc",
      "code": "GBP",
      "displayName": "British Pound",
      "symbol": "£",
      "amountDecimalPlaces": "2:2",
      "amountRoundingPrecision": 0.01,
      "lastModifiedDateTime": "2025-01-01T08:00:00Z"
    },
    {
      "@odata.etag": "W/\"JzIyO0M0Oyc=\"",
      "id": "d4d4d4d4-4444-4444-4444-dddddddddddd",
      "code": "CAD",
      "displayName": "Canadian Dollar",
      "symbol": "$",
      "amountDecimalPlaces": "2:2",
      "amountRoundingPrecision": 0.01,
      "lastModifiedDateTime": "2025-01-01T08:00:00Z"
    }
  ]
}
```

---

### 2. Get a Single Currency

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/currencies(a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa)
Authorization: Bearer {access_token}
```

---

### 3. Create a Currency

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/currencies
Authorization: Bearer {access_token}
Content-Type: application/json
```

```json
{
  "code": "SEK",
  "displayName": "Swedish Krona",
  "symbol": "kr",
  "amountDecimalPlaces": "2:2",
  "amountRoundingPrecision": 0.01
}
```

---

### 4. Update a Currency

```http
PATCH {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/currencies(a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa)
Authorization: Bearer {access_token}
Content-Type: application/json
If-Match: W/"JzE5O0MxOyc="
```

```json
{
  "displayName": "US Dollar (Updated)"
}
```

---

### 5. Delete a Currency

```http
DELETE {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/currencies(a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa)
Authorization: Bearer {access_token}
If-Match: W/"JzE5O0MxOyc="
```

**Response:** `204 No Content`

---

## Fields

| Field                      | Type     | Writable | Description                                    |
|----------------------------|----------|----------|------------------------------------------------|
| `id`                       | GUID     | No       | Unique identifier                              |
| `code`                     | string   | Yes      | ISO currency code (e.g., `USD`, `EUR`)         |
| `displayName`              | string   | Yes      | Currency name                                  |
| `symbol`                   | string   | Yes      | Currency symbol (e.g., `$`, `€`)               |
| `amountDecimalPlaces`      | string   | Yes      | Decimal places for amounts (e.g., `2:2`)       |
| `amountRoundingPrecision`  | decimal  | Yes      | Rounding precision for amounts                 |
| `lastModifiedDateTime`     | datetime | No       | Last modification timestamp                    |

---

## Filter Examples

```http
# Search by code
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/currencies?$filter=code eq 'EUR'
```

---

## ETag / If-Match

Required on PATCH and DELETE operations.

---

## Common Errors

| Status | Error                  | Cause & Resolution                                                                 |
|--------|------------------------|------------------------------------------------------------------------------------|
| `401`  | Unauthorized           | Access token is missing, expired, or invalid.                                      |
| `404`  | Not Found              | Currency ID does not exist.                                                        |
| `400`  | Bad Request            | Invalid currency code or missing required fields.                                  |
| `409`  | Conflict               | ETag mismatch. Re-fetch and retry.                                                 |
