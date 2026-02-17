# Vendors

Vendors are the suppliers and service providers that your Business Central company purchases from. The vendors endpoint supports full CRUD operations and provides access to vendor balances, payment terms, and default dimensions.

---

## Base Endpoint

```
{{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/vendors
```

---

## Operations

### 1. List All Vendors

```http
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/vendors
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "@odata.context": "https://api.businesscentral.dynamics.com/v2.0/.../vendors",
  "value": [
    {
      "@odata.etag": "W/\"JzE5OzExMjIzMzQ0NTU7VkVORDE7Jw==\"",
      "id": "a1a2a3a4-b5b6-c7c8-d9d0-e1e2e3e4e5e6",
      "number": "10000",
      "displayName": "Fabrikam Supplies",
      "addressLine1": "789 Industrial Blvd",
      "addressLine2": "Unit 12",
      "city": "Detroit",
      "state": "MI",
      "country": "US",
      "postalCode": "48201",
      "phoneNumber": "+1-313-555-0400",
      "email": "orders@fabrikamsupplies.com",
      "website": "https://www.fabrikamsupplies.com",
      "taxRegistrationNumber": "54-3210987",
      "currencyId": "00000000-0000-0000-0000-000000000000",
      "currencyCode": "USD",
      "irs1099Code": "MISC-03",
      "paymentTermsId": "a1a1a1a1-b2b2-c3c3-d4d4-e5e5e5e5e5e5",
      "paymentMethodId": "e5e5e5e5-d4d4-c3c3-b2b2-a1a1a1a1a1a1",
      "taxLiable": true,
      "blocked": " ",
      "balance": -12500.00,
      "lastModifiedDateTime": "2025-02-14T11:00:00Z"
    },
    {
      "@odata.etag": "W/\"JzE5OzIyMzM0NDU1NjY7VkVORDI7Jw==\"",
      "id": "b2b3b4b5-c6c7-d8d9-e0e1-f2f3f4f5f6f7",
      "number": "20000",
      "displayName": "Contoso Electronics",
      "addressLine1": "456 Tech Park Drive",
      "addressLine2": "",
      "city": "San Jose",
      "state": "CA",
      "country": "US",
      "postalCode": "95110",
      "phoneNumber": "+1-408-555-0500",
      "email": "ap@contosoelectronics.com",
      "website": "https://www.contosoelectronics.com",
      "taxRegistrationNumber": "43-2109876",
      "currencyId": "00000000-0000-0000-0000-000000000000",
      "currencyCode": "USD",
      "irs1099Code": "",
      "paymentTermsId": "a1a1a1a1-b2b2-c3c3-d4d4-e5e5e5e5e5e5",
      "paymentMethodId": "e5e5e5e5-d4d4-c3c3-b2b2-a1a1a1a1a1a1",
      "taxLiable": true,
      "blocked": " ",
      "balance": -45300.75,
      "lastModifiedDateTime": "2025-02-13T16:45:00Z"
    }
  ]
}
```

---

### 2. Get a Single Vendor

