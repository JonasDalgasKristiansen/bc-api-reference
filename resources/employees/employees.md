# Employees

Employees represent the people working for your Business Central company. The employees endpoint supports full CRUD operations and provides sub-resources for default dimensions, pictures, and time registration entries.

---

## Base Endpoint

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/employees
```

---

## Operations

### 1. List All Employees

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/employees
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
      "number": "EMP001",
      "displayName": "Alice Johnson",
      "givenName": "Alice",
      "middleName": "",
      "surname": "Johnson",
      "jobTitle": "Senior Developer",
      "addressLine1": "500 Technology Drive",
      "addressLine2": "Suite 200",
      "city": "Seattle",
      "state": "WA",
      "country": "US",
      "postalCode": "98101",
      "phoneNumber": "+1 206-555-0100",
      "mobilePhone": "+1 206-555-0101",
      "email": "alice.johnson@company.com",
      "personalEmail": "alice@personal.com",
      "employmentDate": "2022-03-15",
      "terminationDate": "0001-01-01",
      "status": "Active",
      "birthDate": "1990-05-20",
      "statisticsGroupCode": "",
      "lastModifiedDateTime": "2024-06-15T10:00:00Z"
    }
  ]
}
```

### 2. Get a Single Employee

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/employees({id})
Authorization: Bearer {access_token}
Accept: application/json
```

### 3. Create an Employee

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/employees
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "givenName": "Bob",
  "surname": "Wilson",
  "jobTitle": "Accountant",
  "email": "bob.wilson@company.com",
  "employmentDate": "2024-06-17"
}
```

**Response:** `201 Created`

### 4. Update an Employee

```http
PATCH {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/employees({id})
Authorization: Bearer {access_token}
Content-Type: application/json
If-Match: {etag}

{
  "jobTitle": "Senior Accountant",
  "phoneNumber": "+1 206-555-0200"
}
```

### 5. Delete an Employee

```http
DELETE {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/employees({id})
Authorization: Bearer {access_token}
If-Match: {etag}
```

**Response:** `204 No Content`

---

## Field Definitions

| Field                    | Type     | Editable | Description                           |
|--------------------------|----------|----------|---------------------------------------|
| `id`                     | GUID     | No       | Unique identifier                     |
| `number`                 | string   | Yes      | Employee number                       |
| `displayName`            | string   | No       | Full name (read-only, computed)       |
| `givenName`              | string   | Yes      | First name                            |
| `middleName`             | string   | Yes      | Middle name                           |
| `surname`                | string   | Yes      | Last name                             |
| `jobTitle`               | string   | Yes      | Job title                             |
| `addressLine1`           | string   | Yes      | Address line 1                        |
| `addressLine2`           | string   | Yes      | Address line 2                        |
| `city`                   | string   | Yes      | City                                  |
| `state`                  | string   | Yes      | State / province                      |
| `country`                | string   | Yes      | Country/region code                   |
| `postalCode`             | string   | Yes      | Postal / ZIP code                     |
| `phoneNumber`            | string   | Yes      | Work phone number                     |
| `mobilePhone`            | string   | Yes      | Mobile phone number                   |
| `email`                  | string   | Yes      | Work email address                    |
| `personalEmail`          | string   | Yes      | Personal email address                |
| `employmentDate`         | date     | Yes      | Employment start date                 |
| `terminationDate`        | date     | Yes      | Termination date (0001-01-01 if active) |
| `status`                 | enum     | No       | `Active`, `Inactive`                  |
| `birthDate`              | date     | Yes      | Date of birth                         |
| `statisticsGroupCode`    | string   | Yes      | Statistics group code                 |
| `lastModifiedDateTime`   | datetime | No       | Last modified timestamp               |

---

## Sub-Resources

| Sub-Resource           | Path                                                      | Methods                    |
|------------------------|-----------------------------------------------------------|----------------------------|
| defaultDimensions      | `.../employees({id})/defaultDimensions`                   | GET, POST, PATCH, DELETE   |
| picture                | `.../employees({id})/picture`                             | GET, PATCH, DELETE          |
| timeRegistrationEntries| `.../employees({id})/timeRegistrationEntries`             | GET, POST, PATCH, DELETE   |

---

## OData Query Examples

### Filter by status

```
$filter=status eq 'Active'
```

### Search by name

```
$filter=contains(displayName, 'Johnson')
```

### Filter by employment date range

```
$filter=employmentDate ge 2024-01-01
```

### Select specific fields

```
$select=number,displayName,jobTitle,email,status
```

---

## Common Errors

| Status | Error                  | Cause & Resolution                                                                 |
|--------|------------------------|------------------------------------------------------------------------------------|
| `401`  | Unauthorized           | Access token is missing, expired, or invalid. Request a new token.                 |
| `404`  | Not Found              | The employee does not exist. Verify the ID.                                        |
| `409`  | Conflict               | ETag mismatch — the record was modified since your last GET. Fetch and retry.      |
