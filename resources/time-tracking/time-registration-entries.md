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
| **Description**           | `Description` *(OData)* | ✅ Via OData     | Free text. Not in standard API — use OData `TimeSheetLines` PATCH. See [OData Hybrid section](#setting-description-chargeable-and-work-type-code-odata-hybrid-approach) below. |
| **Project No.**           | `jobNumber`          | ✅ Yes           | Sets the project/job. Also auto-sets Type to `Job` |
| **Project Task No.**      | `jobTaskNumber`      | ✅ Yes (optional) | **OPTIONAL.** Task within the project. Only use if you specifically need task-level tracking. Most apps only need `jobNumber`. |
| **Cause of Absence Code** | `absence`            | ✅ Yes           | Sets the absence reason. Also auto-sets Type to `Absence` |
| **Chargeable**            | `Chargeable` *(OData)* | ✅ Via OData     | Boolean. Not in standard API — use OData `TimeSheetLines` PATCH. See [OData Hybrid section](#setting-description-chargeable-and-work-type-code-odata-hybrid-approach) below. |
| **Work Type Code**        | `Work_Type_Code` *(OData)* | ✅ Via OData  | String. Not in standard API — use OData `TimeSheetLines` PATCH. See [OData Hybrid section](#setting-description-chargeable-and-work-type-code-odata-hybrid-approach) below. |
| **Mon–Sun hours**         | `date` + `quantity`  | ✅ Yes           | Each day is a separate POST with a `date` and `quantity` (hours) |
| **Total**                 | *(calculated)*       | ❌ Read-only     | Sum of all entries for the week — calculated by BC |

> **Description, Chargeable, and Work Type Code** are not in the standard `timeRegistrationEntries` API, but **CAN be set** via a hybrid approach using an OData web service. See the [OData Hybrid section](#setting-description-chargeable-and-work-type-code-odata-hybrid-approach) below for full setup and code examples. A BC administrator must publish the web service first (one-time, ~30 seconds).

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
- ❌ Do NOT try to set `description`, `chargeable`, or `workTypeCode` via the standard API — use the [OData hybrid approach](#setting-description-chargeable-and-work-type-code-odata-hybrid-approach) described below
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
- **Description, Chargeable, and Work Type Code** are not in the standard `timeRegistrationEntries` API but **CAN be set** via the [OData hybrid approach](#setting-description-chargeable-and-work-type-code-odata-hybrid-approach) — see the section below.
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

---

## Setting Description, Chargeable, and Work Type Code (OData Hybrid Approach)

> **The standard `timeRegistrationEntries` API does not expose `Description`, `Chargeable`, or `Work Type Code`.** To set these fields — especially Description — you must use an OData web service for Time Sheet Lines as a secondary step after creating the entry via the standard API.

This is the same pattern used for Job Tasks (see `resources/projects/job-tasks.md`).

### Why This Matters

- **Description** is the most important missing field — it lets employees describe what they worked on (e.g., `"Sprint planning meeting"`, `"Bug fix for login page"`)
- **Chargeable** (boolean) indicates whether the time is billable
- **Work Type Code** classifies the type of work (e.g., `"DEV"`, `"MEETING"`, `"TRAVEL"`)

Without the OData step, these fields remain blank in BC's Time Sheet.

### BC Admin Setup (One-Time)

A BC administrator must publish Page 951 as an OData web service. This takes ~30 seconds:

1. Open **Business Central**
2. Search for **Web Services** and open it
3. Click **+ New**
4. Fill in:
   - **Object Type:** `Page`
   - **Object ID:** `951`
   - **Service Name:** `TimeSheetLines`
   - **Published:** ✅ (checked)
5. Close the page

> **Without this step, the OData endpoint will return 404.** If you get a 404 on the OData URL below, the web service has not been published yet.

### OData Endpoint

```
https://api.businesscentral.dynamics.com/v2.0/{BC_TENANT_ID}/{BC_ENVIRONMENT}/ODataV4/Company('{BC_COMPANY_NAME}')/TimeSheetLines
```

Uses the same OAuth2 Bearer token as the standard API. **You MUST include `Accept: application/json`** — without it, BC returns HTML instead of JSON.

### Hybrid Flow (Recommended)

Use the standard API to create entries (simple employee handling), then enrich them via OData:

```
Step 1: POST /timeRegistrationEntries          → Create entry (employeeNumber, date, quantity, jobNumber)
Step 2: Read back the created entry             → Get the id and lineNumber from the response
Step 3: GET OData TimeSheetLines                → Find the matching line using employeeNumber + date + lineNumber
Step 4: PATCH OData TimeSheetLine               → Set Description, Chargeable, Work_Type_Code
```

### Step-by-Step with HTTP Examples

**Step 1 — Create the time entry via standard API:**

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

Response includes `lineNumber` (e.g., `10000`) — save this value.

**Step 2 — Find the matching OData Time Sheet Line:**

```http
GET https://api.businesscentral.dynamics.com/v2.0/{BC_TENANT_ID}/{BC_ENVIRONMENT}/ODataV4/Company('{BC_COMPANY_NAME}')/TimeSheetLines?$filter=Line_No eq 10000 and Job_No eq 'J10000'&$select=Time_Sheet_No,Line_No,Description,Chargeable,Work_Type_Code,Job_No,Job_Task_No,Type,Status
Authorization: Bearer {access_token}
Accept: application/json
```

> **Correlation tip:** Filter by `Line_No` (from step 1's `lineNumber`) and `Job_No` (from `jobNumber`). For more precision, also filter by date if available in the OData fields, or query the employee's Time Sheet for that week.
>
> **⚠️ Production warning:** The filter `Line_No eq X and Job_No eq 'Y'` alone could match lines from **other employees' Time Sheets** (line numbers are not globally unique). In production, you should also resolve the employee's `Time_Sheet_No` first (e.g., by filtering `TimeSheetLines` on the employee's Resource No. and the week's starting date) and include `Time_Sheet_No eq 'TSxxxxx'` in the filter to avoid cross-employee matches.

**Step 3 — PATCH the missing fields:**

```http
PATCH https://api.businesscentral.dynamics.com/v2.0/{BC_TENANT_ID}/{BC_ENVIRONMENT}/ODataV4/Company('{BC_COMPANY_NAME}')/TimeSheetLines(Time_Sheet_No='TS00042',Line_No=10000)
Authorization: Bearer {access_token}
Content-Type: application/json
If-Match: {etag}

{
  "Description": "Sprint planning meeting",
  "Chargeable": true,
  "Work_Type_Code": "DEV"
}
```

> You only need to include the fields you want to update. `Description` alone is the most common case.

### Complete Code Example (TypeScript)

```typescript
/**
 * Create a time entry with Description via the hybrid approach.
 * Step 1: POST via standard API (handles employee→resource mapping)
 * Step 2: PATCH via OData (sets Description, Chargeable, Work_Type_Code)
 */
async function createTimeEntryWithDescription(
  employeeNumber: string,
  date: string,
  quantity: number,
  jobNumber: string,
  description: string,
  chargeable?: boolean,
  workTypeCode?: string
) {
  const token = await getToken();

  // Step 1: Create entry via standard API
  const createResp = await fetch(
    `${BC_BASE_URL}/companies(name='${BC_COMPANY_NAME}')/timeRegistrationEntries`,
    {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${token}`,
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({ employeeNumber, date, quantity, jobNumber }),
    }
  );
  if (!createResp.ok) throw new Error(`Create failed: ${createResp.status}`);
  const entry = await createResp.json();
  const lineNumber = entry.lineNumber; // e.g., 10000

  // Step 2: Find the matching OData Time Sheet Line
  const odataBase = `https://api.businesscentral.dynamics.com/v2.0/${BC_TENANT_ID}/${BC_ENVIRONMENT}/ODataV4/Company('${BC_COMPANY_NAME}')`;
  const filter = `$filter=Line_No eq ${lineNumber} and Job_No eq '${jobNumber}'`;
  const select = `$select=Time_Sheet_No,Line_No,Description,Chargeable,Work_Type_Code`;

  const findResp = await fetch(
    `${odataBase}/TimeSheetLines?${filter}&${select}`,
    {
      headers: {
        'Authorization': `Bearer ${token}`,
        'Accept': 'application/json',
      },
    }
  );
  if (!findResp.ok) throw new Error(`OData GET failed: ${findResp.status}`);
  const findData = await findResp.json();
  const line = findData.value[0];
  if (!line) throw new Error('Could not find matching Time Sheet Line via OData');

  // Step 3: PATCH the missing fields
  const patchBody: Record<string, any> = { Description: description };
  if (chargeable !== undefined) patchBody.Chargeable = chargeable;
  if (workTypeCode) patchBody.Work_Type_Code = workTypeCode;

  const patchResp = await fetch(
    `${odataBase}/TimeSheetLines(Time_Sheet_No='${line.Time_Sheet_No}',Line_No=${line.Line_No})`,
    {
      method: 'PATCH',
      headers: {
        'Authorization': `Bearer ${token}`,
        'Content-Type': 'application/json',
        'If-Match': findData.value[0]['@odata.etag'],
        'Accept': 'application/json',
      },
      body: JSON.stringify(patchBody),
    }
  );
  if (!patchResp.ok) throw new Error(`OData PATCH failed: ${patchResp.status}`);

  return { ...entry, description, chargeable, workTypeCode };
}

// Usage:
await createTimeEntryWithDescription(
  "R0010",           // employeeNumber
  "2026-02-17",      // date
  8.0,               // quantity (hours)
  "J10000",          // jobNumber
  "Sprint planning", // description ← the key field
  true,              // chargeable (optional)
  "DEV"              // workTypeCode (optional)
);
```

### OData Field Names

OData uses underscores, not camelCase. Here is the mapping:

| BC Time Sheet Column | OData Field Name | Type | Editable | Notes |
|---|---|---|---|---|
| Time Sheet No. | `Time_Sheet_No` | string | No | Key — identifies the weekly Time Sheet |
| Line No. | `Line_No` | integer | No | Key — matches `lineNumber` from standard API |
| Type | `Type` | string | No | `Resource`, `Job`, `Absence` — auto-set by BC |
| Description | `Description` | string | **Yes** ✅ | Free text — **most important missing field** |
| Job No. | `Job_No` | string | Yes | Same as `jobNumber` in standard API |
| Job Task No. | `Job_Task_No` | string | Yes | Same as `jobTaskNumber` in standard API |
| Cause of Absence Code | `Cause_of_Absence_Code` | string | Yes | Same as `absence` in standard API |
| Chargeable | `Chargeable` | boolean | **Yes** ✅ | Whether the time is billable |
| Work Type Code | `Work_Type_Code` | string | **Yes** ✅ | Work classification code |
| Status | `Status` | string | No | Open, Submitted, Rejected, Approved |

### Troubleshooting — OData

| Problem | Cause | Fix |
|---|---|---|
| 404 on OData URL | Web service not published | BC admin must publish Page 951 as `TimeSheetLines` (see setup above) |
| HTML returned instead of JSON | Missing `Accept: application/json` header | Add `Accept: application/json` to every OData request |
| Can't PATCH Description | Time Sheet Line status is not Open | Only `Open` lines can be edited — same restriction as standard API |
| Empty result on GET | Wrong filter values | Verify `Time_Sheet_No` and `Line_No` match — use the `lineNumber` from the standard API response |
| `Work_Type_Code` rejected | Invalid code | Check valid Work Type Codes in BC: search for **Work Types** page |

### Important Notes — OData Hybrid

- **Always create entries via the standard `timeRegistrationEntries` API first**, then enrich via OData. The standard API handles employee→resource mapping automatically. Do NOT try to POST directly to the OData endpoint — it requires `Time_Sheet_No` and `Resource_No` which are harder to work with.
- **Description is the highest-value field** — it's what employees actually type to describe their work. If you only PATCH one field, make it Description.
- **The OData PATCH only works on Open entries** — once a Time Sheet is Submitted or Approved, the lines are read-only.
- **OData field names use underscores** (`Work_Type_Code`), not camelCase (`workTypeCode`). Do NOT mix these up.
- **Valid Work Type Codes** depend on BC setup — query the Work Types page in BC or ask the user to provide the available codes.
- **Valid Cause of Absence Codes** also depend on BC setup — query the Causes of Absence page in BC.
