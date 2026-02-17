# Bank Accounts

Bank accounts represent the bank accounts of your Business Central company. They are used in payment journals and financial transactions.

---

## Base Endpoint

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/bankAccounts
```

---

## Operations

### 1. List All Bank Accounts

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/bankAccounts
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
      "number": "CHECKING",
      "displayName": "Company Checking Account",
      "bankAccountNumber": "1234567890",
      "blocked": false,
      "currencyCode": "USD",
      "intercompanyEnabled": false,
      "lastModifiedDateTime": "2024-06-15T10:00:00Z"
    }
  ]
}
```

### 2. Get a Single Bank Account

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/bankAccounts({id})
Authorization: Bearer {access_token}
Accept: application/json
```

### 3. Create a Bank Account

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/bankAccounts
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "number": "SAVINGS",
  "displayName": "Company Savings Account",
  "bankAccountNumber": "9876543210",
  "currencyCode": "USD"
}
```

### 4. Update a Bank Account

```http
PATCH {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/bankAccounts({id})
Authorization: Bearer {access_token}
Content-Type: application/json
If-Match: {etag}

{
  "displayName": "Main Checking Account"
}
```

### 5. Delete a Bank Account

```http
DELETE {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/bankAccounts({id})
Authorization: Bearer {access_token}
If-Match: {etag}
```

---

## Field Definitions

| Field                    | Type     | Editable | Description                         |
|--------------------------|----------|----------|-------------------------------------|
| `id`                     | GUID     | No       | Unique identifier                   |
| `number`                 | string   | Yes      | Bank account code                   |
| `displayName`            | string   | Yes      | Bank account name                   |
| `bankAccountNumber`      | string   | Yes      | Bank account number                 |
| `blocked`                | boolean  | Yes      | Whether the account is blocked      |
| `currencyCode`           | string   | Yes      | Currency code                       |
| `intercompanyEnabled`    | boolean  | Yes      | Intercompany enabled flag           |
| `lastModifiedDateTime`   | datetime | No       | Last modified timestamp             |

---

## Common Errors

| Status | Error                  | Cause & Resolution                                                                 |
|--------|------------------------|------------------------------------------------------------------------------------|
| `401`  | Unauthorized           | Access token is missing, expired, or invalid. Request a new token.                 |
| `404`  | Not Found              | The bank account does not exist. Verify the ID.                                    |
| `409`  | Conflict               | ETag mismatch — the record was modified since your last GET. Fetch and retry.      |
