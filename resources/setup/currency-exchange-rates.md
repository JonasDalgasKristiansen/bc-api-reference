# Currency Exchange Rates

Currency exchange rates define the conversion rates between currencies. These are typically read-only via the API and managed in the BC web client or through data feeds.

---

## Base Endpoint

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/currencyExchangeRates
```

---

## Operations

### 1. List All Exchange Rates

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/currencyExchangeRates
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "value": [
    {
      "id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
      "currencyCode": "EUR",
      "startingDate": "2024-06-01",
      "exchangeRateAmount": 100.0,
      "relationalCurrencyCode": "",
      "relationalExchangeRateAmount": 108.50,
      "lastModifiedDateTime": "2024-06-01T00:00:00Z"
    },
    {
      "id": "b2c3d4e5-f6a7-8901-bcde-f23456789012",
      "currencyCode": "GBP",
      "startingDate": "2024-06-01",
      "exchangeRateAmount": 100.0,
      "relationalCurrencyCode": "",
      "relationalExchangeRateAmount": 126.75,
      "lastModifiedDateTime": "2024-06-01T00:00:00Z"
    }
  ]
}
```

> This endpoint is typically **read-only** (GET only).

---

## Field Definitions

| Field                            | Type     | Description                              |
|----------------------------------|----------|------------------------------------------|
| `id`                             | GUID     | Unique identifier                        |
| `currencyCode`                   | string   | Currency code (e.g., EUR, GBP)           |
| `startingDate`                   | date     | Date the rate takes effect               |
| `exchangeRateAmount`             | decimal  | Exchange rate amount (usually 100)       |
| `relationalCurrencyCode`         | string   | Relational currency (empty = LCY)        |
| `relationalExchangeRateAmount`   | decimal  | Amount in relational currency            |
| `lastModifiedDateTime`           | datetime | Last modified timestamp                  |

---

## OData Query Examples

### Filter by currency

```
$filter=currencyCode eq 'EUR'
```

### Get latest rates

```
$filter=currencyCode eq 'EUR'&$orderby=startingDate desc&$top=1
```

### Get rates for a date range

```
$filter=startingDate ge 2024-01-01 and startingDate le 2024-06-30
```
