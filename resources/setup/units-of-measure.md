# Units of Measure

Units of measure define the measurement units used for items and resources in Business Central (e.g., pieces, boxes, hours, kilograms).

---

## Base Endpoint

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/unitsOfMeasure
```

---

## Operations

### 1. List All Units of Measure

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/unitsOfMeasure
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "value": [
    {
      "@odata.etag": "W/\"JzE5O1VPTTEn\"",
      "id": "a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa",
      "code": "PCS",
      "displayName": "Piece",
      "internationalStandardCode": "EA",
      "lastModifiedDateTime": "2025-01-01T08:00:00Z"
    },
    {
      "@odata.etag": "W/\"JzIwO1VPTTIn\"",
      "id": "b2b2b2b2-2222-2222-2222-bbbbbbbbbbbb",
      "code": "BOX",
      "displayName": "Box",
      "internationalStandardCode": "BX",
      "lastModifiedDateTime": "2025-01-01T08:00:00Z"
    },
    {
      "@odata.etag": "W/\"JzIxO1VPTTMn\"",
      "id": "c3c3c3c3-3333-3333-3333-cccccccccccc",
      "code": "HOUR",
      "displayName": "Hour",
      "internationalStandardCode": "HUR",
      "lastModifiedDateTime": "2025-01-01T08:00:00Z"
    },
    {
      "@odata.etag": "W/\"JzIyO1VPTTQ=\"",
      "id": "d4d4d4d4-4444-4444-4444-dddddddddddd",
      "code": "KG",
      "displayName": "Kilogram",
      "internationalStandardCode": "KGM",
      "lastModifiedDateTime": "2025-01-01T08:00:00Z"
    },
    {
      "@odata.etag": "W/\"JzIzO1VPTTUN\"",
      "id": "e5e5e5e5-5555-5555-5555-eeeeeeeeeeee",
      "code": "SET",
      "displayName": "Set",
      "internationalStandardCode": "SET",
      "lastModifiedDateTime": "2025-01-01T08:00:00Z"
    }
  ]
}
```

---

### 2. Get a Single Unit of Measure

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/unitsOfMeasure(a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa)
Authorization: Bearer {access_token}
```

---

### 3. Create a Unit of Measure

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/unitsOfMeasure
Authorization: Bearer {access_token}
Content-Type: application/json
```

```json
{
  "code": "PALLET",
  "displayName": "Pallet",
  "internationalStandardCode": "PF"
}
```

---

### 4. Update a Unit of Measure

```http
PATCH {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/unitsOfMeasure(a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa)
Authorization: Bearer {access_token}
Content-Type: application/json
If-Match: W/"JzE5O1VPTTEn"
```

```json
{
  "displayName": "Each / Piece"
}
```

---

### 5. Delete a Unit of Measure

```http
DELETE {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/unitsOfMeasure(a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa)
Authorization: Bearer {access_token}
If-Match: W/"JzE5O1VPTTEn"
```

**Response:** `204 No Content`

---

## Fields

| Field                        | Type     | Writable | Description                                    |
|------------------------------|----------|----------|------------------------------------------------|
| `id`                         | GUID     | No       | Unique identifier                              |
| `code`                       | string   | Yes      | Unit code (e.g., `PCS`, `BOX`)                 |
| `displayName`                | string   | Yes      | Display name                                   |
| `internationalStandardCode`  | string   | Yes      | UN/CEFACT international standard code          |
| `lastModifiedDateTime`       | datetime | No       | Last modification timestamp                    |

---

## Common International Standard Codes

| Code  | Meaning                    |
|-------|----------------------------|
| `EA`  | Each                       |
| `BX`  | Box                        |
| `HUR` | Hour                       |
| `KGM` | Kilogram                   |
| `MTR` | Meter                      |
| `LTR` | Liter                      |
| `SET` | Set                        |
| `PF`  | Pallet                     |

---

## ETag / If-Match

Required on PATCH and DELETE operations.

---

## Common Errors

| Status | Error                  | Cause & Resolution                                                                 |
|--------|------------------------|------------------------------------------------------------------------------------|
| `401`  | Unauthorized           | Access token is missing, expired, or invalid.                                      |
| `404`  | Not Found              | Unit of measure ID does not exist.                                                 |
| `400`  | Bad Request            | Duplicate code or missing required fields.                                         |
| `409`  | Conflict               | ETag mismatch. Re-fetch and retry.                                                 |
