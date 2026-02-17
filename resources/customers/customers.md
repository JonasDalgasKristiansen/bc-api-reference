# Customers

Customers represent the people or organizations that purchase goods and services from your Business Central company. The customers endpoint is one of the most frequently used API resources — it supports full CRUD operations, multiple sub-resources, and extensive OData filtering.

---

## Base Endpoint

```
{{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/customers
```

---

## Operations

### 1. List All Customers

```http
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/customers
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "@odata.context": "https://api.businesscentral.dynamics.com/v2.0/e3a1b2c3-d4e5-6789-abcd-ef0123456789/Production/api/v2.0/$metadata#companies(a1b2c3d4-e5f6-7890-abcd-ef1234567890)/customers",
  "value": [
    {
      "@odata.etag": "W/\"JzE5OzEyMzQ1Njc4OTA7REFWSUQ7Jw==\"",
      "id": "d1e2f3a4-b5c6-7890-1234-567890abcdef",
      "number": "10000",
      "displayName": "Adatum Corporation",
      "type": "Company",
      "addressLine1": "192 Market Square",
      "addressLine2": "Suite 300",
      "city": "Atlanta",
      "state": "GA",
      "country": "US",
      "postalCode": "31772",
      "phoneNumber": "+1-404-555-0100",
      "email": "accounts@adatum.com",
      "website": "https://www.adatum.com",
      "taxLiable": true,
      "taxAreaId": "c6d7e8f9-a0b1-2345-6789-0abcdef12345",
      "taxAreaDisplayName": "Atlanta, GA",
      "taxRegistrationNumber": "98-7654321",
      "currencyId": "00000000-0000-0000-0000-000000000000",
      "currencyCode": "USD",
      "paymentTermsId": "a1a1a1a1-b2b2-c3c3-d4d4-e5e5e5e5e5e5",
      "shipmentMethodId": "f6f6f6f6-a7a7-b8b8-c9c9-d0d0d0d0d0d0",
      "paymentMethodId": "e5e5e5e5-d4d4-c3c3-b2b2-a1a1a1a1a1a1",
      "blocked": " ",
      "balance": 15750.00,
      "overdueAmount": 3200.00,
      "totalSalesExcludingTax": 245000.00,
      "lastModifiedDateTime": "2025-02-15T09:30:00Z"
    },
    {
      "@odata.etag": "W/\"JzE5OzU2Nzg5MDEyMzQ7QkVOOw==\"",
      "id": "e2f3a4b5-c6d7-8901-2345-67890abcdef1",
      "number": "20000",
      "displayName": "Trey Research",
      "type": "Company",
      "addressLine1": "1 Main Street",
      "addressLine2": "",
      "city": "Dallas",
      "state": "TX",
      "country": "US",
      "postalCode": "74532",
      "phoneNumber": "+1-214-555-0200",
      "email": "info@treyresearch.net",
      "website": "https://www.treyresearch.net",
      "taxLiable": true,
      "taxAreaId": "c6d7e8f9-a0b1-2345-6789-0abcdef12345",
      "taxAreaDisplayName": "Dallas, TX",
      "taxRegistrationNumber": "87-6543210",
      "currencyId": "00000000-0000-0000-0000-000000000000",
      "currencyCode": "USD",
      "paymentTermsId": "a1a1a1a1-b2b2-c3c3-d4d4-e5e5e5e5e5e5",
      "shipmentMethodId": "f6f6f6f6-a7a7-b8b8-c9c9-d0d0d0d0d0d0",
      "paymentMethodId": "e5e5e5e5-d4d4-c3c3-b2b2-a1a1a1a1a1a1",
      "blocked": " ",
      "balance": 8450.50,
      "overdueAmount": 0.00,
      "totalSalesExcludingTax": 125000.00,
      "lastModifiedDateTime": "2025-02-14T16:15:00Z"
    }
  ]
}
```

---

### 2. Get a Single Customer

