# Automation API — Extensions

The Automation API extensions endpoint allows you to manage installed extensions in a Business Central environment. You can list installed extensions, install new ones, uninstall existing ones, and upload custom extensions.

---

## Base Endpoint

```
{{BC_AUTOMATION_URL}}/companies({{BC_COMPANY_ID}})/extensions
```

---

## Operations

### 1. List Installed Extensions

```http
GET {{BC_AUTOMATION_URL}}/companies({{BC_COMPANY_ID}})/extensions
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "value": [
    {
      "packageId": "a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa",
      "id": "b2b2b2b2-2222-2222-2222-bbbbbbbbbbbb",
      "displayName": "Sales and Inventory Forecast",
      "publisher": "Microsoft",
      "versionMajor": 22,
      "versionMinor": 0,
      "versionBuild": 54321,
      "versionRevision": 0,
      "isInstalled": true,
      "publishedAs": "Global"
    },
    {
      "packageId": "c3c3c3c3-3333-3333-3333-cccccccccccc",
      "id": "d4d4d4d4-4444-4444-4444-dddddddddddd",
      "displayName": "AMC Banking 365 Fundamentals",
      "publisher": "AMC",
      "versionMajor": 22,
      "versionMinor": 0,
      "versionBuild": 12345,
      "versionRevision": 0,
      "isInstalled": true,
      "publishedAs": "Global"
    },
    {
      "packageId": "e5e5e5e5-5555-5555-5555-eeeeeeeeeeee",
      "id": "f6f6f6f6-6666-6666-6666-ffffffffffff",
      "displayName": "My Custom Extension",
      "publisher": "Contoso",
      "versionMajor": 1,
      "versionMinor": 0,
      "versionBuild": 0,
      "versionRevision": 0,
      "isInstalled": true,
      "publishedAs": "Dev"
    }
  ]
}
```

---

### 2. Get a Single Extension

```http
GET {{BC_AUTOMATION_URL}}/companies({{BC_COMPANY_ID}})/extensions(a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa)
Authorization: Bearer {access_token}
```

---

## Extension Fields

| Field              | Type     | Description                                                |
|--------------------|----------|------------------------------------------------------------|
| `packageId`        | GUID     | Package identifier                                         |
| `id`               | GUID     | Extension app ID                                           |
| `displayName`      | string   | Extension name                                             |
| `publisher`        | string   | Publisher name                                             |
| `versionMajor`     | integer  | Major version                                              |
| `versionMinor`     | integer  | Minor version                                              |
| `versionBuild`     | integer  | Build version                                              |
| `versionRevision`  | integer  | Revision version                                           |
| `isInstalled`      | boolean  | Whether the extension is currently installed               |
| `publishedAs`      | string   | `Global`, `Dev`, or `PTE` (Per-Tenant Extension)           |

---

## Bound Action: Install

Install an extension from AppSource or a previously uploaded package:

```http
POST {{BC_AUTOMATION_URL}}/companies({{BC_COMPANY_ID}})/extensions(a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa)/Microsoft.NAV.install
Authorization: Bearer {access_token}
```

**Response:** `204 No Content`

> **Note:** Installation is asynchronous. The extension may take several minutes to fully install. Check the extension's `isInstalled` property to verify completion.

---

## Bound Action: Uninstall

Uninstall an installed extension:

```http
POST {{BC_AUTOMATION_URL}}/companies({{BC_COMPANY_ID}})/extensions(a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa)/Microsoft.NAV.uninstall
Authorization: Bearer {access_token}
```

**Response:** `204 No Content`

> **Warning:** Uninstalling an extension may remove data associated with that extension.

---

## Bound Action: Upload

Upload a custom extension (.app file) to the environment:

```http
POST {{BC_AUTOMATION_URL}}/companies({{BC_COMPANY_ID}})/extensionUpload/Microsoft.NAV.upload
Authorization: Bearer {access_token}
Content-Type: application/octet-stream
```

Send the `.app` file as the request body (binary).

**Response:** `204 No Content`

After uploading, the extension appears in the extensions list with `isInstalled: false`. Use the install action to activate it.

---

## Extension Deployment Operations

### List Extension Deployment Status

Track the status of install/uninstall operations:

```http
GET {{BC_AUTOMATION_URL}}/companies({{BC_COMPANY_ID}})/extensionDeploymentStatus
Authorization: Bearer {access_token}
```

**Response:**

```json
{
  "value": [
    {
      "name": "My Custom Extension",
      "publisher": "Contoso",
      "operationType": "Install",
      "status": "Completed",
      "startedOn": "2025-02-17T10:00:00Z",
      "appVersion": "1.0.0.0"
    }
  ]
}
```

### Deployment Status Values

| Status          | Description                                  |
|-----------------|----------------------------------------------|
| `Unknown`       | Status not yet determined                    |
| `InProgress`    | Operation is running                         |
| `Completed`     | Operation finished successfully              |
| `Failed`        | Operation failed                             |
| `Cancelling`    | Operation is being canceled                  |
| `Cancelled`     | Operation was canceled                       |

---

## Filter Examples

```http
# Installed extensions only
GET {{BC_AUTOMATION_URL}}/companies({{BC_COMPANY_ID}})/extensions?$filter=isInstalled eq true

# Extensions by a specific publisher
GET {{BC_AUTOMATION_URL}}/companies({{BC_COMPANY_ID}})/extensions?$filter=publisher eq 'Microsoft'

# Per-tenant extensions
GET {{BC_AUTOMATION_URL}}/companies({{BC_COMPANY_ID}})/extensions?$filter=publishedAs eq 'PTE'
```

---

## Common Errors

| Status | Error                  | Cause & Resolution                                                                 |
|--------|------------------------|------------------------------------------------------------------------------------|
| `401`  | Unauthorized           | Access token invalid or missing D365 AUTOMATION permission.                        |
| `403`  | Forbidden              | Insufficient permissions to manage extensions.                                     |
| `404`  | Not Found              | Extension ID does not exist.                                                       |
| `409`  | Conflict               | Extension is already installed/uninstalled, or another operation is in progress.    |
