# Projects (Jobs)

> **⚠️ WARNING: The `/projects` endpoint may NOT exist in your BC environment.**
>
> The `projects` entity was added to the BC standard API in **Business Central 2023 release wave 1 (v22.0)**. If your environment runs an older version, calling `/projects` will return **404 Not Found**.
>
> **How to check:** Call `GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/projects` — if you get a 404, your version does not have this endpoint. Use the **OData web service workaround** documented below instead.

---

## Option A: Standard API Endpoint (v22.0+)

If your BC environment is version 22.0 or later, the `projects` endpoint is available in the standard API.

### Base Endpoint

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/projects
```

### List All Projects

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
    }
  ]
}
```

### Create a Project

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/projects
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "number": "J30000",
  "displayName": "New Client Onboarding"
}
```

### Update a Project

```http
PATCH {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/projects({id})
Authorization: Bearer {access_token}
Content-Type: application/json
If-Match: {etag}

{
  "displayName": "New Client Onboarding — Phase 2"
}
```

### Delete a Project

```http
DELETE {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/projects({id})
Authorization: Bearer {access_token}
If-Match: {etag}
```

### Field Definitions (Standard API)

| Field                    | Type     | Editable | Description                      |
|--------------------------|----------|----------|----------------------------------|
| `id`                     | GUID     | No       | Unique identifier                |
| `number`                 | string   | Yes      | Project/job number               |
| `displayName`            | string   | Yes      | Project display name             |
| `lastModifiedDateTime`   | datetime | No       | Last modified timestamp          |

---

## Option B: OData Web Service (Works on ALL BC versions)

If `/projects` returns 404, use OData v4 web services instead. This works on every BC version.

### Step 1 — Expose the Job List page as a web service in BC

1. Open Business Central web client
2. Go to **Web Services** (search in the top bar)
3. Click **+ New**
4. Set:
   - **Object Type:** `Page`
   - **Object ID:** `167` (Job List page)
   - **Service Name:** `Jobs`
   - **Published:** ✅ checked
5. Click **OK** to save

### Step 2 — Call the OData endpoint

The OData web service URL uses a different base path than the standard API:

```
https://api.businesscentral.dynamics.com/v2.0/{BC_TENANT_ID}/{BC_ENVIRONMENT}/ODataV4/Company('{BC_COMPANY_NAME}')/Jobs
```

> **Note the differences from the standard API:**
> - Path uses `/ODataV4/` instead of `/api/v2.0/`
> - Company filter uses `Company('Name')` instead of `companies(name='Name')`
> - The service name `Jobs` matches what you entered in Step 1

### List All Jobs via OData

```http
GET https://api.businesscentral.dynamics.com/v2.0/{{BC_TENANT_ID}}/{{BC_ENVIRONMENT}}/ODataV4/Company('{{BC_COMPANY_NAME}}')/Jobs
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "value": [
    {
      "No": "J10000",
      "Description": "Office Renovation Project",
      "Bill_to_Customer_No": "10000",
      "Status": "Planning",
      "Person_Responsible": "R0010",
      "Starting_Date": "2025-01-15",
      "Ending_Date": "2025-12-31",
      "Blocked": "  "
    }
  ]
}
```

> **Note:** OData web service field names use the BC field captions (e.g., `No`, `Description`, `Bill_to_Customer_No`) — NOT the API field names. Spaces in field names are replaced with `_`.

### Filter Jobs via OData

```
$filter=Status eq 'Planning'
$filter=No eq 'J10000'
$filter=contains(Description, 'Renovation')
$filter=Person_Responsible eq 'R0010'
```

### Select specific fields

```
$select=No,Description,Status,Starting_Date,Ending_Date
```

---

## Option C: Get Job Numbers from Time Registration Entries

If you only need to know which projects exist (and don't need full project details), you can extract unique job numbers from `timeRegistrationEntries` — this endpoint is always available:

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/timeRegistrationEntries?$filter=jobNumber ne ''&$select=jobNumber,jobTaskNumber&$orderby=jobNumber
Authorization: Bearer {access_token}
Accept: application/json
```

This returns all time entries that reference a project, giving you the `jobNumber` and `jobTaskNumber` values.

---

## Recommended Approach for AI Tools

When building a feature that needs job/project data:

1. **First try** the standard API: `GET .../companies(name='...')/projects`
2. **If that returns 404**, fall back to OData: `GET .../ODataV4/Company('...')/Jobs` 
3. **If you only need job numbers** (e.g., for a dropdown in a time entry form), use `timeRegistrationEntries` with `$select=jobNumber,jobTaskNumber`

```typescript
async function getProjects(): Promise<any[]> {
  const token = await getToken();
  
  // Try standard API first
  const stdResp = await fetch(
    `${BASE_URL}/companies(name='${BC_COMPANY_NAME}')/projects`,
    { headers: { 'Authorization': `Bearer ${token}`, 'Accept': 'application/json' } }
  );
  
  if (stdResp.ok) {
    const data = await stdResp.json();
    return data.value;
  }
  
  // Fall back to OData web service
  const odataUrl = `https://api.businesscentral.dynamics.com/v2.0/${BC_TENANT_ID}/${BC_ENVIRONMENT}/ODataV4/Company('${BC_COMPANY_NAME}')/Jobs`;
  const odataResp = await fetch(odataUrl, {
    headers: { 'Authorization': `Bearer ${token}`, 'Accept': 'application/json' }
  });
  
  if (odataResp.ok) {
    const data = await odataResp.json();
    // Map OData field names to standard format
    return data.value.map((j: any) => ({
      number: j.No,
      displayName: j.Description,
      status: j.Status,
      startingDate: j.Starting_Date,
      endingDate: j.Ending_Date,
    }));
  }
  
  throw new Error('Projects/Jobs endpoint not available. Expose Page 167 (Job List) as web service "Jobs" in BC.');
}
```

---

## Common Errors

| Status | Error                  | Cause & Resolution                                                                 |
|--------|------------------------|------------------------------------------------------------------------------------|
| `401`  | Unauthorized           | Access token is missing, expired, or invalid. Request a new token.                 |
| `404`  | Not Found — `/projects`| Your BC version is older than v22.0 and does not have the `projects` endpoint. Use the OData web service approach (Option B) instead. |
| `404`  | Not Found — OData `/Jobs` | The Job List page has not been published as a web service in BC. Follow Option B Step 1 to expose it. |
| `409`  | Conflict               | ETag mismatch — the record was modified since your last GET. Fetch and retry.      |

---

## Important Notes

- The standard API entity name is `projects` (NOT `jobs`) — but it was added in BC v22.0 and may not exist on older environments
- There are NO `jobJournals` or `jobJournalLines` endpoints in any version — use `timeRegistrationEntries` to log hours
- Use `jobNumber` in `timeRegistrationEntries` to reference a project by its number
- The `projects` endpoint (when available) has very few fields — most project detail is managed in the BC web client
- OData web service field names use underscores instead of spaces (e.g., `Bill_to_Customer_No`) and are NOT camelCase

---

## Related Resources

| Resource | File | Description |
|----------|------|-------------|
| Job Tasks | `resources/projects/job-tasks.md` | Task breakdown within a project (OData only — requires web service setup) |
| Time Registration | `resources/time-tracking/time-registration-entries.md` | Log hours against projects using `jobNumber` and `jobTaskNumber` |
