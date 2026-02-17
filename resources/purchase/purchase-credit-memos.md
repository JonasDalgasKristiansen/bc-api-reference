# Purchase Credit Memos

Purchase credit memos represent credit documents from vendors. They can be created manually or as corrections to posted purchase invoices. Draft credit memos can be edited and then posted using a bound action.

---

## Base Endpoint

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/purchaseCreditMemos
```

---

## Status Values

| Status        | Description                                   |
|---------------|-----------------------------------------------|
| `Draft`       | Credit memo is being prepared, editable       |
| `In Review`   | Credit memo is under approval workflow        |
| `Open`        | Credit memo is posted                         |
| `Canceled`    | Credit memo has been canceled                 |
| `Corrective`  | Credit memo is a corrective document          |

---

## Bound Actions

| Action   | Endpoint                                                           | Description                  |
|----------|--------------------------------------------------------------------|------------------------------|
| `post`   | `POST .../purchaseCreditMemos({id})/Microsoft.NAV.post`            | Post draft credit memo       |
| `cancel` | `POST .../purchaseCreditMemos({id})/Microsoft.NAV.cancel`          | Cancel posted credit memo    |

---

## Operations

### 1. List All Purchase Credit Memos

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/purchaseCreditMemos
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
      "number": "PCM-1001",
      "creditMemoDate": "2024-06-15",
      "postingDate": "2024-06-15",
      "dueDate": "2024-07-15",
      "vendorId": "d1e2f3a4-b5c6-7890-1234-567890abcdef",
      "vendorNumber": "10000",
      "vendorName": "Fabrikam, Inc.",
      "vendorCreditMemoNumber": "FCM-2024-100",
      "payToVendorId": "d1e2f3a4-b5c6-7890-1234-567890abcdef",
      "payToVendorNumber": "10000",
      "payToName": "Fabrikam, Inc.",
      "buyFromAddressLine1": "100 Enterprise Way",
      "buyFromAddressLine2": "",
      "buyFromCity": "San Francisco",
      "buyFromCountry": "US",
      "buyFromState": "CA",
      "buyFromPostCode": "94105",
      "currencyId": "00000000-0000-0000-0000-000000000000",
      "currencyCode": "USD",
      "paymentTermsId": "b2c3d4e5-f6a7-8901-bcde-f23456789012",
      "pricesIncludeTax": false,
      "discountAmount": 0,
      "totalAmountExcludingTax": 500.00,
      "totalTaxAmount": 50.00,
      "totalAmountIncludingTax": 550.00,
      "status": "Draft",
      "lastModifiedDateTime": "2024-06-15T10:00:00Z"
    }
  ]
}
```

### 2. Create a Purchase Credit Memo

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/purchaseCreditMemos
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "vendorNumber": "10000",
  "creditMemoDate": "2024-06-17",
  "vendorCreditMemoNumber": "FCM-2024-200"
}
```

**Response:** `201 Created`

### 3. Post a Purchase Credit Memo

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/purchaseCreditMemos({id})/Microsoft.NAV.post
Authorization: Bearer {access_token}
```

**Response:** `204 No Content`

### 4. Update a Purchase Credit Memo

```http
PATCH {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/purchaseCreditMemos({id})
Authorization: Bearer {access_token}
Content-Type: application/json
If-Match: {etag}

{
  "vendorCreditMemoNumber": "FCM-2024-201"
}
```

### 5. Delete a Purchase Credit Memo

```http
DELETE {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/purchaseCreditMemos({id})
Authorization: Bearer {access_token}
If-Match: {etag}
```

**Response:** `204 No Content` — only Draft credit memos can be deleted.

---

## Field Definitions

