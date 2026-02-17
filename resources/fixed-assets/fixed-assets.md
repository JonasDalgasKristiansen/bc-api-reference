# Fixed Assets

Fixed assets represent long-term physical assets like machinery, vehicles, buildings, and IT equipment. The API supports full CRUD operations for managing your asset register.

---

## Base Endpoint

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/fixedAssets
```

---

## Operations

### 1. List All Fixed Assets

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/fixedAssets
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
      "number": "FA000010",
      "displayName": "Dell Laptop - Finance Dept",
      "fixedAssetLocationCode": "MAIN",
      "fixedAssetLocationId": "d1e2f3a4-b5c6-7890-1234-567890abcdef",
      "classCode": "TANGIBLE",
      "subclassCode": "IT-EQUIP",
      "blocked": false,
      "serialNumber": "DL-2024-001234",
      "employeeNumber": "EMP001",
      "employeeId": "b2c3d4e5-f6a7-8901-bcde-f23456789012",
      "underMaintenance": false,
      "lastModifiedDateTime": "2024-06-15T10:00:00Z"
    }
  ]
}
```

### 2. Create a Fixed Asset

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/fixedAssets
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "displayName": "Office Projector - Conference Room A",
  "fixedAssetLocationCode": "MAIN",
  "classCode": "TANGIBLE",
  "subclassCode": "IT-EQUIP",
  "serialNumber": "PJ-2024-005678"
}
```

### 3. Update a Fixed Asset

```http
PATCH {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/fixedAssets({id})
Authorization: Bearer {access_token}
Content-Type: application/json
If-Match: {etag}

{
  "employeeNumber": "EMP002",
  "underMaintenance": true
}
```

### 4. Delete a Fixed Asset

```http
DELETE {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/fixedAssets({id})
Authorization: Bearer {access_token}
If-Match: {etag}
```

---

## Field Definitions

| Field                     | Type     | Editable | Description                         |
|---------------------------|----------|----------|-------------------------------------|
| `id`                      | GUID     | No       | Unique identifier                   |
| `number`                  | string   | Yes      | Fixed asset number                  |
| `displayName`             | string   | Yes      | Asset description / name            |
| `fixedAssetLocationCode`  | string   | Yes      | Location code                       |
| `fixedAssetLocationId`    | GUID     | Yes      | Location identifier                 |
| `classCode`               | string   | Yes      | Asset class code                    |
| `subclassCode`            | string   | Yes      | Asset subclass code                 |
| `blocked`                 | boolean  | Yes      | Whether the asset is blocked        |
| `serialNumber`            | string   | Yes      | Serial number                       |
| `employeeNumber`          | string   | Yes      | Responsible employee number         |
| `employeeId`              | GUID     | Yes      | Responsible employee identifier     |
| `underMaintenance`        | boolean  | Yes      | Whether the asset is under maintenance |
| `lastModifiedDateTime`    | datetime | No       | Last modified timestamp             |

---

## Fixed Asset Locations

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/fixedAssetLocations
```

### List Locations

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/fixedAssetLocations
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "value": [
    {
      "id": "d1e2f3a4-b5c6-7890-1234-567890abcdef",
      "code": "MAIN",
      "displayName": "Main Office",
      "lastModifiedDateTime": "2024-06-15T10:00:00Z"
    }
  ]
}
```

| Field                    | Type     | Editable | Description                         |
|--------------------------|----------|----------|-------------------------------------|
| `id`                     | GUID     | No       | Unique identifier                   |
| `code`                   | string   | Yes      | Location code                       |
| `displayName`            | string   | Yes      | Location name                       |
| `lastModifiedDateTime`   | datetime | No       | Last modified timestamp             |

---

## OData Query Examples

### Filter by class

```
$filter=classCode eq 'TANGIBLE'
```

### Search by name

```
$filter=contains(displayName, 'Laptop')
```

### Filter assets under maintenance

```
$filter=underMaintenance eq true
```

### Filter by responsible employee

```
$filter=employeeNumber eq 'EMP001'
```

---

## Common Errors

| Status | Error                  | Cause & Resolution                                                                 |
|--------|------------------------|------------------------------------------------------------------------------------|
| `401`  | Unauthorized           | Access token is missing, expired, or invalid. Request a new token.                 |
| `404`  | Not Found              | The fixed asset does not exist. Verify the ID.                                     |
| `409`  | Conflict               | ETag mismatch — the record was modified since your last GET. Fetch and retry.      |
