# Journals (General Journals)

General journals allow you to create and post financial transactions directly to G/L accounts, customer accounts, vendor accounts, and bank accounts. Journals are a two-level resource: journal headers (batches) contain journal lines (individual entries).

---

## Base Endpoint

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/journals
```

---

## Operations — Journal Headers

### 1. List All Journals

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/journals
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "@odata.context": "https://api.businesscentral.dynamics.com/v2.0/.../journals",
  "value": [
    {
      "@odata.etag": "W/\"JzE5OzExO0oxOyc=\"",
      "id": "a1a1a1a1-1111-2222-3333-aaaaaaaaaaaa",
      "code": "GENERAL",
      "displayName": "General Journal",
      "templateDisplayName": "General",
      "lastModifiedDateTime": "2025-02-01T08:00:00Z",
      "balancingAccountId": "00000000-0000-0000-0000-000000000000",
      "balancingAccountNumber": ""
    },
    {
      "@odata.etag": "W/\"JzIwOzEyO0oyOyc=\"",
      "id": "b2b2b2b2-4444-5555-6666-bbbbbbbbbbbb",
      "code": "CASH",
      "displayName": "Cash Receipt Journal",
      "templateDisplayName": "Cash Receipt",
      "lastModifiedDateTime": "2025-02-01T08:00:00Z",
      "balancingAccountId": "a1b2c3d4-e5f6-7890-abcd-111111111111",
      "balancingAccountNumber": "10100"
    }
  ]
}
```

---

### 2. Get a Single Journal

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/journals(a1a1a1a1-1111-2222-3333-aaaaaaaaaaaa)
Authorization: Bearer {access_token}
```

---

### 3. Create a Journal

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/journals
Authorization: Bearer {access_token}
Content-Type: application/json
```

**Request Body:**

```json
{
  "code": "APIJOURNAL",
  "displayName": "API Journal Batch",
  "templateDisplayName": "General"
}
```

---

### 4. Update a Journal

```http
PATCH {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/journals(a1a1a1a1-1111-2222-3333-aaaaaaaaaaaa)
Authorization: Bearer {access_token}
Content-Type: application/json
If-Match: W/"JzE5OzExO0oxOyc="
```

```json
{
  "displayName": "Updated Journal Name"
}
```

---

### 5. Delete a Journal

```http
DELETE {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/journals(a1a1a1a1-1111-2222-3333-aaaaaaaaaaaa)
Authorization: Bearer {access_token}
If-Match: W/"JzE5OzExO0oxOyc="
```

**Response:** `204 No Content`

---

## Journal Header Fields

| Field                      | Type     | Writable | Description                                    |
|----------------------------|----------|----------|------------------------------------------------|
| `id`                       | GUID     | No       | Unique identifier                              |
| `code`                     | string   | Yes      | Journal batch code                             |
| `displayName`              | string   | Yes      | Display name for the journal batch             |
| `templateDisplayName`      | string   | Yes      | Template name (e.g., `General`, `Cash Receipt`)|
| `lastModifiedDateTime`     | datetime | No       | Last modification timestamp                    |
| `balancingAccountId`       | GUID     | Yes      | Default balancing account ID                   |
| `balancingAccountNumber`   | string   | Yes      | Default balancing account number               |

---

## Operations — Journal Lines

### List Lines

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/journals(a1a1a1a1-1111-2222-3333-aaaaaaaaaaaa)/journalLines
Authorization: Bearer {access_token}
```

**Response:**

```json
{
  "value": [
    {
      "@odata.etag": "W/\"JzE5O0pMMTsn\"",
      "id": "c3c3c3c3-7777-8888-9999-cccccccccccc",
      "journalId": "a1a1a1a1-1111-2222-3333-aaaaaaaaaaaa",
      "journalDisplayName": "General Journal",
      "lineNumber": 10000,
      "accountType": "G/L Account",
      "accountId": "a1b2c3d4-e5f6-7890-abcd-666666666666",
      "accountNumber": "60100",
      "postingDate": "2025-02-17",
      "documentNumber": "GJ-001",
      "externalDocumentNumber": "",
      "amount": 5000.00,
      "description": "Monthly office rent",
      "comment": "",
      "taxGroupCode": "",
      "balancingAccountType": "G/L Account",
      "balancingAccountId": "a1b2c3d4-e5f6-7890-abcd-111111111111",
      "balancingAccountNumber": "10100",
      "lastModifiedDateTime": "2025-02-17T09:30:00Z"
    },
    {
      "@odata.etag": "W/\"JzE5O0pMMjsn\"",
      "id": "d4d4d4d4-aaaa-bbbb-cccc-dddddddddddd",
      "journalId": "a1a1a1a1-1111-2222-3333-aaaaaaaaaaaa",
      "journalDisplayName": "General Journal",
      "lineNumber": 20000,
      "accountType": "G/L Account",
      "accountId": "a1b2c3d4-e5f6-7890-abcd-444444444444",
      "accountNumber": "40100",
      "postingDate": "2025-02-17",
      "documentNumber": "GJ-001",
      "externalDocumentNumber": "INV-EXT-2025",
      "amount": -12500.00,
      "description": "Revenue adjustment",
      "comment": "",
      "taxGroupCode": "",
      "balancingAccountType": "G/L Account",
      "balancingAccountId": "a1b2c3d4-e5f6-7890-abcd-111111111111",
      "balancingAccountNumber": "10100",
      "lastModifiedDateTime": "2025-02-17T09:35:00Z"
    }
  ]
}
```

### Create a Journal Line

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/journals(a1a1a1a1-1111-2222-3333-aaaaaaaaaaaa)/journalLines
Authorization: Bearer {access_token}
Content-Type: application/json
```

