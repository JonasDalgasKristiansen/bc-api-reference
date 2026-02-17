# Payment Terms

Payment terms define the conditions under which payments are due, including discount percentages for early payment.

---

## Base Endpoint

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/paymentTerms
```

---

## Operations

### 1. List All Payment Terms

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/paymentTerms
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "value": [
    {
      "@odata.etag": "W/\"JzE5O1BUMTsn\"",
      "id": "a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa",
      "code": "NET30",
      "displayName": "Net 30 Days",
      "dueDateCalculation": "30D",
      "discountDateCalculation": "",
      "discountPercent": 0,
      "calculateDiscountOnCreditMemos": false,
      "lastModifiedDateTime": "2025-01-01T08:00:00Z"
    },
    {
      "@odata.etag": "W/\"JzIwO1BUMjsn\"",
      "id": "b2b2b2b2-2222-2222-2222-bbbbbbbbbbbb",
      "code": "2%10NET30",
      "displayName": "2% 10 Net 30",
      "dueDateCalculation": "30D",
      "discountDateCalculation": "10D",
      "discountPercent": 2,
      "calculateDiscountOnCreditMemos": true,
      "lastModifiedDateTime": "2025-01-01T08:00:00Z"
    },
    {
      "@odata.etag": "W/\"JzIxO1BUMzsn\"",
      "id": "c3c3c3c3-3333-3333-3333-cccccccccccc",
      "code": "NET60",
      "displayName": "Net 60 Days",
      "dueDateCalculation": "60D",
      "discountDateCalculation": "",
      "discountPercent": 0,
      "calculateDiscountOnCreditMemos": false,
      "lastModifiedDateTime": "2025-01-01T08:00:00Z"
    },
    {
      "@odata.etag": "W/\"JzIyO1BUNDsn\"",
      "id": "d4d4d4d4-4444-4444-4444-dddddddddddd",
      "code": "COD",
      "displayName": "Cash on Delivery",
      "dueDateCalculation": "0D",
      "discountDateCalculation": "",
      "discountPercent": 0,
      "calculateDiscountOnCreditMemos": false,
      "lastModifiedDateTime": "2025-01-01T08:00:00Z"
    },
    {
      "@odata.etag": "W/\"JzIzO1BUNTsn\"",
      "id": "e5e5e5e5-5555-5555-5555-eeeeeeeeeeee",
      "code": "CM",
      "displayName": "Current Month",
      "dueDateCalculation": "CM",
      "discountDateCalculation": "",
      "discountPercent": 0,
      "calculateDiscountOnCreditMemos": false,
      "lastModifiedDateTime": "2025-01-01T08:00:00Z"
    }
  ]
}
```

---

### 2. Get a Single Payment Term

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/paymentTerms(a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa)
Authorization: Bearer {access_token}
```

---

### 3. Create a Payment Term

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/paymentTerms
Authorization: Bearer {access_token}
Content-Type: application/json
```

```json
{
  "code": "NET45",
  "displayName": "Net 45 Days",
  "dueDateCalculation": "45D",
  "discountDateCalculation": "",
  "discountPercent": 0,
  "calculateDiscountOnCreditMemos": false
}
```

---

### 4. Update a Payment Term

```http
PATCH {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/paymentTerms(a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa)
Authorization: Bearer {access_token}
Content-Type: application/json
If-Match: W/"JzE5O1BUMTsn"
```

```json
{
  "displayName": "Net 30 Days (Standard)"
}
```

---

### 5. Delete a Payment Term

```http
DELETE {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/paymentTerms(a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa)
Authorization: Bearer {access_token}
If-Match: W/"JzE5O1BUMTsn"
```

**Response:** `204 No Content`

---

## Fields

| Field                              | Type     | Writable | Description                                           |
|------------------------------------|----------|----------|-------------------------------------------------------|
| `id`                               | GUID     | No       | Unique identifier                                     |
| `code`                             | string   | Yes      | Payment term code (e.g., `NET30`)                     |
| `displayName`                      | string   | Yes      | Display name                                          |
| `dueDateCalculation`               | string   | Yes      | Date formula for due date (e.g., `30D`, `CM`, `1M`)   |
| `discountDateCalculation`          | string   | Yes      | Date formula for discount eligibility                  |
| `discountPercent`                  | decimal  | Yes      | Early payment discount percentage                     |
| `calculateDiscountOnCreditMemos`   | boolean  | Yes      | Apply discounts on credit memos                       |
| `lastModifiedDateTime`             | datetime | No       | Last modification timestamp                           |

---

## Date Formula Values

| Formula | Meaning                                        |
|---------|------------------------------------------------|
| `0D`    | Same day (cash on delivery)                    |
| `10D`   | 10 days from document date                     |
| `30D`   | 30 days from document date                     |
| `60D`   | 60 days from document date                     |
| `1M`    | 1 month from document date                     |
| `CM`    | End of current month                           |
| `CM+10D`| 10 days after end of current month             |

---

## ETag / If-Match

Required on PATCH and DELETE operations.

---

## Common Errors

| Status | Error                  | Cause & Resolution                                                                 |
|--------|------------------------|------------------------------------------------------------------------------------|
| `401`  | Unauthorized           | Access token is missing, expired, or invalid.                                      |
| `404`  | Not Found              | Payment term ID does not exist.                                                    |
| `400`  | Bad Request            | Invalid date formula or missing required fields.                                   |
| `409`  | Conflict               | ETag mismatch. Re-fetch and retry.                                                 |
