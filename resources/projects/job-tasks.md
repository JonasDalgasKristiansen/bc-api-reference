# Job Tasks (OData Web Service)

> **⚠️ Job Tasks are NOT available in the standard BC v2.0 API.** You must use an OData web service to access them.

Job Tasks are the individual tasks/activities within a project (job) in Business Central. Each project can have multiple tasks, forming a hierarchical structure.

---

## Setup Required in Business Central

Before you can query Job Tasks via OData, the web service must be published in BC:

1. Open **Business Central** in your browser
2. Search for **Web Services** and open the page
3. Click **+ New**
4. Fill in:
   - **Object Type:** `Page`
   - **Object ID:** `1004`
   - **Object Name:** *(auto-fills to "Job Task Lines")*
   - **Service Name:** `JobTasks`
   - **Published:** ✅ (checked)
5. Close the page

> If your BC admin has already published this web service, you can skip this step. You can check by looking for `JobTasks` in the Web Services list.

---

## OData Endpoint

```
https://api.businesscentral.dynamics.com/v2.0/{{BC_TENANT_ID}}/{{BC_ENVIRONMENT}}/ODataV4/Company('{{BC_COMPANY_NAME}}')/JobTasks
```

> **CRITICAL:** Always include the `Accept: application/json` header on OData requests. Without it, BC returns an HTML page instead of JSON data.

---

## Authentication

Same OAuth2 token as the standard API:

```http
GET https://api.businesscentral.dynamics.com/v2.0/{BC_TENANT_ID}/{BC_ENVIRONMENT}/ODataV4/Company('{BC_COMPANY_NAME}')/JobTasks
Authorization: Bearer {access_token}
Accept: application/json
```

---

## Operations

### 1. List All Job Tasks

```http
GET https://api.businesscentral.dynamics.com/v2.0/{BC_TENANT_ID}/{BC_ENVIRONMENT}/ODataV4/Company('{BC_COMPANY_NAME}')/JobTasks
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "value": [
    {
      "Job_No": "J10000",
      "Job_Task_No": "JT1000",
      "Description": "Project Planning",
      "Job_Task_Type": "Posting",
      "Totaling": "",
      "Job_Posting_Group": "SETUP",
      "WIP_Total": " ",
      "WIP_Method": ""
    },
    {
      "Job_No": "J10000",
      "Job_Task_No": "JT2000",
      "Description": "Development Phase",
      "Job_Task_Type": "Posting",
      "Totaling": "",
      "Job_Posting_Group": "SETUP",
      "WIP_Total": " ",
      "WIP_Method": ""
    }
  ]
}
```

### 2. Get Job Tasks for a Specific Project

```http
GET https://api.businesscentral.dynamics.com/v2.0/{BC_TENANT_ID}/{BC_ENVIRONMENT}/ODataV4/Company('{BC_COMPANY_NAME}')/JobTasks?$filter=Job_No eq 'J10000'
Authorization: Bearer {access_token}
Accept: application/json
```

### 3. Get Only Postable Tasks (for time registration dropdowns)

Only tasks with `Job_Task_Type` = `Posting` can have time entries posted to them:

```http
GET https://api.businesscentral.dynamics.com/v2.0/{BC_TENANT_ID}/{BC_ENVIRONMENT}/ODataV4/Company('{BC_COMPANY_NAME}')/JobTasks?$filter=Job_No eq 'J10000' and Job_Task_Type eq 'Posting'
Authorization: Bearer {access_token}
Accept: application/json
```

---

## Field Definitions

> **OData field names use underscores**, not camelCase. This is different from the standard API.

| OData Field Name       | Type   | Description |
|------------------------|--------|-------------|
| `Job_No`               | string | The project/job number (e.g., `"J10000"`) |
| `Job_Task_No`          | string | The task number within the project (e.g., `"JT1000"`) |
| `Description`          | string | Description of the task |
| `Job_Task_Type`        | string | `Posting`, `Heading`, `Total`, `Begin-Total`, `End-Total` |
| `Totaling`             | string | Range for totaling (e.g., `"JT1000..JT2000"`) |
| `Job_Posting_Group`    | string | Posting group code |
| `WIP_Total`            | string | Work-in-progress total setting |
| `WIP_Method`           | string | WIP calculation method |

### Job Task Types

| Type | Can Post Time To? | Description |
|------|-------------------|-------------|
| `Posting` | ✅ Yes | Regular task — time entries and costs can be posted here |
| `Heading` | ❌ No | Header/category — used for grouping, no posting allowed |
| `Total` | ❌ No | Totals a range of tasks |
| `Begin-Total` | ❌ No | Marks the start of a total range |
| `End-Total` | ❌ No | Marks the end of a total range |

> **For time registration apps:** Only show tasks where `Job_Task_Type` = `Posting` in your dropdown. Other types are structural and cannot receive time entries.

---

## Mapping to Time Registration API Fields

When creating a time registration entry, use these values from the Job Tasks response:

| Job Tasks OData Field | Time Registration API Field | Example |
|-----------------------|---------------------------|---------|
| `Job_No` | `jobNumber` | `"J10000"` |
| `Job_Task_No` | `jobTaskNumber` | `"JT1000"` |

> **Note the naming difference:** OData uses `Job_No` / `Job_Task_No` (underscores). The standard API uses `jobNumber` / `jobTaskNumber` (camelCase). Make sure to map correctly.

---

## OData Filter Examples

### Filter by project number

```
$filter=Job_No eq 'J10000'
```

### Filter by task type (only postable tasks)

```
$filter=Job_Task_Type eq 'Posting'
```

### Combine: project + postable tasks only

```
$filter=Job_No eq 'J10000' and Job_Task_Type eq 'Posting'
```

### Select specific fields

```
$select=Job_No,Job_Task_No,Description,Job_Task_Type
```

---

## Troubleshooting

| Problem | Cause | Fix |
|---------|-------|-----|
| HTML response instead of JSON | Missing `Accept: application/json` header | Add `Accept: application/json` to the request |
| 404 Not Found | Web service not published in BC | Go to Web Services in BC and publish Page 1004 as `JobTasks` |
| Empty result | No tasks exist for that project | Verify the project exists and has tasks in BC |
| Time entry fails with task number | Used `Job_Task_No` format instead of API format | Use the value from `Job_Task_No` but pass it as `jobTaskNumber` in the time entry POST body |
| Can't post time to a task | Task is not a `Posting` type | Filter for `Job_Task_Type eq 'Posting'` — only these accept time entries |

---

## Important Notes

- **Job Tasks require OData** — they are NOT available as a standard v2.0 API entity
- **Always send `Accept: application/json`** header or BC returns HTML
- **OData field names use underscores** (`Job_No`, `Job_Task_No`) — different from the standard API's camelCase (`jobNumber`, `jobTaskNumber`)
- **Only `Posting` type tasks can receive time entries** — filter your task dropdowns accordingly
- **The same OAuth2 token** used for the standard API works for OData endpoints
- See `resources/projects/projects.md` for project/job data
- See `resources/time-tracking/time-registration-entries.md` for creating time entries with project + task