**Request Body:**

```json
{
  "accountType": "G/L Account",
  "accountNumber": "60100",
  "postingDate": "2025-02-17",
  "documentNumber": "GJ-002",
  "amount": 2500.00,
  "description": "Office supplies",
  "balancingAccountType": "G/L Account",
  "balancingAccountNumber": "10100"
}
```

### Update a Journal Line

```http
PATCH {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/journals(a1a1a1a1-1111-2222-3333-aaaaaaaaaaaa)/journalLines(c3c3c3c3-7777-8888-9999-cccccccccccc)
Authorization: Bearer {access_token}
Content-Type: application/json
If-Match: W/"JzE5O0pMMTsn"
```

```json
{
  "amount": 5500.00,
  "description": "Monthly office rent (revised)"
}
```

### Delete a Journal Line

```http
DELETE {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/journals(a1a1a1a1-1111-2222-3333-aaaaaaaaaaaa)/journalLines(c3c3c3c3-7777-8888-9999-cccccccccccc)
Authorization: Bearer {access_token}
If-Match: W/"JzE5O0pMMTsn"
```

---

## Journal Line Fields

| Field                      | Type     | Writable | Description                                    |
|----------------------------|----------|----------|------------------------------------------------|
| `id`                       | GUID     | No       | Unique identifier                              |
| `journalId`                | GUID     | No       | Parent journal ID                              |
| `journalDisplayName`       | string   | No       | Parent journal name (read-only)                |
| `lineNumber`               | integer  | No       | Line number (auto-assigned)                    |
| `accountType`              | string   | Yes      | `G/L Account`, `Customer`, `Vendor`, `Bank Account`, `Employee` |
| `accountId`                | GUID     | Yes      | Account ID                                     |
| `accountNumber`            | string   | Yes      | Account number                                 |
| `postingDate`              | date     | Yes      | Posting date                                   |
| `documentNumber`           | string   | Yes      | Document number                                |
| `externalDocumentNumber`   | string   | Yes      | External document reference                    |
| `amount`                   | decimal  | Yes      | Amount (positive = debit, negative = credit)   |
| `description`              | string   | Yes      | Description                                    |
| `comment`                  | string   | Yes      | Comment text                                   |
| `taxGroupCode`             | string   | Yes      | Tax group code                                 |
| `balancingAccountType`     | string   | Yes      | `G/L Account`, `Customer`, `Vendor`, `Bank Account` |
| `balancingAccountId`       | GUID     | Yes      | Balancing account ID                           |
| `balancingAccountNumber`   | string   | Yes      | Balancing account number                       |
| `lastModifiedDateTime`     | datetime | No       | Last modification timestamp                    |

---

## Bound Action: Post Journal

Post all lines in a journal batch:

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/journals(a1a1a1a1-1111-2222-3333-aaaaaaaaaaaa)/Microsoft.NAV.post
Authorization: Bearer {access_token}
```

**Response:** `204 No Content`

After posting, all journal lines are transferred to general ledger entries (and customer/vendor/bank ledger entries as appropriate), and the journal lines are cleared.

---

## Full Example: Create and Post a Journal

Here is a complete end-to-end workflow for creating journal lines and posting them:

### Step 1: Create journal lines

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/journals(a1a1a1a1-1111-2222-3333-aaaaaaaaaaaa)/journalLines
Content-Type: application/json
Authorization: Bearer {access_token}
```

```json
{
  "accountType": "G/L Account",
  "accountNumber": "60100",
  "postingDate": "2025-02-17",
  "documentNumber": "GJ-003",
  "amount": 1500.00,
  "description": "Consulting fees",
  "balancingAccountType": "G/L Account",
  "balancingAccountNumber": "10100"
}
```

### Step 2: Post the journal

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/journals(a1a1a1a1-1111-2222-3333-aaaaaaaaaaaa)/Microsoft.NAV.post
Authorization: Bearer {access_token}
```

After posting:
- Journal lines are cleared
- General ledger entries are created
- If `accountType` was `Customer` or `Vendor`, the corresponding sub-ledger entries are also created

---

## Account Type Values

| accountType      | Use Case                                           |
|------------------|----------------------------------------------------|
| `G/L Account`    | Direct posting to a G/L account                    |
| `Customer`       | Apply payment or adjustment to a customer          |
| `Vendor`         | Apply payment or adjustment to a vendor            |
| `Bank Account`   | Record bank transactions                           |
| `Employee`       | Record employee-related transactions               |

---

## Filter Examples (Journal Lines)

```http
# Lines for a specific document number
GET .../journals({id})/journalLines?$filter=documentNumber eq 'GJ-001'

# Lines posted on a specific date
GET .../journals({id})/journalLines?$filter=postingDate eq 2025-02-17

# Debit entries only
GET .../journals({id})/journalLines?$filter=amount gt 0
```

---

## ETag / If-Match

When updating (PATCH) or deleting (DELETE) journals or journal lines, include the `If-Match` header with the `@odata.etag` from a prior GET.

---

## Common Errors

| Status | Error                  | Cause & Resolution                                                                 |
|--------|------------------------|------------------------------------------------------------------------------------|
| `401`  | Unauthorized           | Access token is missing, expired, or invalid. Request a new token.                 |
| `404`  | Not Found              | The journal or journal line ID does not exist.                                     |
| `400`  | Bad Request            | Invalid field values. Common: invalid accountNumber, missing required fields.       |
| `409`  | Conflict               | ETag mismatch. GET the record again and retry with the new ETag.                   |
| `400`  | Unbalanced journal     | When posting: the journal lines do not balance (debits ≠ credits) without balancing accounts. |
