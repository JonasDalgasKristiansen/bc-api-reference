# Time Registration Entries

Time registration entries allow employees to log hours worked on jobs/projects or record absences. This is the primary endpoint for time tracking in Business Central.

---

## ⚠️ CRITICAL: Time Sheets vs Time Registration Entries

Business Central has a **Time Sheet** system that works as follows:

| Concept                    | Description                                                              |
|----------------------------|--------------------------------------------------------------------------|
| **Time Sheet (Header)**    | A weekly container per Resource (e.g., TS00007, Week 8, Resource R0010). Created in BC via the **"Create Time Sheets"** batch job. **NOT created via API.** |
| **Time Sheet Lines**       | Individual work entries (job, task, hours) inside a Time Sheet. These are what you add. |
| **Time Registration Entry**| API endpoint that adds a **line** to an existing Time Sheet. Each POST adds a line, it does NOT create a new Time Sheet. |

### The Correct Workflow

```
1. Time Sheets ALREADY EXIST in BC (pre-created weekly by administrator via "Create Time Sheets" batch job)
2. You use the timeRegistrationEntries API to ADD LINES to the existing Time Sheet
3. Each POST adds a line (hours on a specific date) to the Time Sheet that covers that date's week
4. NEVER try to create a Time Sheet header via API — they must already exist in BC
```

### Common Mistake (WRONG ❌)

Trying to create a new Time Sheet header for each time entry. This results in multiple duplicate Time Sheet records.

### Correct Approach (RIGHT ✅)

1. **Query existing entries** for the employee and current week first
2. **POST a new entry** with a `date` that falls within an existing Time Sheet's weekly period
3. BC automatically places the entry as a **new line** inside the matching Time Sheet
4. To add multiple days of work, POST **one entry per date** — they all become separate lines in the same Time Sheet

### Step-by-Step Example: Add Hours to This Week's Time Sheet

**Step 1 — Confirm the employee exists and get their number:**

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/employees?$filter=number eq 'R0010'&$select=id,number,displayName
```

**Step 2 — Check what entries already exist for the current week (to avoid duplicates):**

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/timeRegistrationEntries?$filter=employeeNumber eq 'R0010' and date ge 2026-02-16 and date le 2026-02-22
```

**Step 3 — Add hours for a specific date (this becomes a LINE in the existing Time Sheet for that week):**

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/timeRegistrationEntries
Content-Type: application/json
Authorization: Bearer {access_token}

{
  "employeeNumber": "R0010",
  "date": "2026-02-17",
  "quantity": 8.0,
  "jobNumber": "J10000",
  "jobTaskNumber": "JT1000"
}
```

**Step 4 — Add another day to the SAME Time Sheet (different date, same week):**

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/timeRegistrationEntries
Content-Type: application/json
Authorization: Bearer {access_token}

{
  "employeeNumber": "R0010",
  "date": "2026-02-18",
  "quantity": 7.5,
  "jobNumber": "J10000",
  "jobTaskNumber": "JT1000"
}
```

> Both entries above become lines inside the **same** Time Sheet (the one covering Week 8, 16/02/2026–22/02/2026 for resource R0010).

### What NOT to Do

- ❌ Do NOT post entries with dates across multiple weeks in one batch — each week has a separate Time Sheet
- ❌ Do NOT try to create Time Sheet headers via the API — use "Create Time Sheets" in BC
- ❌ Do NOT create one entry per hour — create one entry per date with total `quantity` (hours) for that day
- ❌ Do NOT assign entries to a Resource number directly — use `employeeNumber` which corresponds to the Resource's linked employee

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

### 4. Create a Time Registration Entry (Add a Line to Existing Time Sheet)

> **IMPORTANT:** This adds a line to the Time Sheet that covers the specified `date`. The Time Sheet must already exist in BC for that employee's resource and week. Do NOT use this to create a new Time Sheet — Time Sheets are created via the "Create Time Sheets" batch job in the BC client.

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

- **Time Sheets are NOT created via the API** — they are pre-created in BC via the "Create Time Sheets" batch job. The `timeRegistrationEntries` endpoint only adds LINES to existing Time Sheets.
- **One entry = one line in an existing Time Sheet.** POST one entry per date. All entries for dates within the same week go into the same Time Sheet automatically.
- **Never create multiple entries for the same employee + same date + same job** — update the existing entry's `quantity` instead with PATCH.
- Use `jobNumber` and `jobTaskNumber` (NOT `jobNo` or `jobTaskNo`) — these are the correct API field names
- Entries with `status` other than `Open` cannot be edited or deleted
- If both `jobNumber` and `absence` are empty, the entry is a general time entry
- The `employee` sub-resource endpoint is useful when building per-employee time views
- `lastModfiedDateTime` has a typo (missing 'i') — this is Microsoft's actual field name, use it as-is
- The `employeeNumber` field corresponds to the **Resource No.** column shown on the Time Sheet page in BC
