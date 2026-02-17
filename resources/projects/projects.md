# Projects (Jobs)

Projects represent jobs in Business Central. The BC API v2.0 exposes the Job table under the entity name `projects` (NOT `jobs`). Projects are the parent records for time registration entries — use `timeRegistrationEntries` to log hours against projects.

> **IMPORTANT:** The API endpoint is `projects`, not `jobs`. There are no `jobJournals` or `jobJournalLines` endpoints in the API v2.0 — time tracking is done via `timeRegistrationEntries`.

---

## Base Endpoint

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/projects
```

---

## Operations

### 1. List All Projects

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/projects
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
      "number": "J10000",
      "displayName": "Office Renovation Project",
      "lastModifiedDateTime": "2024-06-15T10:00:00Z"
    },
    {
      "@odata.etag": "W/\"JzIwOzEyMzQ1Njc4OTA7Jw==\"",
      "id": "b2c3d4e5-f6a7-8901-bcde-f23456789012",
      "number": "J20000",
      "displayName": "IT Infrastructure Upgrade",
      "lastModifiedDateTime": "2024-06-10T15:30:00Z"
    }
  ]
}
```

### 2. Get a Single Project

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/projects({id})
Authorization: Bearer {access_token}
Accept: application/json
```

### 3. Create a Project

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/projects
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "number": "J30000",
  "displayName": "New Client Onboarding"
}
```

**Response:** `201 Created`

```json
{
  "@odata.etag": "W/\"JzIxOzEyMzQ1Njc4OTA7Jw==\"",
  "id": "c3d4e5f6-a7b8-9012-cdef-345678901234",
  "number": "J30000",
  "displayName": "New Client Onboarding",
  "lastModifiedDateTime": "2024-06-17T09:00:00Z"
}
```

### 4. Update a Project

```http
PATCH {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/projects({id})
Authorization: Bearer {access_token}
Content-Type: application/json
If-Match: {etag}

{
  "displayName": "New Client Onboarding — Phase 2"
}
```

### 5. Delete a Project

```http
DELETE {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/projects({id})
Authorization: Bearer {access_token}
If-Match: {etag}
```

**Response:** `204 No Content`

---

## Field Definitions

| Field                    | Type     | Editable | Description                      |
|--------------------------|----------|----------|----------------------------------|
| `id`                     | GUID     | No       | Unique identifier                |
| `number`                 | string   | Yes      | Project/job number               |
| `displayName`            | string   | Yes      | Project display name             |
| `lastModifiedDateTime`   | datetime | No       | Last modified timestamp          |

---

## Sub-Resources

| Sub-Resource         | Path                                          | Methods                    |
|----------------------|-----------------------------------------------|----------------------------|
| documentAttachments  | `.../projects({id})/documentAttachments`       | GET, POST, PATCH, DELETE   |
| pdfDocument          | `.../projects({id})/pdfDocument`               | GET                        |

---

## OData Query Examples

### Filter by project number

```
$filter=number eq 'J10000'
```

### Search by name

```
$filter=contains(displayName, 'Renovation')
```

### Select specific fields

```
$select=number,displayName
```

### Sort by last modified

```
$orderby=lastModifiedDateTime desc
```

---

## Common Errors

| Status | Error                  | Cause & Resolution                                                                 |
|--------|------------------------|------------------------------------------------------------------------------------|
| `401`  | Unauthorized           | Access token is missing, expired, or invalid. Request a new token.                 |
| `404`  | Not Found              | The project does not exist. Verify the ID or number.                               |
| `409`  | Conflict               | ETag mismatch — the record was modified since your last GET. Fetch and retry.      |

---

## Important Notes

- The BC API entity name is `projects`, NOT `jobs` — even though the BC table is called "Job"
- There are no `jobJournals` or `jobJournalLines` endpoints — use `timeRegistrationEntries` to log hours
- Use `jobNumber` in `timeRegistrationEntries` to reference a project by its number
- The `projects` endpoint has relatively few fields — most project detail is managed in the BC web client
