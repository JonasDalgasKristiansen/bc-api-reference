# Time Registration Entries

Time registration entries allow employees to log hours worked on jobs/projects or record absences. This is the primary endpoint for time tracking in Business Central. Entries go through an approval workflow (Open → Submitted → Approved/Rejected).

---

## Base Endpoint

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/timeRegistrationEntries
```

Also available as a sub-resource under employees:

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/employees({employeeId})/timeRegistrationEntries
```

---

## Status Values

| Status       | Description                                    |
|--------------|------------------------------------------------|
| `Open`       | Entry is being prepared, editable              |
| `Submitted`  | Entry has been submitted for approval          |
| `Rejected`   | Entry was rejected by approver                 |
| `Approved`   | Entry has been approved                        |

---

## Operations

### 1. List All Time Registration Entries

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/timeRegistrationEntries
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
      "employeeId": "d1e2f3a4-b5c6-7890-1234-567890abcdef",
      "employeeNumber": "EMP001",
      "jobId": "00000000-0000-0000-0000-000000000000",
      "jobNumber": "J10000",
      "jobTaskNumber": "JT1000",
      "absence": "",
      "lineNumber": 10000,
      "date": "2024-06-15",
      "quantity": 7.5,
      "status": "Approved",
      "unitOfMeasureId": "b2c3d4e5-f6a7-8901-bcde-f23456789012",
      "unitOfMeasureCode": "HOUR",
      "lastModfiedDateTime": "2024-06-15T14:30:00Z"
    }
  ]
}
```

> **Note:** The field `lastModfiedDateTime` is intentionally misspelled (missing an 'i') — this is the actual field name in Microsoft's API.

### 2. Get Entries for a Specific Employee

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/employees({employeeId})/timeRegistrationEntries
Authorization: Bearer {access_token}
Accept: application/json
```

### 3. Get a Single Entry

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/timeRegistrationEntries({id})
Authorization: Bearer {access_token}
Accept: application/json
```

### 4. Create a Time Registration Entry

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/timeRegistrationEntries
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "employeeNumber": "EMP001",
  "jobNumber": "J10000",
  "jobTaskNumber": "JT1000",
  "date": "2024-06-17",
  "quantity": 8.0
}
```

**Response:** `201 Created`

```json
{
  "@odata.etag": "W/\"JzIwOzEyMzQ1Njc4OTA7Jw==\"",
  "id": "c3d4e5f6-a7b8-9012-cdef-345678901234",
  "employeeId": "d1e2f3a4-b5c6-7890-1234-567890abcdef",
  "employeeNumber": "EMP001",
  "jobId": "e4f5a6b7-c8d9-0123-efab-456789012345",
  "jobNumber": "J10000",
  "jobTaskNumber": "JT1000",
  "absence": "",
  "lineNumber": 20000,
  "date": "2024-06-17",
  "quantity": 8.0,
  "status": "Open",
  "unitOfMeasureId": "b2c3d4e5-f6a7-8901-bcde-f23456789012",
  "unitOfMeasureCode": "HOUR",
  "lastModfiedDateTime": "2024-06-17T09:00:00Z"
}
```

### 5. Create a Time Entry for Absence

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/timeRegistrationEntries
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "employeeNumber": "EMP001",
  "absence": "Vacation",
  "date": "2024-06-18",
  "quantity": 8.0
}
```

### 6. Update a Time Registration Entry

```http
PATCH {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/timeRegistrationEntries({id})
Authorization: Bearer {access_token}
Content-Type: application/json
If-Match: {etag}

{
  "quantity": 6.5
}
```

**Response:** `200 OK` with updated entity.

### 7. Delete a Time Registration Entry

```http
DELETE {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/timeRegistrationEntries({id})
Authorization: Bearer {access_token}
If-Match: {etag}
```

**Response:** `204 No Content`

> Only entries with `status` = `Open` can be deleted.

---

## Field Definitions

| Field                  | Type     | Editable | Description                                       |
|------------------------|----------|----------|---------------------------------------------------|
| `id`                   | GUID     | No       | Unique identifier                                 |
| `employeeId`           | GUID     | Yes      | Employee identifier                               |
| `employeeNumber`       | string   | Yes      | Employee number (alternative to employeeId)       |
| `jobId`                | GUID     | No       | Project/job identifier (read-only, set via jobNumber) |
| `jobNumber`            | string   | Yes      | Project/job number                                |
| `jobTaskNumber`        | string   | Yes      | Job task number within the project                |
| `absence`              | string   | Yes      | Absence code (e.g., "Vacation", "Sick")           |
| `lineNumber`           | integer  | No       | Line number (auto-assigned)                       |
| `date`                 | date     | Yes      | Date the time was worked                          |
| `quantity`             | decimal  | Yes      | Number of hours                                   |
| `status`               | enum     | No       | Open, Submitted, Rejected, Approved               |
| `unitOfMeasureId`      | GUID     | Yes      | Unit of measure identifier                        |
| `unitOfMeasureCode`    | string   | Yes      | Unit of measure code (e.g., "HOUR")               |
| `lastModfiedDateTime`  | datetime | No       | Last modified timestamp (note: typo is intentional) |

---

## Sub-Resources

| Sub-Resource       | Path                                              | Methods                    |
|--------------------|---------------------------------------------------|----------------------------|
| dimensionSetLines  | `.../timeRegistrationEntries({id})/dimensionSetLines` | GET, POST, PATCH, DELETE |

---

## OData Query Examples

### Filter by employee

```
$filter=employeeNumber eq 'EMP001'
```

### Filter by date range

```
$filter=date ge 2024-06-01 and date le 2024-06-30
```

### Filter by project

```
$filter=jobNumber eq 'J10000'
```

### Filter by status

```
$filter=status eq 'Open'
```

### Get approved entries for a specific week

```
$filter=date ge 2024-06-17 and date le 2024-06-21 and status eq 'Approved'
```

### Select specific fields

```
$select=employeeNumber,jobNumber,jobTaskNumber,date,quantity,status
```

---

## Common Errors

| Status | Error                  | Cause & Resolution                                                                 |
|--------|------------------------|------------------------------------------------------------------------------------|
| `401`  | Unauthorized           | Access token is missing, expired, or invalid. Request a new token.                 |
| `404`  | Not Found              | The entry or employee does not exist. Verify the IDs.                              |
| `400`  | Bad Request            | Invalid field values — check that date format is YYYY-MM-DD, quantity is numeric.  |
| `409`  | Conflict               | ETag mismatch — the record was modified since your last GET. Fetch and retry.      |

---

## Important Notes

- Use `jobNumber` and `jobTaskNumber` (NOT `jobNo` or `jobTaskNo`) — these are the correct API field names
- Entries with `status` other than `Open` cannot be edited or deleted
- If both `jobNumber` and `absence` are empty, the entry is a general time entry
- The `employee` sub-resource endpoint is useful when building per-employee time sheets
- `lastModfiedDateTime` has a typo (missing 'i') — this is Microsoft's actual field name, use it as-is
