# Automation API — Companies

The Automation API companies endpoint allows you to list and manage companies within a Business Central environment. This is different from the standard API companies endpoint — it provides additional management capabilities.

---

## Base Endpoint

```
{{BC_AUTOMATION_URL}}/companies
```

---

## Operations

### 1. List All Companies

```http
GET {{BC_AUTOMATION_URL}}/companies
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "value": [
    {
      "id": "a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa",
      "name": "CRONUS USA, Inc.",
      "displayName": "CRONUS USA, Inc.",
      "evaluationCompany": false,
      "businessProfileId": ""
    },
    {
      "id": "b2b2b2b2-2222-2222-2222-bbbbbbbbbbbb",
      "name": "My Company",
      "displayName": "My Company",
      "evaluationCompany": false,
      "businessProfileId": ""
    }
  ]
}
```

---

### 2. Get a Single Company

```http
GET {{BC_AUTOMATION_URL}}/companies(a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa)
Authorization: Bearer {access_token}
```

---

### 3. Create a Company

```http
POST {{BC_AUTOMATION_URL}}/companies
Authorization: Bearer {access_token}
Content-Type: application/json
```

```json
{
  "name": "New Subsidiary",
  "displayName": "New Subsidiary",
  "evaluationCompany": false
}
```

> **Note:** Company creation is an asynchronous operation. The company may take several minutes to be fully initialized.

---

### 4. Delete a Company

```http
DELETE {{BC_AUTOMATION_URL}}/companies(b2b2b2b2-2222-2222-2222-bbbbbbbbbbbb)
Authorization: Bearer {access_token}
```

**Response:** `204 No Content`

> **Warning:** Deleting a company permanently removes all its data. This action cannot be undone.

---

## Fields

| Field                  | Type     | Writable | Description                                    |
|------------------------|----------|----------|------------------------------------------------|
| `id`                   | GUID     | No       | Unique identifier                              |
| `name`                 | string   | Yes      | Company name (system name)                     |
| `displayName`          | string   | Yes      | Display name                                   |
| `evaluationCompany`    | boolean  | Yes      | Whether this is an evaluation/demo company     |
| `businessProfileId`    | string   | No       | Business profile identifier                    |

---

## Common Errors

| Status | Error                  | Cause & Resolution                                                                 |
|--------|------------------------|------------------------------------------------------------------------------------|
| `401`  | Unauthorized           | Access token invalid or missing D365 AUTOMATION permission.                        |
| `403`  | Forbidden              | App registration does not have Automation API permissions.                          |
| `404`  | Not Found              | Company name does not match any company.                                           |
