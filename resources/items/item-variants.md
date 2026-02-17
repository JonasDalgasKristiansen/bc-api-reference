# Item Variants

Item variants represent different versions or configurations of the same item (e.g., color, size). They are available both as a sub-resource of items and as a top-level endpoint.

---

## Base Endpoints

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/itemVariants
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/items({itemId})/itemVariants
```

---

## Operations

### 1. List All Item Variants

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/itemVariants
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "value": [
    {
      "@odata.etag": "W/\"JzE5OzEyMzQ1Njc4OTA7Jw==\"",
      "id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
      "itemId": "d1e2f3a4-b5c6-7890-1234-567890abcdef",
      "itemNumber": "1896-S",
      "code": "RED",
      "description": "Red color variant",
      "lastModifiedDateTime": "2024-06-15T10:00:00Z"
    },
    {
      "@odata.etag": "W/\"JzIwOzEyMzQ1Njc4OTA7Jw==\"",
      "id": "b2c3d4e5-f6a7-8901-bcde-f23456789012",
      "itemId": "d1e2f3a4-b5c6-7890-1234-567890abcdef",
      "itemNumber": "1896-S",
      "code": "BLUE",
      "description": "Blue color variant",
      "lastModifiedDateTime": "2024-06-14T15:30:00Z"
    }
  ]
}
```

### 2. List Variants for a Specific Item

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/items({itemId})/itemVariants
Authorization: Bearer {access_token}
Accept: application/json
```

### 3. Create an Item Variant

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/items({itemId})/itemVariants
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "code": "GREEN",
  "description": "Green color variant"
}
```

### 4. Update an Item Variant

```http
PATCH {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/itemVariants({id})
Authorization: Bearer {access_token}
Content-Type: application/json
If-Match: {etag}

{
  "description": "Dark Green color variant"
}
```

### 5. Delete an Item Variant

```http
DELETE {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/itemVariants({id})
Authorization: Bearer {access_token}
If-Match: {etag}
```

---

## Field Definitions

| Field                    | Type     | Editable | Description                         |
|--------------------------|----------|----------|-------------------------------------|
| `id`                     | GUID     | No       | Unique identifier                   |
| `itemId`                 | GUID     | No       | Parent item identifier              |
| `itemNumber`             | string   | No       | Parent item number                  |
| `code`                   | string   | Yes      | Variant code                        |
| `description`            | string   | Yes      | Variant description                 |
| `lastModifiedDateTime`   | datetime | No       | Last modified timestamp             |

---

## Common Errors

| Status | Error                  | Cause & Resolution                                                                 |
|--------|------------------------|------------------------------------------------------------------------------------|
| `401`  | Unauthorized           | Access token is missing, expired, or invalid. Request a new token.                 |
| `404`  | Not Found              | The item or variant does not exist. Verify the IDs.                                |
| `409`  | Conflict               | ETag mismatch — the record was modified since your last GET. Fetch and retry.      |
