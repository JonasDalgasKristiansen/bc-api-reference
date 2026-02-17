# Dimensions

Dimensions are analysis codes used to tag transactions for reporting and analysis. Common dimensions include Department, Project, and Region. Each dimension has a set of dimension values, and default dimensions can be assigned to master records (customers, vendors, items, G/L accounts).

---

## Base Endpoint

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/dimensions
```

---

## Operations — Dimensions

### 1. List All Dimensions

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/dimensions
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "value": [
    {
      "@odata.etag": "W/\"JzE5O0QxOyc=\"",
      "id": "a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa",
      "code": "DEPARTMENT",
      "displayName": "Department",
      "lastModifiedDateTime": "2025-01-01T08:00:00Z"
    },
    {
      "@odata.etag": "W/\"JzIwO0QyOyc=\"",
      "id": "b2b2b2b2-2222-2222-2222-bbbbbbbbbbbb",
      "code": "PROJECT",
      "displayName": "Project",
      "lastModifiedDateTime": "2025-01-01T08:00:00Z"
    },
    {
      "@odata.etag": "W/\"JzIxO0QzOyc=\"",
      "id": "c3c3c3c3-3333-3333-3333-cccccccccccc",
      "code": "AREA",
      "displayName": "Area / Region",
      "lastModifiedDateTime": "2025-01-01T08:00:00Z"
    }
  ]
}
```

---

### 2. Get a Single Dimension

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/dimensions(a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa)
Authorization: Bearer {access_token}
```

---

## Dimension Fields

| Field                    | Type     | Writable | Description                                    |
|--------------------------|----------|----------|------------------------------------------------|
| `id`                     | GUID     | No       | Unique identifier                              |
| `code`                   | string   | No       | Dimension code (e.g., `DEPARTMENT`)            |
| `displayName`            | string   | No       | Display name                                   |
| `lastModifiedDateTime`   | datetime | No       | Last modification timestamp                    |

> **Note:** Dimensions themselves are **read-only** through the standard API. Dimension setup is done through the Business Central web client. You can read dimensions and their values via the API.

---

## Dimension Values (Sub-Resource)

### List Dimension Values

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/dimensions(a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa)/dimensionValues
Authorization: Bearer {access_token}
```

**Response:**

```json
{
  "value": [
    {
      "id": "d4d4d4d4-4444-4444-4444-dddddddddddd",
      "code": "SALES",
      "dimensionId": "a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa",
      "displayName": "Sales Department",
      "lastModifiedDateTime": "2025-01-01T08:00:00Z"
    },
    {
      "id": "e5e5e5e5-5555-5555-5555-eeeeeeeeeeee",
      "code": "ADMIN",
      "dimensionId": "a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa",
      "displayName": "Administration",
      "lastModifiedDateTime": "2025-01-01T08:00:00Z"
    },
    {
      "id": "f6f6f6f6-6666-6666-6666-ffffffffffff",
      "code": "PROD",
      "dimensionId": "a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa",
      "displayName": "Production",
      "lastModifiedDateTime": "2025-01-01T08:00:00Z"
    }
  ]
}
```

### Get a Single Dimension Value

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/dimensions(a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa)/dimensionValues(d4d4d4d4-4444-4444-4444-dddddddddddd)
```

---

## Dimension Value Fields

| Field                    | Type     | Description                                    |
|--------------------------|----------|------------------------------------------------|
| `id`                     | GUID     | Unique identifier                              |
| `code`                   | string   | Dimension value code (e.g., `SALES`)           |
| `dimensionId`            | GUID     | Parent dimension ID                            |
| `displayName`            | string   | Display name                                   |
| `lastModifiedDateTime`   | datetime | Last modification timestamp                    |

> **Note:** Dimension values are also **read-only** through the standard API.

---

## Default Dimensions

Default dimensions can be assigned to master records. When a transaction is created for that record, the dimension is automatically applied. Default dimensions are accessed through specific resource endpoints:

### For Customers

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/customers({customerId})/defaultDimensions
```

### For Vendors

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/vendors({vendorId})/defaultDimensions
```

### For Items

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/items({itemId})/defaultDimensions
```

### For G/L Accounts

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/accounts({accountId})/defaultDimensions
```

### Default Dimension Response Example

```json
{
  "value": [
    {
      "@odata.etag": "W/\"JzE5O0REMTsn\"",
      "parentId": "c1d2e3f4-a5b6-7890-cdef-999999999999",
      "dimensionId": "a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa",
      "dimensionCode": "DEPARTMENT",
      "dimensionValueId": "d4d4d4d4-4444-4444-4444-dddddddddddd",
      "dimensionValueCode": "SALES",
      "postingValidation": "Same Code"
    }
  ]
}
```

### Create a Default Dimension

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/customers({customerId})/defaultDimensions
Authorization: Bearer {access_token}
Content-Type: application/json
```

```json
{
  "dimensionCode": "DEPARTMENT",
  "dimensionValueCode": "SALES",
  "postingValidation": "Same Code"
}
```

### Update a Default Dimension

```http
PATCH {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/customers({customerId})/defaultDimensions({dimensionId})
Authorization: Bearer {access_token}
Content-Type: application/json
If-Match: W/"JzE5O0REMTsn"
```

```json
{
  "dimensionValueCode": "ADMIN",
  "postingValidation": "Code Mandatory"
}
```

### Delete a Default Dimension

```http
DELETE {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/customers({customerId})/defaultDimensions({dimensionId})
Authorization: Bearer {access_token}
If-Match: W/"JzE5O0REMTsn"
```

---

## Default Dimension Fields

| Field                  | Type     | Writable | Description                                    |
|------------------------|----------|----------|------------------------------------------------|
| `parentId`             | GUID     | No       | Parent record ID (customer, vendor, etc.)      |
| `dimensionId`          | GUID     | No       | Dimension ID                                   |
| `dimensionCode`        | string   | Yes      | Dimension code (e.g., `DEPARTMENT`)            |
| `dimensionValueId`     | GUID     | No       | Dimension value ID                             |
| `dimensionValueCode`   | string   | Yes      | Dimension value code (e.g., `SALES`)           |
| `postingValidation`    | string   | Yes      | Validation rule (see below)                    |

---

## Posting Validation Values

| Value              | Meaning                                                    |
|--------------------|------------------------------------------------------------|
| ` ` (blank)        | No restriction — dimension is optional                     |
| `Code Mandatory`   | A dimension value must be specified on every transaction   |
| `Same Code`        | Must use this exact dimension value                        |
| `No Code`          | This dimension must NOT be used                            |

---

## Filter Examples

```http
# Get all dimension values for the DEPARTMENT dimension
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/dimensions?$filter=code eq 'DEPARTMENT'

# Get default dimensions for a specific customer (expand dimension values)
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/customers({customerId})/defaultDimensions
```

---

## Common Errors

| Status | Error                  | Cause & Resolution                                                                 |
|--------|------------------------|------------------------------------------------------------------------------------|
| `401`  | Unauthorized           | Access token is missing, expired, or invalid.                                      |
| `404`  | Not Found              | Dimension, dimension value, or parent record does not exist.                       |
| `400`  | Bad Request            | Invalid dimension code, value code, or posting validation.                         |
| `409`  | Conflict               | ETag mismatch. Re-fetch and retry.                                                 |
| `405`  | Method Not Allowed     | Attempting to create/update dimensions or dimension values (read-only).             |
