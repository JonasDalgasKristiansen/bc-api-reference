# Company Information

The company information resource provides detailed information about the currently selected Business Central company, including address, contact details, tax registration, and fiscal year settings. Each company has exactly one `companyInformation` record.

---

## Base Endpoint

```
{{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/companyInformation
```

---

## Get Company Information

### Request

```http
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/companyInformation
Authorization: Bearer {access_token}
Accept: application/json
```

### Response

```json
{
  "@odata.context": "https://api.businesscentral.dynamics.com/v2.0/e3a1b2c3-d4e5-6789-abcd-ef0123456789/Production/api/v2.0/$metadata#companies(a1b2c3d4-e5f6-7890-abcd-ef1234567890)/companyInformation",
  "value": [
    {
      "@odata.etag": "W/\"JzE5OzU2Nzg5MDEyMzQ1Njc4OTAxMjM0NTtKT0hOOw==\"",
      "id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
      "displayName": "CRONUS USA, Inc.",
      "industry": "Manufacturing",
      "phoneNumber": "+1-425-555-0100",
      "faxNumber": "+1-425-555-0101",
      "email": "info@cronususa.com",
      "website": "https://www.cronususa.com",
      "taxRegistrationNumber": "12-3456789",
      "currencyCode": "USD",
      "currentFiscalYearStartDate": "2025-01-01",
      "businessProfileId": "",
      "picture@odata.mediaReadLink": "https://api.businesscentral.dynamics.com/v2.0/.../companyInformation(a1b2c3d4-e5f6-7890-abcd-ef1234567890)/picture",
      "address": {
        "street": "7122 South Ashford Street",
        "city": "Atlanta",
        "state": "GA",
        "countryLetterCode": "US",
        "postalCode": "31772"
      },
      "lastModifiedDateTime": "2025-02-10T14:22:18Z"
    }
  ]
}
```

---

## Update Company Information

### Request

```http
PATCH {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/companyInformation({{id}})
Authorization: Bearer {access_token}
Content-Type: application/json
If-Match: W/"JzE5OzU2Nzg5MDEyMzQ1Njc4OTAxMjM0NTtKT0hOOw=="
```

### Request Body

```json
{
  "phoneNumber": "+1-425-555-0200",
  "email": "support@cronususa.com",
  "website": "https://www.cronususa.com",
  "address": {
    "street": "456 Innovation Drive",
    "city": "Atlanta",
    "state": "GA",
    "countryLetterCode": "US",
    "postalCode": "31772"
  }
}
```

### Response

```json
{
  "@odata.context": "https://api.businesscentral.dynamics.com/v2.0/e3a1b2c3-d4e5-6789-abcd-ef0123456789/Production/api/v2.0/$metadata#companies(a1b2c3d4-e5f6-7890-abcd-ef1234567890)/companyInformation/$entity",
  "@odata.etag": "W/\"JzIwOzU2Nzg5MDEyMzQ1Njc4OTAxMjM0NTtKT0hOOw==\"",
  "id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "displayName": "CRONUS USA, Inc.",
  "industry": "Manufacturing",
  "phoneNumber": "+1-425-555-0200",
  "faxNumber": "+1-425-555-0101",
  "email": "support@cronususa.com",
  "website": "https://www.cronususa.com",
  "taxRegistrationNumber": "12-3456789",
  "currencyCode": "USD",
  "currentFiscalYearStartDate": "2025-01-01",
  "businessProfileId": "",
  "address": {
    "street": "456 Innovation Drive",
    "city": "Atlanta",
    "state": "GA",
    "countryLetterCode": "US",
    "postalCode": "31772"
  },
  "lastModifiedDateTime": "2025-02-17T10:45:00Z"
}
```

---

## Fields

| Field                        | Type     | Writable | Description                                        |
|------------------------------|----------|----------|----------------------------------------------------|
| `id`                         | GUID     | No       | Unique identifier (same as company ID)             |
| `displayName`                | string   | Yes      | Company display name                               |
| `industry`                   | string   | Yes      | Industry classification                            |
| `phoneNumber`                | string   | Yes      | Primary phone number                               |
| `faxNumber`                  | string   | Yes      | Fax number                                         |
| `email`                      | string   | Yes      | Primary email address                              |
| `website`                    | string   | Yes      | Company website URL                                |
| `taxRegistrationNumber`      | string   | Yes      | Tax / VAT registration number                      |
| `currencyCode`               | string   | Yes      | Local currency code (e.g., USD, EUR)               |
| `currentFiscalYearStartDate` | date     | No       | Start date of the current fiscal year              |
| `businessProfileId`          | string   | No       | Associated business profile identifier             |
| `address`                    | object   | Yes      | Address object (see below)                         |
| `lastModifiedDateTime`       | datetime | No       | When the record was last modified                  |

### Address Object

| Field              | Type   | Description                   |
|--------------------|--------|-------------------------------|
| `street`           | string | Street address                |
| `city`             | string | City                          |
| `state`            | string | State or province             |
| `countryLetterCode`| string | ISO country code (e.g., US)   |
| `postalCode`       | string | ZIP / postal code             |

---

## ETag / If-Match

When updating company information with PATCH, you **must** include the `If-Match` header with the `@odata.etag` value from a prior GET response.

1. **GET** the company information to retrieve the current `@odata.etag`
2. Include it as `If-Match: {etag}` on your PATCH request
3. If the record was modified between your GET and PATCH, you will receive a `409 Conflict` — GET again and retry

---

## Common Errors

| Status | Error                  | Cause & Resolution                                                                 |
|--------|------------------------|------------------------------------------------------------------------------------|
| `401`  | Unauthorized           | Access token is missing, expired, or invalid. Request a new token.                 |
| `404`  | Not Found              | The company ID is incorrect or the company does not exist.                         |
| `400`  | Bad Request            | The request body contains invalid field values. Check field types and formats.     |
| `409`  | Conflict               | ETag mismatch — the record was modified since your last GET. Fetch and retry.      |
