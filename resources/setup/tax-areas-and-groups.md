# Tax Areas

Tax areas define geographic regions for sales tax calculation. Each customer and transaction can be assigned to a tax area.

---

## Base Endpoint

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/taxAreas
```

---

## Operations

### 1. List All Tax Areas

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/taxAreas
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
      "code": "ATLANTA, GA",
      "displayName": "Atlanta, GA",
      "taxType": "Sales Tax",
      "lastModifiedDateTime": "2024-01-01T00:00:00Z"
    }
  ]
}
```

### 2. Create / Update / Delete

Standard CRUD operations (POST, PATCH with If-Match, DELETE with If-Match).

---

## Field Definitions

| Field                    | Type     | Editable | Description                         |
|--------------------------|----------|----------|-------------------------------------|
| `id`                     | GUID     | No       | Unique identifier                   |
| `code`                   | string   | Yes      | Tax area code                       |
| `displayName`            | string   | Yes      | Display name                        |
| `taxType`                | enum     | No       | `Sales Tax` or `VAT`               |
| `lastModifiedDateTime`   | datetime | No       | Last modified timestamp             |

---

# Tax Groups

Tax groups define categories of items for tax purposes (e.g., taxable, non-taxable, labor, supplies).

---

## Base Endpoint

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/taxGroups
```

---

## Operations

### 1. List All Tax Groups

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/taxGroups
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
      "code": "TAXABLE",
      "displayName": "Taxable items",
      "taxType": "Sales Tax",
      "lastModifiedDateTime": "2024-01-01T00:00:00Z"
    }
  ]
}
```

---

## Field Definitions

| Field                    | Type     | Editable | Description                         |
|--------------------------|----------|----------|-------------------------------------|
| `id`                     | GUID     | No       | Unique identifier                   |
| `code`                   | string   | Yes      | Tax group code                      |
| `displayName`            | string   | Yes      | Display name                        |
| `taxType`                | enum     | No       | `Sales Tax` or `VAT`               |
| `lastModifiedDateTime`   | datetime | No       | Last modified timestamp             |
