# Country/Regions

Country and region codes used throughout Business Central for addresses, tax setup, and international trade.

---

## Base Endpoint

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/countriesRegions
```

---

## Operations

### 1. List All Countries/Regions

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/countriesRegions
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
      "code": "US",
      "displayName": "United States",
      "addressFormat": "City+County+Post Code",
      "lastModifiedDateTime": "2024-01-01T00:00:00Z"
    },
    {
      "@odata.etag": "W/\"JzIwOzEyMzQ1Njc4OTA7Jw==\"",
      "id": "b2c3d4e5-f6a7-8901-bcde-f23456789012",
      "code": "DK",
      "displayName": "Denmark",
      "addressFormat": "Post Code+City",
      "lastModifiedDateTime": "2024-01-01T00:00:00Z"
    }
  ]
}
```

### 2. Create a Country/Region

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/countriesRegions
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "code": "XX",
  "displayName": "Custom Region"
}
```

### 3. Update a Country/Region

```http
PATCH {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/countriesRegions({id})
Authorization: Bearer {access_token}
Content-Type: application/json
If-Match: {etag}

{
  "displayName": "Updated Region Name"
}
```

---

## Field Definitions

| Field                    | Type     | Editable | Description                         |
|--------------------------|----------|----------|-------------------------------------|
| `id`                     | GUID     | No       | Unique identifier                   |
| `code`                   | string   | Yes      | Country/region code (e.g., US, DK)  |
| `displayName`            | string   | Yes      | Display name                        |
| `addressFormat`          | enum     | Yes      | Address format for this country     |
| `lastModifiedDateTime`   | datetime | No       | Last modified timestamp             |
