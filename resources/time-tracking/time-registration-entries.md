# Time Registration Entries

Time registration entries allow employees to log hours worked on jobs/projects or record absences. This is the primary endpoint for time tracking in Business Central.

---

## ⚠️ CRITICAL: Time Sheets vs Time Registration Entries

Business Central has a **Time Sheet** system that works as follows:

| Concept                    | Description                                                              |
|----------------------------|--------------------------------------------------------------------------|
| **Time Sheet (Header)**    | A weekly container per Resource (e.g., TS00007, Week 8, Resource R0010). Created in BC via the **"Create Time Sheets"** batch job. **NOT created via API.** |
| **Time Sheet Lines**       | Individual work entries (job, task, hours) inside a Time Sheet. These are what you add via the API. |
| **Time Registration Entry**| API endpoint that adds a **line** to an existing Time Sheet. Each POST adds a line — it does NOT create a new Time Sheet. |

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

---

## Employee-to-Resource Mapping

In BC, **Employees** and **Resources** are two separate tables. Time Sheets belong to a **Resource**, but the API uses `employeeNumber`. The mapping works like this:

| Concept | Table | Example | Used Where |
|---------|-------|---------|------------|
| **Resource No.** | Resource | `R0010` | Time Sheet header, Time Sheet page in BC |
| **Employee Number** | Employee | `R0010` | API `employeeNumber` field |

The employee must be linked to a resource in BC (via the Resource card → Related → Employee Link). When you POST with `employeeNumber`, BC resolves the linked resource and places the entry in that resource's Time Sheet.

### How to verify the mapping

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/employees?$select=id,number,displayName
```

The `number` field returned here is the value you use as `employeeNumber` in time entry POSTs.

---

## Time Sheet Fields vs API Fields

The Time Sheet page in BC shows several columns. **Not all of them are available in the API:**

| Time Sheet Column         | API Field            | Can You Set It? | Notes |
|---------------------------|----------------------|-----------------|-------|
| **Type**                  | *(auto-determined)*  | ❌ Not settable  | BC sets this automatically based on what fields you include (see below) |
| **Status**                | `status`             | ❌ Read-only     | `Open`, `Submitted`, `Rejected`, `Approved` — can only read, not change via API |
| **Description**           | —                    | ❌ Not in API    | The Time Sheet Line description is not exposed in the `timeRegistrationEntries` entity |
| **Project No.**           | `jobNumber`          | ✅ Yes           | Sets the project/job. Also auto-sets Type to `Job` |
| **Project Task No.**      | `jobTaskNumber`      | ✅ Yes (optional) | **OPTIONAL.** Task within the project. Only use if you specifically need task-level tracking. Most apps only need `jobNumber`. |
| **Cause of Absence Code** | `absence`            | ✅ Yes           | Sets the absence reason. Also auto-sets Type to `Absence` |
| **Chargeable**            | —                    | ❌ Not in API    | The chargeable flag is not exposed in the standard v2.0 API |
| **Work Code**             | —                    | ❌ Not in API    | Work codes are not exposed in the standard v2.0 API |
| **Mon–Sun hours**         | `date` + `quantity`  | ✅ Yes           | Each day is a separate POST with a `date` and `quantity` (hours) |
| **Total**                 | *(calculated)*       | ❌ Read-only     | Sum of all entries for the week — calculated by BC |

> **Why AI tools can't fill in Description, Chargeable, or Work Code:** These fields exist in BC's Time Sheet Line table but Microsoft's standard `timeRegistrationEntries` API entity simply does not include them. Only the fields listed in the [Field Definitions](#field-definitions) section below are available.

### How the Type Column Works

You don't set `Type` directly. BC determines it based on what you include in the POST body:

| What You Include | Type Becomes | Example |
|------------------|-------------|---------|
| `jobNumber` (optionally with `jobTaskNumber`) | **Job** | `{ "employeeNumber": "R0010", "jobNumber": "J10000", "date": "2026-02-17", "quantity": 8 }` |
| `absence` | **Absence** | `{ "employeeNumber": "R0010", "absence": "Vacation", "date": "2026-02-17", "quantity": 8 }` |
| Neither `jobNumber` nor `absence` | **Resource** | `{ "employeeNumber": "R0010", "date": "2026-02-17", "quantity": 8 }` |

> **Do NOT set both `jobNumber` and `absence`** on the same entry — use one or the other.

---

## Status Workflow

| Status       | Description                                    | Can Edit? | Can Delete? |
|--------------|------------------------------------------------|-----------|-------------|
| `Open`       | Entry is being prepared                        | ✅ Yes    | ✅ Yes      |
| `Submitted`  | Submitted for approval                         | ❌ No     | ❌ No       |
| `Rejected`   | Rejected by approver (can be reopened in BC)   | ❌ No     | ❌ No       |
| `Approved`   | Approved by manager                            | ❌ No     | ❌ No       |

> **Status transitions happen in BC, not via the API.** The API cannot submit, approve, or reject entries. Users must do this from the Time Sheet page in Business Central.

---

## What NOT to Do

- ❌ Do NOT POST to `/employees({id})/timeRegistrationEntries` — always POST to the **top-level** `/timeRegistrationEntries`
- ❌ Do NOT use `employeeId` (GUID) in the POST body — use `employeeNumber` (short code like `"MH"`, `"R0010"`)
- ❌ Do NOT post entries with dates across multiple weeks in one batch — each week has a separate Time Sheet
- ❌ Do NOT try to create Time Sheet headers via the API — use "Create Time Sheets" in BC
- ❌ Do NOT create one entry per hour — create one entry per date with total `quantity` (hours) for that day
- ❌ Do NOT assign entries to a Resource number directly — use `employeeNumber` which corresponds to the Resource's linked employee
- ❌ Do NOT set both `jobNumber` and `absence` on the same entry
- ❌ Do NOT try to set `description`, `chargeable`, or `workCode` — these fields are not in the API
- ❌ Do NOT try to change `status` via PATCH — status transitions are done in BC's Time Sheet UI

---

## Base Endpoint — ALWAYS USE THIS

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/timeRegistrationEntries
```

