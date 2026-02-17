# Contacts

Contacts represent people or organizations in the BC CRM module. They can be linked to customers, vendors, bank accounts, or employees. Contacts support full CRUD operations and have sub-resources for pictures and contact information.

---

## Base Endpoint

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/contacts
```

---

## Operations

### 1. List All Contacts

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/contacts
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
      "number": "CT000010",
      "type": "Company",
      "displayName": "Adatum Corporation",
      "jobTitle": "",
      "companyNumber": "CT000010",
      "companyName": "Adatum Corporation",
      "contactBusinessRelation": "Customer",
      "addressLine1": "192 Market Square",
      "addressLine2": "",
      "city": "Atlanta",
      "state": "GA",
      "country": "US",
      "postalCode": "31772",
      "phoneNumber": "+1 425-555-0100",
      "mobilePhoneNumber": "",
      "email": "info@adatum.com",
      "website": "https://www.adatum.com",
      "searchName": "ADATUM CORPORATION",
      "privacyBlocked": false,
      "taxRegistrationNumber": "",
      "lastInteractionDate": "2024-06-10",
      "lastModifiedDateTime": "2024-06-15T10:00:00Z"
    },
    {
      "@odata.etag": "W/\"JzIwOzEyMzQ1Njc4OTA7Jw==\"",
      "id": "b2c3d4e5-f6a7-8901-bcde-f23456789012",
      "number": "CT000011",
      "type": "Person",
      "displayName": "John Smith",
      "jobTitle": "Purchasing Manager",
      "companyNumber": "CT000010",
      "companyName": "Adatum Corporation",
      "contactBusinessRelation": " ",
      "addressLine1": "192 Market Square",
      "addressLine2": "",
      "city": "Atlanta",
      "state": "GA",
      "country": "US",
      "postalCode": "31772",
      "phoneNumber": "+1 425-555-0101",
      "mobilePhoneNumber": "+1 425-555-0102",
      "email": "john.smith@adatum.com",
      "website": "",
      "searchName": "JOHN SMITH",
      "privacyBlocked": false,
      "taxRegistrationNumber": "",
      "lastInteractionDate": "2024-06-12",
      "lastModifiedDateTime": "2024-06-14T08:30:00Z"
    }
  ]
}
```

### 2. Get a Single Contact

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/contacts({id})
Authorization: Bearer {access_token}
Accept: application/json
```

### 3. Create a Contact

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/contacts
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "type": "Company",
  "displayName": "Fabrikam Inc.",
  "addressLine1": "100 Enterprise Way",
  "city": "San Francisco",
  "state": "CA",
  "country": "US",
  "postalCode": "94105",
  "phoneNumber": "+1 415-555-0200",
  "email": "info@fabrikam.com",
  "website": "https://www.fabrikam.com"
}
```

**Response:** `201 Created`

### 4. Update a Contact

```http
PATCH {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/contacts({id})
Authorization: Bearer {access_token}
Content-Type: application/json
If-Match: {etag}

{
  "email": "newemail@fabrikam.com",
  "phoneNumber": "+1 415-555-0201"
}
```

### 5. Delete a Contact

```http
DELETE {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/contacts({id})
Authorization: Bearer {access_token}
If-Match: {etag}
```

**Response:** `204 No Content`

---

## Field Definitions

| Field                      | Type     | Editable | Description                                        |
|----------------------------|----------|----------|----------------------------------------------------|
| `id`                       | GUID     | No       | Unique identifier                                  |
| `number`                   | string   | Yes      | Contact number                                     |
| `type`                     | enum     | Yes      | `Company` or `Person`                              |
| `displayName`              | string   | Yes      | Full name / company name                           |
| `jobTitle`                 | string   | Yes      | Job title (for Person contacts)                    |
| `companyNumber`            | string   | Yes      | Parent company contact number                      |
| `companyName`              | string   | No       | Parent company name (read-only)                    |
| `contactBusinessRelation`  | enum     | No       | Business relation: ` `, `Customer`, `Vendor`, `Bank Account`, `Employee` |
| `addressLine1`             | string   | Yes      | Address line 1                                     |
| `addressLine2`             | string   | Yes      | Address line 2                                     |
| `city`                     | string   | Yes      | City                                               |
| `state`                    | string   | Yes      | State / province                                   |
| `country`                  | string   | Yes      | Country/region code                                |
| `postalCode`               | string   | Yes      | Postal / ZIP code                                  |
| `phoneNumber`              | string   | Yes      | Phone number                                       |
| `mobilePhoneNumber`        | string   | Yes      | Mobile phone number                                |
| `email`                    | string   | Yes      | Email address                                      |
| `website`                  | string   | Yes      | Website URL                                        |
| `searchName`               | string   | Yes      | Search name (uppercase)                            |
| `privacyBlocked`           | boolean  | Yes      | Whether the contact is privacy-blocked             |
| `taxRegistrationNumber`    | string   | Yes      | Tax / VAT registration number                      |
| `lastInteractionDate`      | date     | No       | Date of last interaction (read-only)               |
| `lastModifiedDateTime`     | datetime | No       | Last modified timestamp                            |

---

## Sub-Resources

| Sub-Resource         | Path                                        | Methods              |
|----------------------|---------------------------------------------|----------------------|
| contactInformation   | `.../contacts({id})/contactInformation`     | GET                  |
| picture              | `.../contacts({id})/picture`                | GET, PATCH, DELETE   |

---

## OData Query Examples

### Filter by type

```
$filter=type eq 'Company'
$filter=type eq 'Person'
```

### Search by name

```
$filter=contains(displayName, 'Adatum')
```

### Filter by email domain

```
$filter=contains(email, 'adatum.com')
```

### Filter contacts linked to customers

```
$filter=contactBusinessRelation eq 'Customer'
```

### Get contacts for a specific company

```
$filter=companyNumber eq 'CT000010'
```

### Select specific fields

```
$select=number,displayName,type,email,phoneNumber,companyName
```

---

## Common Errors

| Status | Error                  | Cause & Resolution                                                                 |
|--------|------------------------|------------------------------------------------------------------------------------|
| `401`  | Unauthorized           | Access token is missing, expired, or invalid. Request a new token.                 |
| `404`  | Not Found              | The contact does not exist. Verify the ID.                                         |
| `400`  | Bad Request            | Invalid field values. Check that `type` is `Company` or `Person`.                  |
| `409`  | Conflict               | ETag mismatch — the record was modified since your last GET. Fetch and retry.      |

---

## Important Notes

- `type` must be `Company` or `Person` — a Person contact is always linked to a Company contact via `companyNumber`
- `contactBusinessRelation` is read-only and shows whether the contact is linked to a Customer, Vendor, etc.
- Use the `contactInformation` sub-resource (or top-level endpoint) to see the mapping between contacts and business entities
