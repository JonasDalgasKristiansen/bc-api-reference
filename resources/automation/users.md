# Automation API — Users

The Automation API users endpoint allows you to manage users within a Business Central company, including their permissions, user groups, and access. You can also invite new users using a bound action.

---

## Base Endpoint

```
{{BC_AUTOMATION_URL}}/companies(name='{{BC_COMPANY_NAME}}')/users
```

---

## Operations

### 1. List All Users

```http
GET {{BC_AUTOMATION_URL}}/companies(name='{{BC_COMPANY_NAME}}')/users
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "value": [
    {
      "@odata.etag": "W/\"JzE5O1UxOyc=\"",
      "userSecurityId": "a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa",
      "userName": "ADMIN",
      "displayName": "Admin User",
      "state": "Enabled",
      "expiryDate": "0001-01-01T00:00:00Z",
      "contactEmail": "admin@contoso.com"
    },
    {
      "@odata.etag": "W/\"JzIwO1UyOyc=\"",
      "userSecurityId": "b2b2b2b2-2222-2222-2222-bbbbbbbbbbbb",
      "userName": "JDOE",
      "displayName": "Jane Doe",
      "state": "Enabled",
      "expiryDate": "0001-01-01T00:00:00Z",
      "contactEmail": "jane.doe@contoso.com"
    },
    {
      "@odata.etag": "W/\"JzIxO1UzOyc=\"",
      "userSecurityId": "c3c3c3c3-3333-3333-3333-cccccccccccc",
      "userName": "API-APP",
      "displayName": "API Application",
      "state": "Enabled",
      "expiryDate": "0001-01-01T00:00:00Z",
      "contactEmail": ""
    }
  ]
}
```

---

### 2. Get a Single User

```http
GET {{BC_AUTOMATION_URL}}/companies(name='{{BC_COMPANY_NAME}}')/users(a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa)
Authorization: Bearer {access_token}
```

---

### 3. Update a User

```http
PATCH {{BC_AUTOMATION_URL}}/companies(name='{{BC_COMPANY_NAME}}')/users(b2b2b2b2-2222-2222-2222-bbbbbbbbbbbb)
Authorization: Bearer {access_token}
Content-Type: application/json
If-Match: W/"JzIwO1UyOyc="
```

```json
{
  "state": "Disabled"
}
```

---

## User Fields

| Field              | Type     | Writable | Description                                    |
|--------------------|----------|----------|------------------------------------------------|
| `userSecurityId`   | GUID     | No       | Unique user identifier                         |
| `userName`         | string   | Yes      | Username                                       |
| `displayName`      | string   | Yes      | Display name                                   |
| `state`            | string   | Yes      | `Enabled` or `Disabled`                        |
| `expiryDate`       | datetime | Yes      | Account expiry date                            |
| `contactEmail`     | string   | Yes      | Contact email                                  |

---

## User Permissions (Sub-Resource)

### List User Permissions

```http
GET {{BC_AUTOMATION_URL}}/companies(name='{{BC_COMPANY_NAME}}')/users(a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa)/userPermissions
Authorization: Bearer {access_token}
```

**Response:**

```json
{
  "value": [
    {
      "id": "d4d4d4d4-4444-4444-4444-dddddddddddd",
      "userSecurityId": "a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa",
      "roleId": "SUPER",
      "displayName": "SUPER",
      "company": "CRONUS USA, Inc.",
      "appId": "00000000-0000-0000-0000-000000000000",
      "extensionName": "",
      "scope": "System"
    },
    {
      "id": "e5e5e5e5-5555-5555-5555-eeeeeeeeeeee",
      "userSecurityId": "a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa",
      "roleId": "D365 BUS FULL ACCESS",
      "displayName": "D365 Bus Full Access",
      "company": "",
      "appId": "00000000-0000-0000-0000-000000000000",
      "extensionName": "",
      "scope": "System"
    }
  ]
}
```

### Add a Permission Set to a User