> **⚠️ ALWAYS use the top-level endpoint above for creating time entries.**
> 
> There is also a sub-resource under employees (`/employees({employeeId})/timeRegistrationEntries`) but **DO NOT use it for POST/create operations** — it requires a GUID and causes errors. Use it only for GET (reading entries for a specific employee).
>
> **For creating entries:** POST to the top-level `/timeRegistrationEntries` endpoint and pass `employeeNumber` (the short code like `"MH"` or `"R0010"`) in the JSON body — NOT `employeeId` (a GUID).

### Read-only sub-resource (GET only)

```
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/employees({employeeId})/timeRegistrationEntries
```

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
      "employeeNumber": "R0010",
      "jobId": "00000000-0000-0000-0000-000000000000",
      "jobNumber": "J10000",
      "jobTaskNumber": "",
      "absence": "",
      "lineNumber": 10000,
      "date": "2026-02-17",
      "quantity": 7.5,
      "status": "Open",
      "unitOfMeasureId": "b2c3d4e5-f6a7-8901-bcde-f23456789012",
      "unitOfMeasureCode": "HOUR",
      "lastModfiedDateTime": "2026-02-17T14:30:00Z"
    }
  ]
}
```

> **Note:** The field `lastModfiedDateTime` is intentionally misspelled (missing an 'i') — this is the actual field name in Microsoft's API.

### 2. Get Entries for a Specific Employee (GET only)

> The employee sub-resource endpoint is only for **reading** entries. Never POST to this URL — always POST to the top-level `/timeRegistrationEntries` endpoint.

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

### 4. Create — Basic Time Entry (General Hours, No Project)

> **IMPORTANT — READ ALL OF THIS:**
> - **URL:** Always POST to the **top-level** `/timeRegistrationEntries` endpoint. NEVER POST to `/employees({id})/timeRegistrationEntries`.
> - **Employee field:** Use `employeeNumber` (the short code like `"MH"`, `"R0010"`) — NOT `employeeId` (a GUID). If you pass `employeeId`, BC returns: *"The Employee does not exist. Identification fields and values: No.=''"*
> - **Time Sheet:** The Time Sheet must already exist in BC for that employee's resource and week. Do NOT try to create a new Time Sheet via the API.
> - **Date:** Must fall within the date range of an existing Time Sheet for this employee's resource. If no Time Sheet covers the date, you'll get an error.

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/timeRegistrationEntries
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "employeeNumber": "R0010",
  "date": "2026-02-17",
  "quantity": 8.0
}
```

**Response:** `201 Created`

