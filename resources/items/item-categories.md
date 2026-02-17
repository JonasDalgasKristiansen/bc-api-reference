# Item Categories

Item categories are used to group and classify items in Business Central. They provide a hierarchical way to organize your product catalog. Categories are referenced by items through the `itemCategoryCode` field.

---

## Base Endpoint

```
{{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/itemCategories
```

---

## Operations

### 1. List All Item Categories

```http
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/itemCategories
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "@odata.context": "https://api.businesscentral.dynamics.com/v2.0/.../itemCategories",
  "value": [
    {
      "@odata.etag": "W/\"JzE5OzExMTExMTExMTE7Q0FUMQ==\"",
      "id": "c2d3e4f5-a6b7-8901-2345-222222222222",
      "code": "MISC",
      "displayName": "Miscellaneous",
      "lastModifiedDateTime": "2024-06-15T10:00:00Z"
    },
    {
      "@odata.etag": "W/\"JzE5OzIyMjIyMjIyMjI7Q0FUMg==\"",
      "id": "d3e4f5a6-b7c8-9012-3456-888888888888",
      "code": "FURNITURE",
      "displayName": "Office Furniture",
      "lastModifiedDateTime": "2024-06-15T10:00:00Z"
    },
    {
      "@odata.etag": "W/\"JzE5OzMzMzMzMzMzMzM7Q0FUMw==\"",
      "id": "e4f5a6b7-c8d9-0123-4567-aaaaaaaaaaaa",
      "code": "ELECTRONICS",
      "displayName": "Electronics & Accessories",
      "lastModifiedDateTime": "2024-08-20T14:30:00Z"
    }
  ]
}
```

### 2. Get a Single Item Category

```http
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/itemCategories(d3e4f5a6-b7c8-9012-3456-888888888888)
Authorization: Bearer {access_token}
Accept: application/json
```

### 3. Create an Item Category

```http
POST {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/itemCategories
Authorization: Bearer {access_token}
Content-Type: application/json
```

**Request Body:**

```json
{
  "code": "SOFTWARE",
  "displayName": "Software & Licenses"
}
```

**Response (201 Created):**

```json
{
  "@odata.etag": "W/\"JzE5OzQ0NDQ0NDQ0NDQ7Q0FUNQ==\"",
  "id": "f5a6b7c8-d9e0-1234-5678-bbbbbbbbbbbb",
  "code": "SOFTWARE",
  "displayName": "Software & Licenses",
  "lastModifiedDateTime": "2025-02-17T10:00:00Z"
}
```

### 4. Update an Item Category

```http
PATCH {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/itemCategories(d3e4f5a6-b7c8-9012-3456-888888888888)
Authorization: Bearer {access_token}
Content-Type: application/json
If-Match: W/"JzE5OzIyMjIyMjIyMjI7Q0FUMg=="
```

**Request Body:**

```json
{
  "displayName": "Office & Home Furniture"
}
```

### 5. Delete an Item Category

```http
DELETE {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/itemCategories(d3e4f5a6-b7c8-9012-3456-888888888888)
Authorization: Bearer {access_token}
If-Match: W/"JzE5OzIyMjIyMjIyMjI7Q0FUMg=="
```

**Response:** `204 No Content`

---

## Fields

| Field                  | Type     | Writable | Description                               |
|------------------------|----------|----------|-------------------------------------------|
| `id`                   | GUID     | No       | Unique identifier                         |
| `code`                 | string   | Yes      | Category code (e.g., `FURNITURE`)         |
| `displayName`          | string   | Yes      | Display name                              |
| `lastModifiedDateTime` | datetime | No       | Last modification timestamp               |

---

## Usage Notes

- `itemCategoryCode` on the items resource references these codes
- Deleting a category that is in use by items will fail with a `400` error
- Categories are used for reporting and filtering items

---

## ETag / If-Match

When updating (PATCH) or deleting (DELETE) an item category, include the `If-Match` header with the `@odata.etag` from a prior GET.

---

## Common Errors

| Status | Error                  | Cause & Resolution                                                                 |
|--------|------------------------|------------------------------------------------------------------------------------|
| `401`  | Unauthorized           | Access token is missing, expired, or invalid. Request a new token.                 |
| `404`  | Not Found              | The item category ID does not exist. Verify the GUID.                              |
| `400`  | Bad Request            | Invalid field values, or trying to delete a category in use by items.              |
| `409`  | Conflict               | ETag mismatch on PATCH/DELETE. GET the record again and retry with the new ETag.   |
