# Automation API — Environments (Admin Center API)

The Admin Center API allows you to manage Business Central environments (sandbox and production). This uses a **completely different base URL** from both the standard API and the Automation API.

---

## Base URL

```
https://api.businesscentral.dynamics.com/admin/v2.28
```

---

## Authentication

Uses the same OAuth2 Client Credentials flow, but the app registration needs **admin API permissions**:

```http
POST https://login.microsoftonline.com/{{BC_TENANT_ID}}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

client_id={{BC_CLIENT_ID}}
&client_secret={{BC_CLIENT_SECRET}}
&scope=https://api.businesscentral.dynamics.com/.default
&grant_type=client_credentials
```

> **Note:** The app registration must be granted the **Admin Center API** permissions in the Azure portal.

---

## Operations

### 1. List All Environments

```http
GET https://api.businesscentral.dynamics.com/admin/v2.28/applications/businesscentral/environments
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "value": [
    {
      "friendlyName": "Production",
      "type": "Production",
      "name": "production",
      "countryCode": "US",
      "applicationFamily": "BusinessCentral",
      "aadTenantId": "{{BC_TENANT_ID}}",
      "applicationVersion": "24.0.12345.0",
      "status": "Active",
      "webClientLoginUrl": "https://businesscentral.dynamics.com/{{BC_TENANT_ID}}/production",
      "platformVersion": "24.0.12345.0"
    },
    {
      "friendlyName": "Sandbox",
      "type": "Sandbox",
      "name": "sandbox",
      "countryCode": "US",
      "applicationFamily": "BusinessCentral",
      "aadTenantId": "{{BC_TENANT_ID}}",
      "applicationVersion": "24.0.12345.0",
      "status": "Active",
      "webClientLoginUrl": "https://businesscentral.dynamics.com/{{BC_TENANT_ID}}/sandbox",
      "platformVersion": "24.0.12345.0"
    }
  ]
}
```

---

### 2. Get a Single Environment

```http
GET https://api.businesscentral.dynamics.com/admin/v2.28/applications/businesscentral/environments/{environmentName}
Authorization: Bearer {access_token}
```

Example:

```http
GET https://api.businesscentral.dynamics.com/admin/v2.28/applications/businesscentral/environments/production
```

---

### 3. Create an Environment

```http
PUT https://api.businesscentral.dynamics.com/admin/v2.28/applications/businesscentral/environments/{environmentName}
Authorization: Bearer {access_token}
Content-Type: application/json
```

**Create a new Sandbox:**

```json
{
  "environmentType": "Sandbox",
  "countryCode": "US",
  "applicationVersion": "24.0"
}
```

**Create a Sandbox from a copy of Production:**

```json
{
  "environmentType": "Sandbox",
  "countryCode": "US",
  "sourceEnvironmentName": "production"
}
```

**Response (201 Created or 202 Accepted):**

```json
{
  "friendlyName": "dev-test",
  "type": "Sandbox",
  "name": "dev-test",
  "countryCode": "US",
  "applicationFamily": "BusinessCentral",
  "aadTenantId": "{{BC_TENANT_ID}}",
  "status": "Preparing"
}
```

> **Note:** Environment creation is **asynchronous**. The status will be `Preparing` initially. Poll the environment endpoint until the status changes to `Active`.

---

### 4. Delete an Environment

```http
DELETE https://api.businesscentral.dynamics.com/admin/v2.28/applications/businesscentral/environments/{environmentName}
Authorization: Bearer {access_token}
```

**Response:** `202 Accepted` (async deletion)

> **Warning:** Deleting a production environment permanently destroys all data. Only sandbox environments should be deleted through the API.

---

## Environment Fields

| Field                  | Type     | Description                                                |
|------------------------|----------|------------------------------------------------------------|
| `friendlyName`         | string   | Human-readable environment name                            |
| `type`                 | string   | `Production` or `Sandbox`                                  |
| `name`                 | string   | Environment name (used in URLs)                            |
| `countryCode`          | string   | Country code (e.g., `US`, `GB`, `DK`)                     |
| `applicationFamily`    | string   | Always `BusinessCentral`                                   |
| `aadTenantId`          | GUID     | Azure AD tenant ID                                         |
| `applicationVersion`   | string   | BC application version                                     |
| `status`               | string   | `Active`, `Preparing`, `Removing`, `SoftDeleted`           |
| `webClientLoginUrl`    | string   | URL to the web client                                      |
| `platformVersion`      | string   | Platform version                                           |

---

## Environment Status Values

| Status          | Description                                         |
|-----------------|-----------------------------------------------------|
| `Active`        | Environment is running and accessible               |
| `Preparing`     | Environment is being created or updated             |
| `Removing`      | Environment is being deleted                        |
| `SoftDeleted`   | Environment has been deleted but may be recoverable |

---

## Available Application Versions

List the available application versions for creating new environments:

```http
GET https://api.businesscentral.dynamics.com/admin/v2.28/applications/businesscentral/environments/availableApplicationVersions
Authorization: Bearer {access_token}
```

---

## Environment Operations

### Restart an Environment

```http
POST https://api.businesscentral.dynamics.com/admin/v2.28/applications/businesscentral/environments/{environmentName}/restart
Authorization: Bearer {access_token}
```

### Get Available Updates

```http
GET https://api.businesscentral.dynamics.com/admin/v2.28/applications/businesscentral/environments/{environmentName}/availableUpdates
Authorization: Bearer {access_token}
```

### Schedule an Update

```http
PUT https://api.businesscentral.dynamics.com/admin/v2.28/applications/businesscentral/environments/{environmentName}/updateSettings
Authorization: Bearer {access_token}
Content-Type: application/json
```

```json
{
  "preferredStartDate": "2025-03-01",
  "preferredStartTime": "02:00",
  "preferredEndDate": "2025-03-01",
  "preferredEndTime": "06:00"
}
```

---

## Environment Quotas

Each tenant has limits on the number of environments:

| Environment Type | Typical Limit                    |
|------------------|----------------------------------|
| Production       | 1 per license (additional paid)  |
| Sandbox          | 3 per tenant (configurable)      |

---

## Common Workflow: Create a Sandbox for Testing

```
Step 1: Create sandbox from production copy
PUT .../environments/test-sandbox
{
  "environmentType": "Sandbox",
  "countryCode": "US",
  "sourceEnvironmentName": "production"
}

Step 2: Poll until active
GET .../environments/test-sandbox
→ Repeat until status = "Active" (may take 5-15 minutes)

Step 3: Use the sandbox
Base URL: https://api.businesscentral.dynamics.com/v2.0/{tenant}/test-sandbox/api/v2.0

Step 4: Delete when done
DELETE .../environments/test-sandbox
```

---

## Important Notes

- The Admin Center API uses a **different base URL** than the standard and automation APIs
- Environment creation and deletion are **asynchronous** — always poll for completion
- Production environment limits depend on your license
- Sandbox environments created from production copies include all data at the time of copy
- The API version (`v2.28`) may change — check Microsoft documentation for the latest

---

## Common Errors

| Status | Error                  | Cause & Resolution                                                                 |
|--------|------------------------|------------------------------------------------------------------------------------|
| `401`  | Unauthorized           | Access token invalid or missing Admin Center API permissions.                       |
| `403`  | Forbidden              | App registration does not have admin permissions.                                  |
| `404`  | Not Found              | Environment name does not exist.                                                   |
| `409`  | Conflict               | Environment with that name already exists, or quota exceeded.                      |
| `429`  | Too Many Requests      | Rate limit exceeded. Wait and retry with exponential backoff.                      |