```json
{
  "@odata.etag": "W/\"JzIwOzEyMzQ1Njc4OTA7Jw==\"",
  "id": "c3d4e5f6-a7b8-9012-cdef-345678901234",
  "employeeId": "d1e2f3a4-b5c6-7890-1234-567890abcdef",
  "employeeNumber": "R0010",
  "jobId": "00000000-0000-0000-0000-000000000000",
  "jobNumber": "",
  "jobTaskNumber": "",
  "absence": "",
  "lineNumber": 10000,
  "date": "2026-02-17",
  "quantity": 8.0,
  "status": "Open",
  "unitOfMeasureId": "b2c3d4e5-f6a7-8901-bcde-f23456789012",
  "unitOfMeasureCode": "HOUR",
  "lastModfiedDateTime": "2026-02-17T09:00:00Z"
}
```

> This creates a **Resource** type line (no project, no absence).

### 5. Create — Time Entry with Project

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/timeRegistrationEntries
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "employeeNumber": "R0010",
  "date": "2026-02-17",
  "quantity": 8.0,
  "jobNumber": "J10000"
}
```

**Response:** `201 Created` — the entry will have Type = **Job** in the Time Sheet.

> **`jobTaskNumber` is OPTIONAL.** You only need `jobNumber` to log time against a project. Only add `jobTaskNumber` if the user specifically picks a task. See the [Advanced: Job Task Numbers](#advanced-job-task-numbers-optional) section below.

### 6. Create — Absence Entry

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/timeRegistrationEntries
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "employeeNumber": "R0010",
  "absence": "Vacation",
  "date": "2026-02-18",
  "quantity": 8.0
}
```

**Response:** `201 Created` — the entry will have Type = **Absence** in the Time Sheet.

> **Common absence codes:** `"Vacation"`, `"Sick"`, `"Personal"`, `"Training"`. The exact codes available depend on your BC setup — check the **Causes of Absence** page in BC.

### 7. Create — Full Work Week (Monday–Friday)

To register a full work week, POST **one entry per day**. All entries for dates in the same week automatically go into the same Time Sheet.

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/timeRegistrationEntries
Authorization: Bearer {access_token}
Content-Type: application/json

{"employeeNumber": "R0010", "date": "2026-02-16", "quantity": 8.0, "jobNumber": "J10000"}
```

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/timeRegistrationEntries
Authorization: Bearer {access_token}
Content-Type: application/json

{"employeeNumber": "R0010", "date": "2026-02-17", "quantity": 8.0, "jobNumber": "J10000"}
```

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/timeRegistrationEntries
Authorization: Bearer {access_token}
Content-Type: application/json

{"employeeNumber": "R0010", "date": "2026-02-18", "quantity": 8.0, "jobNumber": "J10000"}
```

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/timeRegistrationEntries
Authorization: Bearer {access_token}
Content-Type: application/json

{"employeeNumber": "R0010", "date": "2026-02-19", "quantity": 8.0, "jobNumber": "J10000"}
```

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/timeRegistrationEntries
Authorization: Bearer {access_token}
Content-Type: application/json

{"employeeNumber": "R0010", "date": "2026-02-20", "quantity": 7.5, "jobNumber": "J10000"}
```

> All 5 entries become lines inside the **same** Time Sheet (Week 8, 16/02/2026–22/02/2026, Resource R0010). You **must** send one POST per day — there is no batch/array endpoint.

### 8. Step-by-Step: Complete Time Registration Flow

This is the recommended flow for a time registration app:

**Step 1 — Get the list of employees (for a "Choose Employee" dropdown):**

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/employees?$select=id,number,displayName
```

**Step 2 — Get the list of projects (for a "Choose Project" dropdown):**

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/projects?$select=id,number,displayName
```

> If this returns 404, use the OData fallback — see `resources/projects/projects.md`.

**Step 3 — Check existing entries for the selected employee & week (to avoid duplicates):**

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/timeRegistrationEntries?$filter=employeeNumber eq 'R0010' and date ge 2026-02-16 and date le 2026-02-22&$select=id,employeeNumber,jobNumber,date,quantity,status
```

**Step 4 — Create the time entry:**

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/timeRegistrationEntries
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "employeeNumber": "R0010",
  "date": "2026-02-17",
  "quantity": 8.0,
  "jobNumber": "J10000"
}
```

> **Note:** `jobTaskNumber` is intentionally omitted — it's optional. Only add it if the user specifically needs task-level tracking. See [Advanced: Job Task Numbers](#advanced-job-task-numbers-optional).
```

### 9. Update a Time Registration Entry

> Only entries with `status` = `Open` can be updated. You need the `id` and the `@odata.etag` from a GET request.

```http
PATCH {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/timeRegistrationEntries({id})
Authorization: Bearer {access_token}
Content-Type: application/json
If-Match: W/"JzIwOzEyMzQ1Njc4OTA7Jw=="

{
  "quantity": 6.5
}
```

