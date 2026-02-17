# Salespersons / Purchasers

Salespersons and purchasers represent the internal sales reps and buyers who are assigned to customer/vendor transactions.

---

## Base Endpoint

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salespersonsPurchasers
```

---

## Operations

### 1. List All Salespersons/Purchasers

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salespersonsPurchasers
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
      "code": "JR",
      "displayName": "Jim Rivera",
      "email": "jim.rivera@company.com",
      "email2": "",
      "phoneNo": "+1 206-555-0100",
      "jobTitle": "Sales Manager",
      "commisionPercent": 5.0,
      "privacyBlocked": false,
      "blocked": false,
      "lastModifiedDateTime": "2024-06-15T10:00:00Z"
    }
  ]
}
```

> **Note:** The field `commisionPercent` is intentionally misspelled (missing an 's') — this is the actual field name in Microsoft's API.

### 2. Create a Salesperson

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salespersonsPurchasers
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "code": "AS",
  "displayName": "Anna Schmidt",
  "email": "anna.schmidt@company.com",
  "jobTitle": "Account Executive",
  "commisionPercent": 3.5
}
```

### 3. Update / Delete

Standard PATCH with If-Match and DELETE with If-Match.

---

## Field Definitions

| Field                    | Type     | Editable | Description                              |
|--------------------------|----------|----------|------------------------------------------|
| `id`                     | GUID     | No       | Unique identifier                        |
| `code`                   | string   | Yes      | Salesperson/purchaser code               |
| `displayName`            | string   | Yes      | Full name                                |
| `email`                  | string   | Yes      | Primary email                            |
| `email2`                 | string   | Yes      | Secondary email                          |
| `phoneNo`                | string   | Yes      | Phone number                             |
| `jobTitle`               | string   | Yes      | Job title                                |
| `commisionPercent`       | decimal  | Yes      | Commission percentage (note: typo is intentional) |
| `privacyBlocked`         | boolean  | Yes      | Privacy blocked flag                     |
| `blocked`                | boolean  | Yes      | Blocked flag                             |
| `lastModifiedDateTime`   | datetime | No       | Last modified timestamp                  |

---

## Important Notes

- `commisionPercent` has a typo (missing an 's') — this is Microsoft's actual field name, use it as-is
- Salespersons are referenced on sales documents, purchasers on purchase documents
- The same entity handles both salespersons and purchasers