```http
POST {{BC_AUTOMATION_URL}}/companies(name='{{BC_COMPANY_NAME}}')/users(b2b2b2b2-2222-2222-2222-bbbbbbbbbbbb)/userPermissions
Authorization: Bearer {access_token}
Content-Type: application/json
```

```json
{
  "roleId": "D365 AUTOMATION",
  "company": "",
  "scope": "System",
  "appId": "00000000-0000-0000-0000-000000000000"
}
```

### Remove a Permission Set

```http
DELETE {{BC_AUTOMATION_URL}}/companies(name='{{BC_COMPANY_NAME}}')/users(b2b2b2b2-2222-2222-2222-bbbbbbbbbbbb)/userPermissions(d4d4d4d4-4444-4444-4444-dddddddddddd)
Authorization: Bearer {access_token}
```

---

## User Permission Fields

| Field              | Type     | Writable | Description                                    |
|--------------------|----------|----------|------------------------------------------------|
| `id`               | GUID     | No       | Unique identifier                              |
| `userSecurityId`   | GUID     | No       | Parent user ID                                 |
| `roleId`           | string   | Yes      | Permission set code                            |
| `displayName`      | string   | No       | Display name of the permission set             |
| `company`          | string   | Yes      | Company scope (empty = all companies)          |
| `appId`            | GUID     | Yes      | Extension app ID (zero GUID = system)          |
| `extensionName`    | string   | No       | Extension name (read-only)                     |
| `scope`            | string   | Yes      | `System` or `Tenant`                           |

---

## User Group Members (Sub-Resource)

### List User Group Memberships

```http
GET {{BC_AUTOMATION_URL}}/companies(name='{{BC_COMPANY_NAME}}')/users(a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa)/userGroupMembers
Authorization: Bearer {access_token}
```

**Response:**

```json
{
  "value": [
    {
      "id": "f6f6f6f6-6666-6666-6666-ffffffffffff",
      "userSecurityId": "a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa",
      "code": "D365 BUS PREMIUM",
      "displayName": "Dynamics 365 Business Premium",
      "company": "CRONUS USA, Inc."
    }
  ]
}
```

### Add User to a User Group

```http
POST {{BC_AUTOMATION_URL}}/companies(name='{{BC_COMPANY_NAME}}')/users(b2b2b2b2-2222-2222-2222-bbbbbbbbbbbb)/userGroupMembers
Authorization: Bearer {access_token}
Content-Type: application/json
```

```json
{
  "code": "D365 BUS PREMIUM",
  "company": "CRONUS USA, Inc."
}
```

---

## Bound Action: Invite User

> **Note:** User invitation is available for Azure AD-linked environments.

---

## Common Permission Sets

| Role ID                    | Description                                        |
|----------------------------|----------------------------------------------------|
| `SUPER`                    | Full access to all objects                         |
| `D365 BUS FULL ACCESS`    | Full business access                               |
| `D365 BUS PREMIUM`        | Business Premium license                           |
| `D365 BASIC`              | Basic access                                       |
| `D365 AUTOMATION`         | Automation API access                              |
| `D365 READ`               | Read-only access                                   |
| `SECURITY`                | Security administration                            |

---

## Filter Examples

```http
# Enabled users only
GET {{BC_AUTOMATION_URL}}/companies(name='{{BC_COMPANY_NAME}}')/users?$filter=state eq 'Enabled'

# Find a specific user by name
GET {{BC_AUTOMATION_URL}}/companies(name='{{BC_COMPANY_NAME}}')/users?$filter=userName eq 'JDOE'
```

---

## Common Errors

| Status | Error                  | Cause & Resolution                                                                 |
|--------|------------------------|------------------------------------------------------------------------------------|
| `401`  | Unauthorized           | Access token invalid or missing D365 AUTOMATION permission.                        |
| `403`  | Forbidden              | Insufficient permissions to manage users.                                          |
| `404`  | Not Found              | User ID does not exist.                                                            |
| `409`  | Conflict               | ETag mismatch. Re-fetch and retry.                                                 |