**Response:** `200 OK` with updated entity.

> You can also update `jobNumber`, `jobTaskNumber`, `absence`, and `date` on open entries.

### 10. Delete a Time Registration Entry

> Only entries with `status` = `Open` can be deleted.

```http
DELETE {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/timeRegistrationEntries({id})
Authorization: Bearer {access_token}
If-Match: W/"JzIwOzEyMzQ1Njc4OTA7Jw=="
```

**Response:** `204 No Content`

---

## Date Handling

- **Date format:** Always `YYYY-MM-DD` (e.g., `2026-02-17`)
- **The date must fall within an existing Time Sheet's week range.** If no Time Sheet exists that covers the given date, BC will return an error.
- **BC Time Sheets are weekly** — typically Monday to Sunday. All entries with dates in the same week go into the same Time Sheet.
- **To find what week a Time Sheet covers:** Check the Time Sheet's start date and it spans 7 days from there.

### How to verify a Time Sheet exists for a given week

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/timeRegistrationEntries?$filter=employeeNumber eq 'R0010' and date ge 2026-02-16 and date le 2026-02-22&$top=1
```

If this returns results, a Time Sheet exists for that employee's resource and week. If it returns empty and a POST for that date also fails, the Time Sheet hasn't been created yet in BC.

---

## Field Definitions

| Field                  | Type     | Editable | Example Value | Description |
|------------------------|----------|----------|---------------|-------------|
| `id`                   | GUID     | No       | `"a1b2c3d4-e5f6-..."` | Unique identifier |
| `employeeId`           | GUID     | No       | `"d1e2f3a4-b5c6-..."` | Employee GUID (read-only — returned in response, do NOT send in POST body) |
| `employeeNumber`       | string   | **Yes** ✅ | `"R0010"` | **USE THIS** — Employee number/code. Always use this when creating entries. |
| `jobId`                | GUID     | No       | `"e4f5a6b7-c8d9-..."` | Project/job GUID (read-only, auto-set via `jobNumber`) |
| `jobNumber`            | string   | **Yes** ✅ | `"J10000"` | Project/job number — sets Type to **Job** |
| `jobTaskNumber`        | string   | **Yes** ✅ | `"JT1000"` | **OPTIONAL.** Job task number within the project. Only needed for task-level tracking. Most apps should omit this. |
| `absence`              | string   | **Yes** ✅ | `"Vacation"` | Absence code — sets Type to **Absence** |
| `lineNumber`           | integer  | No       | `10000` | Auto-assigned line number in the Time Sheet |
| `date`                 | date     | **Yes** ✅ | `"2026-02-17"` | Date the time was worked (YYYY-MM-DD) |
| `quantity`             | decimal  | **Yes** ✅ | `8.0` | Number of hours |
| `status`               | enum     | No       | `"Open"` | `Open`, `Submitted`, `Rejected`, `Approved` |
| `unitOfMeasureId`      | GUID     | Yes      | `"b2c3d4e5-f6a7-..."` | Unit of measure GUID |
| `unitOfMeasureCode`    | string   | Yes      | `"HOUR"` | Unit of measure code |
| `dimensions`           | array    | No       | *(sub-resource)* | Access via `/timeRegistrationEntries({id})/dimensionSetLines` |
| `lastModfiedDateTime`  | datetime | No       | `"2026-02-17T09:00:00Z"` | Last modified (note: typo `Modfied` is intentional — Microsoft's API) |

---

## Sub-Resources

| Sub-Resource       | Path                                              | Methods                    |
|--------------------|---------------------------------------------------|----------------------------|
| dimensionSetLines  | `.../timeRegistrationEntries({id})/dimensionSetLines` | GET, POST, PATCH, DELETE |

---

## OData Query Examples

### Filter by employee

```
$filter=employeeNumber eq 'R0010'
```

### Filter by date range (one week)

```
$filter=date ge 2026-02-16 and date le 2026-02-22
```

### Filter by project

```
$filter=jobNumber eq 'J10000'
```

### Filter by status

```
$filter=status eq 'Open'
```

### Get all entries for an employee in a specific week

```
$filter=employeeNumber eq 'R0010' and date ge 2026-02-16 and date le 2026-02-22
```

### Get entries with hours > 0

```
$filter=quantity gt 0
```

### Combine: employee + week + project

```
$filter=employeeNumber eq 'R0010' and date ge 2026-02-16 and date le 2026-02-22 and jobNumber eq 'J10000'
```

### Select specific fields (for performance)

```
$select=id,employeeNumber,jobNumber,date,quantity,status
```

---

## Troubleshooting

| Problem | Error Message | Cause | Fix |
|---------|--------------|-------|-----|
| Employee not found | *"The Employee does not exist. Identification fields and values: No.=''"* | Used `employeeId` (GUID) instead of `employeeNumber` | Use `employeeNumber` (e.g., `"R0010"`) in the POST body, NOT `employeeId` |
| Employee not found | *"The Employee does not exist. Identification fields and values: No.='XYZ'"* | The employee number doesn't exist in BC | Verify with `GET .../employees?$filter=number eq 'XYZ'` |
| Date has no Time Sheet | *"There is no Time Sheet for this Resource and Date"* or similar | No Time Sheet was created for that week | The BC admin must run "Create Time Sheets" batch job in BC for that date range |
| Can't edit entry | *"The record is not open"* or 400 error | Trying to PATCH an entry that's Submitted/Approved | You can only edit entries with `status` = `Open` |
| HTML response instead of JSON | HTML page returned | Missing `Accept: application/json` header | Add `Accept: application/json` to all GET requests |
| 404 on POST | 404 Not Found | POSTing to `/employees({id})/timeRegistrationEntries` | POST to the **top-level** `/timeRegistrationEntries` endpoint instead |
| Duplicate entry | 400 or silent duplicate | Entry already exists for that employee + date + job | GET first to check, then either skip or PATCH the existing entry's `quantity` |
| Wrong hours created | Multiple entries for same day | Created one entry per hour instead of one per day | Create ONE entry per date with total hours in `quantity` |

---

## Important Notes

- **Time Sheets are NOT created via the API** — they are pre-created in BC via the "Create Time Sheets" batch job. The `timeRegistrationEntries` endpoint only adds LINES to existing Time Sheets.
- **One entry = one line in an existing Time Sheet.** POST one entry per date. All entries for dates within the same week go into the same Time Sheet automatically.
- **Never create multiple entries for the same employee + same date + same job** — update the existing entry's `quantity` instead with PATCH.
- Use `jobNumber` (NOT `jobNo`) — this is the correct API field name. `jobTaskNumber` is optional.
- **`jobTaskNumber` is OPTIONAL** — you can log time against a project with just `jobNumber`. Do NOT include `jobTaskNumber` unless the user specifically needs task-level tracking.
- Entries with `status` other than `Open` cannot be edited or deleted.
- Status transitions (Submit, Approve, Reject) happen in BC's Time Sheet page — NOT via the API.
- If both `jobNumber` and `absence` are empty, the entry is a general **Resource** type entry.
- `lastModfiedDateTime` has a typo (missing 'i') — this is Microsoft's actual field name, use it as-is.
- The `employeeNumber` field corresponds to the **Resource No.** column shown on the Time Sheet page in BC.
- **Fields NOT available in the API:** Description, Chargeable, and Work Code are visible on the BC Time Sheet page but are NOT exposed in the `timeRegistrationEntries` API. You cannot read or write these fields via the standard v2.0 API.
- **The Type field is auto-determined:** You don't set it — BC infers it from `jobNumber` (→ Job type) or `absence` (→ Absence type) or neither (→ Resource type).
- **`jobTaskNumber` is ADVANCED/OPTIONAL** — it requires OData web service setup in BC. See `resources/projects/job-tasks.md`. Most apps should NOT use it.

---

## Advanced: Job Task Numbers (Optional)

> **Skip this section unless the user specifically asks for task-level tracking.** Most time registration apps only need `employeeNumber`, `date`, `quantity`, and `jobNumber`.

`jobTaskNumber` lets you assign a time entry to a specific task within a project. However:

1. **Job Tasks are NOT in the standard v2.0 API** — you must query them via OData web services
2. **Requires BC setup** — an admin must publish Page 1004 as web service `JobTasks` in BC
3. **OData returns different field names** — `Job_Task_No` (not `jobTaskNumber`)
4. **Only `Posting` type tasks accept time entries**

If you need it, see `resources/projects/job-tasks.md` for full setup and usage.

### Example with job task number

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/timeRegistrationEntries
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "employeeNumber": "R0010",
  "date": "2026-02-17",
  "quantity": 8.0,
  "jobNumber": "J10000",
  "jobTaskNumber": "JT1000"
}
```

> Only add `jobTaskNumber` if you have a valid task number from the OData `JobTasks` endpoint.
