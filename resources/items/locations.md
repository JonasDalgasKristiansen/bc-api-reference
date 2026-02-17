# Locations

Locations represent physical or logical warehouses where inventory is stored. They are used to track item quantities across multiple sites.

---

## Base Endpoint

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/locations
```

---

## Operations

### 1. List All Locations

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/locations
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
      "code": "MAIN",
      "displayName": "Main Warehouse",
      "contact": "John Smith",
      "addressLine1": "100 Warehouse Blvd",
      "addressLine2": "",
      "city": "Seattle",
      "state": "WA",
      "country": "US",
      "postalCode": "98101",
      "phoneNumber": "+1 206-555-0300",
      "email": "warehouse@company.com",
      "lastModifiedDateTime": "2024-06-15T10:00:00Z"
    }
  ]
}
```

### 2. Create a Location

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/locations
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "code": "EAST",
  "displayName": "East Coast Warehouse",
  "addressLine1": "200 Distribution Ave",
  "city": "Atlanta",
  "state": "GA",
  "country": "US",
  "postalCode": "30301"
}
```

### 3. Update a Location

```http
PATCH {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/locations({id})
Authorization: Bearer {access_token}
Content-Type: application/json
If-Match: {etag}

{
  "displayName": "East Coast Distribution Center"
}
```

### 4. Delete a Location

```http
DELETE {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/locations({id})
Authorization: Bearer {access_token}
If-Match: {etag}
```

---

## Field Definitions

| Field                    | Type     | Editable | Description                         |
|--------------------------|----------|----------|-------------------------------------|
| `id`                     | GUID     | No       | Unique identifier                   |
| `code`                   | string   | Yes      | Location code                       |
| `displayName`            | string   | Yes      | Location name                       |
| `contact`                | string   | Yes      | Contact person                      |
| `addressLine1`           | string   | Yes      | Address line 1                      |
| `addressLine2`           | string   | Yes      | Address line 2                      |
| `city`                   | string   | Yes      | City                                |
| `state`                  | string   | Yes      | State / province                    |
| `country`                | string   | Yes      | Country/region code                 |
| `postalCode`             | string   | Yes      | Postal / ZIP code                   |
| `phoneNumber`            | string   | Yes      | Phone number                        |
| `email`                  | string   | Yes      | Email address                       |
| `lastModifiedDateTime`   | datetime | No       | Last modified timestamp             |

---

## Common Errors

| Status | Error                  | Cause & Resolution                                                                 |
|--------|------------------------|------------------------------------------------------------------------------------|
| `401`  | Unauthorized           | Access token is missing, expired, or invalid.                                      |
| `404`  | Not Found              | The location does not exist. Verify the ID.                                        |
| `409`  | Conflict               | ETag mismatch — fetch and retry.                                                   |