| Field                        | Type     | Editable | Description                              |
|------------------------------|----------|----------|------------------------------------------|
| `id`                         | GUID     | No       | Unique identifier                        |
| `number`                     | string   | Yes      | Credit memo number                       |
| `creditMemoDate`             | date     | Yes      | Document date                            |
| `postingDate`                | date     | Yes      | Posting date                             |
| `dueDate`                    | date     | Yes      | Due date                                 |
| `vendorId`                   | GUID     | Yes      | Vendor identifier                        |
| `vendorNumber`               | string   | Yes      | Vendor number                            |
| `vendorName`                 | string   | No       | Vendor display name                      |
| `vendorCreditMemoNumber`     | string   | Yes      | Vendor's credit memo reference number    |
| `payToVendorId`              | GUID     | Yes      | Pay-to vendor identifier                 |
| `payToVendorNumber`          | string   | Yes      | Pay-to vendor number                     |
| `payToName`                  | string   | No       | Pay-to vendor name                       |
| `buyFromAddressLine1`        | string   | Yes      | Buy-from address line 1                  |
| `buyFromAddressLine2`        | string   | Yes      | Buy-from address line 2                  |
| `buyFromCity`                | string   | Yes      | Buy-from city                            |
| `buyFromCountry`             | string   | Yes      | Buy-from country                         |
| `buyFromState`               | string   | Yes      | Buy-from state                           |
| `buyFromPostCode`            | string   | Yes      | Buy-from postal code                     |
| `shortcutDimension1Code`     | string   | Yes      | Shortcut dimension 1                     |
| `shortcutDimension2Code`     | string   | Yes      | Shortcut dimension 2                     |
| `currencyId`                 | GUID     | Yes      | Currency identifier                      |
| `currencyCode`               | string   | Yes      | Currency code                            |
| `paymentTermsId`             | GUID     | Yes      | Payment terms identifier                 |
| `shipmentMethodId`           | GUID     | Yes      | Shipment method identifier               |
| `purchaser`                  | string   | Yes      | Purchaser code                           |
| `pricesIncludeTax`           | boolean  | Yes      | Whether prices include tax               |
| `discountAmount`             | decimal  | No       | Discount amount                          |
| `totalAmountExcludingTax`    | decimal  | No       | Total excl. tax                          |
| `totalTaxAmount`             | decimal  | No       | Total tax                                |
| `totalAmountIncludingTax`    | decimal  | No       | Total incl. tax                          |
| `status`                     | enum     | No       | Document status                          |
| `invoiceId`                  | GUID     | Yes      | Related purchase invoice ID              |
| `invoiceNumber`              | string   | Yes      | Related purchase invoice number          |
| `lastModifiedDateTime`       | datetime | No       | Last modified timestamp                  |

---

## Lines — `purchaseCreditMemoLines`

### Endpoint

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/purchaseCreditMemos({pcmId})/purchaseCreditMemoLines
```

### Create a Line

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/purchaseCreditMemos({pcmId})/purchaseCreditMemoLines
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "lineType": "Item",
  "lineObjectNumber": "1896-S",
  "description": "ATHENS Desk",
  "quantity": 2,
  "unitCost": 250.00
}
```

### Line Field Definitions

| Field                        | Type     | Editable | Description                              |
|------------------------------|----------|----------|------------------------------------------|
| `id`                         | GUID     | No       | Unique line identifier                   |
| `documentId`                 | GUID     | No       | Parent credit memo ID                    |
| `sequence`                   | integer  | No       | Line sequence number                     |
| `itemId`                     | GUID     | Yes      | Item identifier                          |
| `accountId`                  | GUID     | Yes      | G/L account identifier                   |
| `lineType`                   | enum     | Yes      | `Comment`, `Account`, `Item`, `Resource`, `Fixed Asset`, `Charge` |
| `lineObjectNumber`           | string   | Yes      | Item/account number                      |
| `description`                | string   | Yes      | Line description                         |
| `unitOfMeasureId`            | GUID     | Yes      | Unit of measure identifier               |
| `unitOfMeasureCode`          | string   | Yes      | Unit of measure code                     |
| `unitCost`                   | decimal  | Yes      | Unit cost                                |
| `quantity`                   | decimal  | Yes      | Quantity                                 |
| `discountAmount`             | decimal  | Yes      | Discount amount                          |
| `discountPercent`            | decimal  | Yes      | Discount percent                         |
| `amountExcludingTax`         | decimal  | No       | Amount excl. tax                         |
| `amountIncludingTax`         | decimal  | No       | Amount incl. tax                         |
| `totalTaxAmount`             | decimal  | No       | Line tax amount                          |
| `itemVariantId`              | GUID     | Yes      | Item variant identifier                  |
| `locationId`                 | GUID     | Yes      | Location identifier                      |

---

## Common Errors

| Status | Error                  | Cause & Resolution                                                                 |
|--------|------------------------|------------------------------------------------------------------------------------|
| `401`  | Unauthorized           | Access token is missing, expired, or invalid. Request a new token.                 |
| `404`  | Not Found              | The credit memo or line does not exist.                                            |
| `400`  | Bad Request            | Invalid field values. Check lineType, dates, and amounts.                          |
| `409`  | Conflict               | ETag mismatch — the record was modified since your last GET. Fetch and retry.      |
