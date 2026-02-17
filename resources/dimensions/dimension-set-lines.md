# Dimension Set Lines

Dimension set lines represent the dimension values applied to individual transactions (journal lines, sales invoices, purchase orders, etc.). This is different from `defaultDimensions` which are set on master records.

---

## Availability

Dimension set lines are **sub-resources** available on many parent entities:

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesInvoices({id})/dimensionSetLines
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesOrders({id})/dimensionSetLines
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesCreditMemos({id})/dimensionSetLines
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/purchaseInvoices({id})/dimensionSetLines
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/purchaseOrders({id})/dimensionSetLines
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/purchaseCreditMemos({id})/dimensionSetLines
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/journals({jId})/journalLines({jlId})/dimensionSetLines
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/generalLedgerEntries({id})/dimensionSetLines
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/timeRegistrationEntries({id})/dimensionSetLines
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/vendorPaymentJournals({vjId})/vendorPayments({vpId})/dimensionSetLines
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/customerPaymentJournals({cjId})/customerPayments({cpId})/dimensionSetLines
```

Also available on individual **line** entities:

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesInvoices({siId})/salesInvoiceLines({lineId})/dimensionSetLines
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/purchaseOrders({poId})/purchaseOrderLines({lineId})/dimensionSetLines
```

---

## Operations

### 1. List Dimension Set Lines

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesInvoices({id})/dimensionSetLines
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "value": [
    {
      "id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
      "code": "DEPARTMENT",
      "displayName": "Department",
      "parentId": "d1e2f3a4-b5c6-7890-1234-567890abcdef",
      "parentType": "Sales Invoice",
      "valueId": "b2c3d4e5-f6a7-8901-bcde-f23456789012",
      "valueCode": "SALES",
      "valueDisplayName": "Sales Department"
    },
    {
      "id": "c3d4e5f6-a7b8-9012-cdef-345678901234",
      "code": "PROJECT",
      "displayName": "Project",
      "parentId": "d1e2f3a4-b5c6-7890-1234-567890abcdef",
      "parentType": "Sales Invoice",
      "valueId": "e4f5a6b7-c8d9-0123-efab-456789012345",
      "valueCode": "P-10000",
      "valueDisplayName": "Office Renovation"
    }
  ]
}
```

### 2. Add a Dimension Set Line

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesInvoices({id})/dimensionSetLines
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "code": "COSTCENTER",
  "valueCode": "CC-100"
}
```

### 3. Update a Dimension Set Line

```http
PATCH {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesInvoices({id})/dimensionSetLines({dimLineId})
Authorization: Bearer {access_token}
Content-Type: application/json
If-Match: {etag}

{
  "valueCode": "CC-200"
}
```

### 4. Delete a Dimension Set Line

```http
DELETE {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesInvoices({id})/dimensionSetLines({dimLineId})
Authorization: Bearer {access_token}
If-Match: {etag}
```

---

## Field Definitions

| Field                    | Type     | Editable | Description                              |
|--------------------------|----------|----------|------------------------------------------|
| `id`                     | GUID     | No       | Unique identifier                        |
| `code`                   | string   | Yes      | Dimension code                           |
| `consolidationCode`      | string   | No       | Consolidation code                       |
| `parentId`               | GUID     | No       | Parent entity ID                         |
| `parentType`             | enum     | No       | Parent type (e.g., `Sales Invoice`, `Journal Line`, `General Ledger Entry`) |
| `displayName`            | string   | No       | Dimension display name                   |
| `valueId`                | GUID     | Yes      | Dimension value identifier               |
| `valueCode`              | string   | Yes      | Dimension value code                     |
| `valueConsolidationCode` | string   | No       | Value consolidation code                 |
| `valueDisplayName`       | string   | No       | Dimension value display name             |

---

## Default Dimensions

Default dimensions are set on **master records** (customers, vendors, items, employees, accounts) and are automatically applied to new transactions.

### Endpoints

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/customers({id})/defaultDimensions
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/vendors({id})/defaultDimensions
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/items({id})/defaultDimensions
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/employees({id})/defaultDimensions
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/accounts({id})/defaultDimensions
```

### Set a Default Dimension

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/customers({custId})/defaultDimensions
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "dimensionCode": "DEPARTMENT",
  "dimensionValueCode": "SALES",
  "postingValidation": "Code_Mandatory"
}
```

### Default Dimension Field Definitions

| Field                    | Type     | Editable | Description                              |
|--------------------------|----------|----------|------------------------------------------|
| `id`                     | GUID     | No       | Unique identifier                        |
| `parentType`             | enum     | No       | `Customer`, `Item`, `Vendor`, `Employee` |
| `parentId`               | GUID     | No       | Parent entity ID                         |
| `dimensionId`            | GUID     | No       | Dimension identifier                     |
| `dimensionCode`          | string   | Yes      | Dimension code                           |
| `dimensionValueId`       | GUID     | No       | Dimension value identifier               |
| `dimensionValueCode`     | string   | Yes      | Dimension value code                     |
| `postingValidation`      | enum     | Yes      | ` `, `Code_Mandatory`, `Same_Code`, `No_Code` |
| `lastModifiedDateTime`   | datetime | No       | Last modified timestamp                  |

---

## Important Notes

- **dimensionSetLines** are per-transaction — they show what dimensions were applied to a specific invoice, order, journal line, etc.
- **defaultDimensions** are per-master-record — they define what dimensions are automatically applied when you create a new transaction for that customer/vendor/item
- `postingValidation` on default dimensions controls whether a dimension is required, fixed, or forbidden on transactions for that entity
