# Companies

The companies endpoint is the root of all Business Central API calls. It lists all companies accessible with your credentials and is the **only root-level endpoint** — every other endpoint requires a company ID in the path.

---

## Endpoint

```
GET {{BC_BASE_URL}}/companies
```

> **Note:** This endpoint does NOT require a company ID — it is used to discover available companies and their IDs.

---

## List All Companies

### Request

```http
GET {{BC_BASE_URL}}/companies
Authorization: Bearer {access_token}
Accept: application/json
```

### Response

```json
{
  "@odata.context": "https://api.businesscentral.dynamics.com/v2.0/e3a1b2c3-d4e5-6789-abcd-ef0123456789/Production/api/v2.0/$metadata#companies",
  "value": [
    {
      "id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
      "systemVersion": "22.0.54321.0",
      "timestamp": 78654321,
      "name": "CRONUS USA, Inc.",
      "displayName": "CRONUS USA, Inc.",
      "businessProfileId": "",
      "systemCreatedAt": "2023-01-15T08:30:00Z",
      "systemCreatedBy": "00000000-0000-0000-0000-000000000001",
      "systemModifiedAt": "2025-02-10T14:22:18Z",
      "systemModifiedBy": "b5c6d7e8-f9a0-1234-5678-90abcdef1234"
    },
    {
      "id": "f9e8d7c6-b5a4-3210-fedc-ba9876543210",
      "systemVersion": "22.0.54321.0",
      "timestamp": 78654322,
      "name": "My Company",
      "displayName": "My Company",
      "businessProfileId": "SMALL-BUS-001",
      "systemCreatedAt": "2024-03-01T10:00:00Z",
      "systemCreatedBy": "00000000-0000-0000-0000-000000000001",
      "systemModifiedAt": "2025-01-28T09:15:30Z",
      "systemModifiedBy": "b5c6d7e8-f9a0-1234-5678-90abcdef1234"
    }
  ]
}
```

---

## Get a Single Company

### Request

```http
GET {{BC_BASE_URL}}/companies(a1b2c3d4-e5f6-7890-abcd-ef1234567890)
Authorization: Bearer {access_token}
Accept: application/json
```

### Response

```json
{
  "@odata.context": "https://api.businesscentral.dynamics.com/v2.0/e3a1b2c3-d4e5-6789-abcd-ef0123456789/Production/api/v2.0/$metadata#companies/$entity",
  "@odata.etag": "W/\"JzE5OzEyMzQ1Njc4OTAxMjM0NTY3ODkwMTtKT0hOOw==\"",
  "id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "systemVersion": "22.0.54321.0",
  "timestamp": 78654321,
  "name": "CRONUS USA, Inc.",
  "displayName": "CRONUS USA, Inc.",
  "businessProfileId": "",
  "systemCreatedAt": "2023-01-15T08:30:00Z",
  "systemCreatedBy": "00000000-0000-0000-0000-000000000001",
  "systemModifiedAt": "2025-02-10T14:22:18Z",
  "systemModifiedBy": "b5c6d7e8-f9a0-1234-5678-90abcdef1234"
}
```

---

## Fields

| Field              | Type     | Description                                              |
|--------------------|----------|----------------------------------------------------------|
| `id`               | GUID     | Unique identifier for the company                        |
| `systemVersion`    | string   | BC system version                                        |
| `timestamp`        | integer  | Internal row version number                              |
| `name`             | string   | System name of the company                               |
| `displayName`      | string   | Display name shown in the UI                             |
| `businessProfileId`| string   | Business profile identifier (may be empty)               |
| `systemCreatedAt`  | datetime | When the company was created                             |
| `systemCreatedBy`  | GUID     | User ID who created the company                          |
| `systemModifiedAt` | datetime | When the company was last modified                       |
| `systemModifiedBy` | GUID     | User ID who last modified the company                    |

---

## OData Query Examples

```http
# Filter by name
GET {{BC_BASE_URL}}/companies?$filter=name eq 'CRONUS USA, Inc.'

# Select specific fields only
GET {{BC_BASE_URL}}/companies?$select=id,name,displayName

# Order by name
GET {{BC_BASE_URL}}/companies?$orderby=name asc
```

---

## Common Errors

| Status | Error                  | Cause & Resolution                                                                 |
|--------|------------------------|------------------------------------------------------------------------------------|
| `401`  | Unauthorized           | Access token is missing, expired, or invalid. Request a new token.                 |
| `403`  | Forbidden              | The app registration does not have permission to access BC. Check API permissions.  |
| `404`  | Not Found              | The company ID does not exist. Verify the GUID is correct.                         |