```http
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/vendors(a1a2a3a4-b5b6-c7c8-d9d0-e1e2e3e4e5e6)
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:** Same structure as a single item in the list above, including the `@odata.etag`.

---

### 3. Create a Vendor

```http
POST {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/vendors
Authorization: Bearer {access_token}
Content-Type: application/json
```

**Request Body:**

```json
{
  "displayName": "Northwind Traders",
  "addressLine1": "123 Commerce Street",
  "addressLine2": "Floor 5",
  "city": "Seattle",
  "state": "WA",
  "country": "US",
  "postalCode": "98101",
  "phoneNumber": "+1-206-555-0600",
  "email": "vendor@northwindtraders.com",
  "website": "https://www.northwindtraders.com",
  "taxRegistrationNumber": "32-1098765",
  "currencyCode": "USD",
  "irs1099Code": "MISC-03",
  "paymentTermsId": "a1a1a1a1-b2b2-c3c3-d4d4-e5e5e5e5e5e5",
  "paymentMethodId": "e5e5e5e5-d4d4-c3c3-b2b2-a1a1a1a1a1a1",
  "taxLiable": true
}
```

**Response (201 Created):**

```json
{
  "@odata.etag": "W/\"JzE5OzMzNDQ1NTY2Nzc7VkVORDM7Jw==\"",
  "id": "c3c4c5c6-d7d8-e9e0-f1f2-a3a4a5a6a7a8",
  "number": "30000",
  "displayName": "Northwind Traders",
  "addressLine1": "123 Commerce Street",
  "addressLine2": "Floor 5",
  "city": "Seattle",
  "state": "WA",
  "country": "US",
  "postalCode": "98101",
  "phoneNumber": "+1-206-555-0600",
  "email": "vendor@northwindtraders.com",
  "website": "https://www.northwindtraders.com",
  "taxRegistrationNumber": "32-1098765",
  "currencyId": "00000000-0000-0000-0000-000000000000",
  "currencyCode": "USD",
  "irs1099Code": "MISC-03",
  "paymentTermsId": "a1a1a1a1-b2b2-c3c3-d4d4-e5e5e5e5e5e5",
  "paymentMethodId": "e5e5e5e5-d4d4-c3c3-b2b2-a1a1a1a1a1a1",
  "taxLiable": true,
  "blocked": " ",
  "balance": 0.00,
  "lastModifiedDateTime": "2025-02-17T10:00:00Z"
}
```

---

### 4. Update a Vendor

```http
PATCH {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/vendors(a1a2a3a4-b5b6-c7c8-d9d0-e1e2e3e4e5e6)
Authorization: Bearer {access_token}
Content-Type: application/json
If-Match: W/"JzE5OzExMjIzMzQ0NTU7VkVORDE7Jw=="
```

**Request Body:**

```json
{
  "email": "new-orders@fabrikamsupplies.com",
  "phoneNumber": "+1-313-555-0499"
}
```

**Response (200 OK):** Returns the full updated vendor object with a new `@odata.etag`.

---

### 5. Delete a Vendor

```http
DELETE {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/vendors(a1a2a3a4-b5b6-c7c8-d9d0-e1e2e3e4e5e6)
Authorization: Bearer {access_token}
If-Match: W/"JzE5OzExMjIzMzQ0NTU7VkVORDE7Jw=="
```

**Response:** `204 No Content`

---

## Fields

| Field                    | Type     | Writable | Description                                              |
|--------------------------|----------|----------|----------------------------------------------------------|
| `id`                     | GUID     | No       | Unique identifier                                        |
| `number`                 | string   | Yes*     | Vendor number (auto-generated if omitted on POST)        |
| `displayName`            | string   | Yes      | Vendor name                                              |
| `addressLine1`           | string   | Yes      | Street address line 1                                    |
| `addressLine2`           | string   | Yes      | Street address line 2                                    |
| `city`                   | string   | Yes      | City                                                     |
| `state`                  | string   | Yes      | State / province                                         |
| `country`                | string   | Yes      | Country code (e.g., `US`)                                |
| `postalCode`             | string   | Yes      | ZIP / postal code                                        |
| `phoneNumber`            | string   | Yes      | Phone number                                             |
| `email`                  | string   | Yes      | Email address                                            |
| `website`                | string   | Yes      | Website URL                                              |
| `taxRegistrationNumber`  | string   | Yes      | Tax / VAT registration number                            |
| `currencyId`             | GUID     | Yes      | Currency identifier (empty GUID = local currency)        |
| `currencyCode`           | string   | Yes      | Currency code (e.g., `USD`, `EUR`)                       |
| `irs1099Code`            | string   | Yes      | IRS 1099 reporting code (US only)                        |
| `paymentTermsId`         | GUID     | Yes      | Payment terms identifier                                 |
| `paymentMethodId`        | GUID     | Yes      | Payment method identifier                                |
| `taxLiable`              | boolean  | Yes      | Whether the vendor is tax liable                         |
| `blocked`                | string   | Yes      | Blank (` `) = not blocked, `Payment`, `All`              |
| `balance`                | decimal  | No       | Current balance (negative = you owe them) (read-only)    |
| `lastModifiedDateTime`   | datetime | No       | Last modification timestamp                              |

---

## Expand Options

```http
# Related currency record
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/vendors?$expand=currency

# Related payment terms record
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/vendors?$expand=paymentTerm

# Related payment method record
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/vendors?$expand=paymentMethod

# Default dimensions assigned to the vendor
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/vendors?$expand=defaultDimensions

# Vendor picture / logo
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/vendors?$expand=picture

# Multiple expands
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/vendors?$expand=currency,paymentTerm,paymentMethod,defaultDimensions
```

---

## Filter Examples

```http
# Only non-blocked vendors
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/vendors?$filter=blocked eq ' '

# Vendors with outstanding balance (you owe them)
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/vendors?$filter=balance lt 0

# Search by name
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/vendors?$filter=contains(displayName,'Fabrikam')

# Filter by currency
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/vendors?$filter=currencyCode eq 'USD'

# Recently modified
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/vendors?$filter=lastModifiedDateTime gt 2024-01-01T00:00:00Z

# Vendors with 1099 codes (US)
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/vendors?$filter=irs1099Code ne ''

# Combined: non-blocked with balance, select key fields
GET {{BC_BASE_URL}}/companies({{BC_COMPANY_ID}})/vendors?$filter=blocked eq ' ' and balance lt 0&$select=number,displayName,balance,email&$orderby=balance asc&$top=20
```

---

## ETag / If-Match

When updating (PATCH) or deleting (DELETE) a vendor, you **must** include the `If-Match` header:

1. **GET** the vendor to obtain the `@odata.etag` from the response
2. Pass it as: `If-Match: W/"JzE5OzExMjIzMzQ0NTU7VkVORDE7Jw=="`
3. If the record was modified between your GET and update, you'll receive `409 Conflict`
4. On conflict: GET the vendor again, get the new ETag, and retry

---

## Common Errors

| Status | Error                  | Cause & Resolution                                                                 |
|--------|------------------------|------------------------------------------------------------------------------------|
| `401`  | Unauthorized           | Access token is missing, expired, or invalid. Request a new token.                 |
| `404`  | Not Found              | The vendor ID does not exist. Verify the GUID.                                     |
| `400`  | Bad Request            | Invalid field values in POST/PATCH body. Check data types and required fields.     |
| `409`  | Conflict               | ETag mismatch on PATCH/DELETE. GET the record again and retry with the new ETag.   |
