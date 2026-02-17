# Financial Reports — Overview

Business Central provides several read-only financial report endpoints that return pre-calculated financial data. These are useful for dashboards, reporting, and financial analysis without having to aggregate general ledger entries yourself.

---

## Available Financial Reports

| Report                      | Endpoint                                              | Description                            |
|-----------------------------|-------------------------------------------------------|----------------------------------------|
| [Balance Sheet](#balance-sheet) | `.../balanceSheet`                                | Assets, liabilities, equity            |
| [Income Statement](#income-statement) | `.../incomeStatement`                      | Revenue and expenses                   |
| [Trial Balance](#trial-balance) | `.../trialBalance`                                | Debit/credit per account               |
| [Cash Flow Statement](#cash-flow-statement) | `.../cashFlowStatement`              | Cash flow summary                      |
| [Retained Earnings](#retained-earnings-statement) | `.../retainedEarningsStatement` | Retained earnings changes              |
| [Aged Accounts Receivable](#aged-accounts-receivable) | `.../agedAccountsReceivable` | Customer aging                    |
| [Aged Accounts Payable](#aged-accounts-payable) | `.../agedAccountsPayable`       | Vendor aging                           |
| [Customer Sales](#customer-sales) | `.../customerSales`                            | Sales summary by customer              |
| [Vendor Purchases](#vendor-purchases) | `.../vendorPurchases`                      | Purchase summary by vendor             |

> All financial report endpoints are **GET only** (read-only).

---

## Balance Sheet

```
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/balanceSheet
```

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/balanceSheet
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "value": [
    {
      "id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
      "lineNumber": 10000,
      "display": "Current Assets",
      "balance": 125000.00,
      "lineType": "header",
      "indentation": 0,
      "dateFilter": "2024-06-30"
    },
    {
      "id": "b2c3d4e5-f6a7-8901-bcde-f23456789012",
      "lineNumber": 20000,
      "display": "Cash and Bank Accounts",
      "balance": 85000.00,
      "lineType": "detail",
      "indentation": 1,
      "dateFilter": "2024-06-30"
    }
  ]
}
```

| Field          | Type     | Description                         |
|----------------|----------|-------------------------------------|
| `id`           | GUID     | Unique identifier                   |
| `lineNumber`   | integer  | Line number for ordering            |
| `display`      | string   | Row label                           |
| `balance`      | decimal  | Balance amount                      |
| `lineType`     | string   | Row type (header, detail, total)    |
| `indentation`  | integer  | Indentation level for hierarchy     |
| `dateFilter`   | date     | Date filter applied                 |

---

## Income Statement

```
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/incomeStatement
```

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/incomeStatement
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "value": [
    {
      "id": "c3d4e5f6-a7b8-9012-cdef-345678901234",
      "lineNumber": 10000,
      "display": "Sales Revenue",
      "netChange": 250000.00,
      "lineType": "detail",
      "indentation": 1,
      "dateFilter": "2024-06-30"
    }
  ]
}
```

| Field          | Type     | Description                         |
|----------------|----------|-------------------------------------|
| `id`           | GUID     | Unique identifier                   |
| `lineNumber`   | integer  | Line number for ordering            |
| `display`      | string   | Row label                           |
| `netChange`    | decimal  | Net change amount                   |
| `lineType`     | string   | Row type                            |
| `indentation`  | integer  | Indentation level                   |
| `dateFilter`   | date     | Date filter applied                 |

---

## Trial Balance

```
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/trialBalance
```

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/trialBalance
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "value": [
    {
      "accountId": "d4e5f6a7-b8c9-0123-defa-456789012345",
      "number": "1000",
      "accountType": "Posting",
      "display": "Cash",
      "totalDebit": "150000.00",
      "totalCredit": "65000.00",
      "balanceAtDateDebit": "85000.00",
      "balanceAtDateCredit": "0.00",
      "dateFilter": "2024-06-30"
    }
  ]
}
```

| Field                  | Type     | Description                              |
|------------------------|----------|------------------------------------------|
| `accountId`            | GUID     | G/L Account identifier                   |
| `number`               | string   | Account number                           |
| `accountType`          | enum     | `Posting`, `Heading`, `Total`, `Begin Total`, `End Total` |
| `display`              | string   | Account name                             |
| `totalDebit`           | string   | Total debit amount                       |
| `totalCredit`          | string   | Total credit amount                      |
| `balanceAtDateDebit`   | string   | Balance at date — debit side             |
| `balanceAtDateCredit`  | string   | Balance at date — credit side            |
| `dateFilter`           | date     | Date filter applied                      |

---

## Cash Flow Statement

```
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/cashFlowStatement
```

Same structure as Income Statement (`id`, `lineNumber`, `display`, `netChange`, `lineType`, `indentation`, `dateFilter`).

---

## Retained Earnings Statement

```
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/retainedEarningsStatement
```

Same structure as Income Statement (`id`, `lineNumber`, `display`, `netChange`, `lineType`, `indentation`, `dateFilter`).

---

## Aged Accounts Receivable

```
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/agedAccountsReceivable
```

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/agedAccountsReceivable
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "value": [
    {
      "customerId": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
      "customerNumber": "10000",
      "name": "Adatum Corporation",
      "currencyCode": "USD",
      "balanceDue": 15000.00,
      "currentAmount": 5000.00,
      "period1Label": "1-30 days",
      "period1Amount": 3000.00,
      "period2Label": "31-60 days",
      "period2Amount": 4000.00,
      "period3Label": "61+ days",
      "period3Amount": 3000.00,
      "agedAsOfDate": "2024-06-30",
      "periodLengthFilter": "30D"
    }
  ]
}
```

| Field                | Type     | Description                              |
|----------------------|----------|------------------------------------------|
| `customerId`         | GUID     | Customer identifier                      |
| `customerNumber`     | string   | Customer number                          |
| `name`               | string   | Customer name                            |
| `currencyCode`       | string   | Currency code                            |
| `balanceDue`         | decimal  | Total balance due                        |
| `currentAmount`      | decimal  | Current (not overdue) amount             |
| `period1Label`       | string   | Label for period 1                       |
| `period1Amount`      | decimal  | Amount in period 1                       |
| `period2Label`       | string   | Label for period 2                       |
| `period2Amount`      | decimal  | Amount in period 2                       |
| `period3Label`       | string   | Label for period 3                       |
| `period3Amount`      | decimal  | Amount in period 3                       |
| `agedAsOfDate`       | date     | As-of date for aging                     |
| `periodLengthFilter` | string   | Period length (e.g., "30D")              |

---

## Aged Accounts Payable

```
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/agedAccountsPayable
```

Same structure as Aged Accounts Receivable, but with `vendorId`, `vendorNumber` instead of customer fields.

---

## Customer Sales

```
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/customerSales
```

```json
{
  "value": [
    {
      "customerId": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
      "customerNumber": "10000",
      "name": "Adatum Corporation",
      "totalSalesAmount": 75000.00,
      "dateFilter_FilterOnly": "2024-01-01..2024-06-30"
    }
  ]
}
```

| Field                   | Type     | Description                     |
|-------------------------|----------|---------------------------------|
| `customerId`            | GUID     | Customer identifier             |
| `customerNumber`        | string   | Customer number                 |
| `name`                  | string   | Customer name                   |
| `totalSalesAmount`      | decimal  | Total sales amount              |
| `dateFilter_FilterOnly` | date     | Date filter                     |

---

## Vendor Purchases

```
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/vendorPurchases
```

Same structure as Customer Sales, with `vendorId`, `vendorNumber`, `totalPurchaseAmount`.

---

## OData Query Examples

### Filter aged receivables by customer

```
$filter=customerNumber eq '10000'
```

### Filter financial reports by date

```
$filter=dateFilter eq 2024-06-30
```

### Filter customer sales for a period

```
$filter=dateFilter_FilterOnly ge 2024-01-01 and dateFilter_FilterOnly le 2024-06-30
```

### Sort trial balance by account number

```
$orderby=number asc
```