```http
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/customers(d1e2f3a4-b5c6-7890-1234-567890abcdef)
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:** Same structure as a single item in the list above.

---

### 3. Create a Customer

```http
POST {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/customers
Authorization: Bearer {access_token}
Content-Type: application/json
```

**Request Body:**

```json
{
  "displayName": "Fabrikam, Inc.",
  "type": "Company",
  "addressLine1": "3456 Oak Avenue",
  "addressLine2": "Building C",
  "city": "Chicago",
  "state": "IL",
  "country": "US",
  "postalCode": "60601",
  "phoneNumber": "+1-312-555-0300",
  "email": "billing@fabrikam.com",
  "website": "https://www.fabrikam.com",
  "taxLiable": true,
  "taxRegistrationNumber": "76-5432109",
  "currencyCode": "USD",
  "paymentTermsId": "a1a1a1a1-b2b2-c3c3-d4d4-e5e5e5e5e5e5",
  "paymentMethodId": "e5e5e5e5-d4d4-c3c3-b2b2-a1a1a1a1a1a1"
}
```

**Response (201 Created):**

```json
{
  "@odata.context": "https://api.businesscentral.dynamics.com/v2.0/.../customers/$entity",
  "@odata.etag": "W/\"JzIwOzEyMzQ1Njc4OTA7RkFCUklLOw==\"",
  "id": "f3a4b5c6-d7e8-9012-3456-7890abcdef12",
  "number": "30000",
  "displayName": "Fabrikam, Inc.",
  "type": "Company",
  "addressLine1": "3456 Oak Avenue",
  "addressLine2": "Building C",
  "city": "Chicago",
  "state": "IL",
  "country": "US",
  "postalCode": "60601",
  "phoneNumber": "+1-312-555-0300",
  "email": "billing@fabrikam.com",
  "website": "https://www.fabrikam.com",
  "taxLiable": true,
  "taxAreaId": "00000000-0000-0000-0000-000000000000",
  "taxAreaDisplayName": "",
  "taxRegistrationNumber": "76-5432109",
  "currencyId": "00000000-0000-0000-0000-000000000000",
  "currencyCode": "USD",
  "paymentTermsId": "a1a1a1a1-b2b2-c3c3-d4d4-e5e5e5e5e5e5",
  "shipmentMethodId": "00000000-0000-0000-0000-000000000000",
  "paymentMethodId": "e5e5e5e5-d4d4-c3c3-b2b2-a1a1a1a1a1a1",
  "blocked": " ",
  "balance": 0.00,
  "overdueAmount": 0.00,
  "totalSalesExcludingTax": 0.00,
  "lastModifiedDateTime": "2025-02-17T10:00:00Z"
}
```

---

### 4. Update a Customer

```http
PATCH {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/customers(d1e2f3a4-b5c6-7890-1234-567890abcdef)
Authorization: Bearer {access_token}
Content-Type: application/json
If-Match: W/"JzE5OzEyMzQ1Njc4OTA7REFWSUQ7Jw=="
```

**Request Body:**

```json
{
  "email": "new-billing@adatum.com",
  "phoneNumber": "+1-404-555-0199",
  "addressLine1": "200 Market Square"
}
```

**Response (200 OK):** Returns the full updated customer object with a new `@odata.etag`.

---

### 5. Delete a Customer

```http
DELETE {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/customers(d1e2f3a4-b5c6-7890-1234-567890abcdef)
Authorization: Bearer {access_token}
If-Match: W/"JzE5OzEyMzQ1Njc4OTA7REFWSUQ7Jw=="
```

**Response:** `204 No Content`

---

## Fields

| Field                     | Type     | Writable | Description                                              |
|---------------------------|----------|----------|----------------------------------------------------------|
| `id`                      | GUID     | No       | Unique identifier                                        |
| `number`                  | string   | Yes*     | Customer number (auto-generated if omitted on POST)      |
| `displayName`             | string   | Yes      | Customer name                                            |
| `type`                    | string   | Yes      | `Person` or `Company`                                    |
| `addressLine1`            | string   | Yes      | Street address line 1                                    |
| `addressLine2`            | string   | Yes      | Street address line 2                                    |
| `city`                    | string   | Yes      | City                                                     |
| `state`                   | string   | Yes      | State / province                                         |
| `country`                 | string   | Yes      | Country code (e.g., `US`, `CA`, `GB`)                    |
| `postalCode`              | string   | Yes      | ZIP / postal code                                        |
| `phoneNumber`             | string   | Yes      | Phone number                                             |
| `email`                   | string   | Yes      | Email address                                            |
| `website`                 | string   | Yes      | Website URL                                              |
| `taxLiable`               | boolean  | Yes      | Whether the customer is tax liable                       |
| `taxAreaId`               | GUID     | Yes      | Tax area identifier                                      |
| `taxAreaDisplayName`      | string   | No       | Tax area display name (read-only)                        |
| `taxRegistrationNumber`   | string   | Yes      | Tax / VAT registration number                            |
| `currencyId`              | GUID     | Yes      | Currency identifier (empty GUID = local currency)        |
| `currencyCode`            | string   | Yes      | Currency code (e.g., `USD`, `EUR`)                       |
| `paymentTermsId`          | GUID     | Yes      | Payment terms identifier                                 |
| `shipmentMethodId`        | GUID     | Yes      | Shipment method identifier                               |
| `paymentMethodId`         | GUID     | Yes      | Payment method identifier                                |
| `blocked`                 | string   | Yes      | Blank (` `) = not blocked, `Ship`, `Invoice`, `All`      |
| `balance`                 | decimal  | No       | Current outstanding balance (read-only)                  |
| `overdueAmount`           | decimal  | No       | Overdue amount (read-only)                               |
| `totalSalesExcludingTax`  | decimal  | No       | Lifetime sales excluding tax (read-only)                 |
| `lastModifiedDateTime`    | datetime | No       | Last modification timestamp                              |

---

## Expand Options

Use `$expand` to include related entities in the response:

```http
# Financial details (balance breakdown, credit limit, etc.)
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/customers?$expand=customerFinancialDetails

# Default dimensions assigned to the customer
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/customers?$expand=defaultDimensions

# Customer picture / logo
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/customers?$expand=picture

# Related currency record
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/customers?$expand=currency

# Related payment terms record
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/customers?$expand=paymentTerm

# Related shipment method record
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/customers?$expand=shipmentMethod

# Related payment method record
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/customers?$expand=paymentMethod

# Multiple expands
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/customers?$expand=customerFinancialDetails,defaultDimensions,currency
```

---

## Filter Examples

```http
# Only non-blocked customers
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/customers?$filter=blocked eq ' '

# Customers with outstanding balance
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/customers?$filter=balance gt 0

# Customers with overdue amounts
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/customers?$filter=overdueAmount gt 0

# Recently modified customers
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/customers?$filter=lastModifiedDateTime gt 2024-01-01T00:00:00Z

# Search by name (contains)
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/customers?$filter=contains(displayName,'Corp')

# Search by exact name
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/customers?$filter=displayName eq 'Adatum Corporation'

# Filter by currency
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/customers?$filter=currencyCode eq 'USD'

# Filter by type
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/customers?$filter=type eq 'Company'

# Combined: non-blocked with balance, sorted by balance descending, top 20
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/customers?$filter=blocked eq ' ' and balance gt 0&$orderby=balance desc&$top=20&$select=number,displayName,balance,overdueAmount
```

---

## ETag / If-Match

When updating (PATCH) or deleting (DELETE) a customer, you **must** include the `If-Match` header:

1. **GET** the customer to obtain the `@odata.etag` from the response
2. Pass it as: `If-Match: W/"JzE5OzEyMzQ1Njc4OTA7REFWSUQ7Jw=="`
3. If the record was modified between your GET and update, you'll receive `409 Conflict`
4. On conflict: GET the customer again, get the new ETag, and retry your update

---

## Common Errors

| Status | Error                  | Cause & Resolution                                                                 |
|--------|------------------------|------------------------------------------------------------------------------------|
| `401`  | Unauthorized           | Access token is missing, expired, or invalid. Request a new token.                 |
| `404`  | Not Found              | The customer ID does not exist. Verify the GUID.                                   |
| `400`  | Bad Request            | Invalid field values in POST/PATCH body. Check data types and required fields.     |
| `409`  | Conflict               | ETag mismatch on PATCH/DELETE. GET the record again and retry with the new ETag.   |
