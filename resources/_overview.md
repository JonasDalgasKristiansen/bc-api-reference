# API Overview — Complete Endpoint Reference

This is the master cheat sheet for the entire Microsoft Dynamics 365 Business Central REST API v2.0, including the Automation API. Use this file as a quick reference for all available endpoints, supported methods, OData query patterns, and common headers.

---

## Base URLs

| API              | Base URL                                                                         |
|------------------|----------------------------------------------------------------------------------|
| Standard API     | `{{BC_BASE_URL}}` → `https://api.businesscentral.dynamics.com/v2.0/{tenant}/{environment}/api/v2.0` |
| Automation API   | `{{BC_AUTOMATION_URL}}` → `https://api.businesscentral.dynamics.com/v2.0/{tenant}/{environment}/api/microsoft/automation/v2.0` |
| Admin Center API | `https://api.businesscentral.dynamics.com/admin/v2.28`                           |

---

## Authentication Summary

**Get a token:**

```http
POST https://login.microsoftonline.com/{{BC_TENANT_ID}}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials
&client_id={{BC_CLIENT_ID}}
&client_secret={{BC_CLIENT_SECRET}}
&scope=https://api.businesscentral.dynamics.com/.default
```

**Use the token:**

```
Authorization: Bearer {access_token}
```

Tokens expire after 1 hour. Cache and refresh automatically. See [README.md](../README.md) for the full authentication setup guide.

---

## All Available Endpoints

### Standard API — `{{BC_BASE_URL}}`

| Category   | Resource                   | Endpoint Path                                                         | Methods                    | Notes                             |
|------------|----------------------------|-----------------------------------------------------------------------|----------------------------|-----------------------------------|
| Company    | companies                  | `/companies`                                                          | GET                        | Root-level, no company ID needed  |
| Company    | companyInformation         | `/companies({id})/companyInformation`                                 | GET, PATCH                 | Single record per company         |
| Customers  | customers                  | `/companies({id})/customers`                                          | GET, POST, PATCH, DELETE   |                                   |
| Customers  | customerFinancialDetails   | `/companies({id})/customers({custId})/customerFinancialDetails`       | GET                        | Read-only, expand from customers  |
| Customers  | customers/defaultDimensions| `/companies({id})/customers({custId})/defaultDimensions`              | GET, POST, PATCH, DELETE   | Sub-resource                      |
| Customers  | customers/picture          | `/companies({id})/customers({custId})/picture`                        | GET, PATCH, DELETE         | Binary image data                 |
| Customers  | customerPaymentJournals    | `/companies({id})/customerPaymentJournals`                            | GET, POST, PATCH, DELETE   |                                   |
| Customers  | customerPaymentJournalLines| `/companies({id})/customerPaymentJournals({jId})/customerPaymentJournalLines` | GET, POST, PATCH, DELETE |                              |
| Vendors    | vendors                    | `/companies({id})/vendors`                                            | GET, POST, PATCH, DELETE   |                                   |
| Vendors    | vendors/defaultDimensions  | `/companies({id})/vendors({vendId})/defaultDimensions`                | GET, POST, PATCH, DELETE   | Sub-resource                      |
| Vendors    | vendors/picture            | `/companies({id})/vendors({vendId})/picture`                          | GET, PATCH, DELETE         | Binary image data                 |
| Items      | items                      | `/companies({id})/items`                                              | GET, POST, PATCH, DELETE   |                                   |
| Items      | items/defaultDimensions    | `/companies({id})/items({itemId})/defaultDimensions`                  | GET, POST, PATCH, DELETE   | Sub-resource                      |
| Items      | items/picture              | `/companies({id})/items({itemId})/picture`                            | GET, PATCH, DELETE         | Binary image data                 |
| Items      | itemInventory              | `/companies({id})/items({itemId})/itemInventory`                      | GET                        | Read-only inventory levels        |
| Items      | itemCategories             | `/companies({id})/itemCategories`                                     | GET, POST, PATCH, DELETE   |                                   |
| Items      | locations                  | `/companies({id})/locations`                                          | GET, POST, PATCH, DELETE   |                                   |
| Sales      | salesQuotes                | `/companies({id})/salesQuotes`                                        | GET, POST, PATCH, DELETE   | Actions: makeInvoice, makeOrder, send |
| Sales      | salesQuoteLines            | `/companies({id})/salesQuotes({sqId})/salesQuoteLines`                | GET, POST, PATCH, DELETE   |                                   |
| Sales      | salesOrders                | `/companies({id})/salesOrders`                                        | GET, POST, PATCH, DELETE   | Action: shipAndInvoice            |
| Sales      | salesOrderLines            | `/companies({id})/salesOrders({soId})/salesOrderLines`                | GET, POST, PATCH, DELETE   |                                   |
| Sales      | salesInvoices              | `/companies({id})/salesInvoices`                                      | GET, POST, PATCH, DELETE   | Actions: post, send, cancelAndSend|
| Sales      | salesInvoiceLines          | `/companies({id})/salesInvoices({siId})/salesInvoiceLines`            | GET, POST, PATCH, DELETE   |                                   |
| Sales      | salesCreditMemos           | `/companies({id})/salesCreditMemos`                                   | GET, POST, PATCH, DELETE   | Actions: post, send               |
| Sales      | salesCreditMemoLines       | `/companies({id})/salesCreditMemos({scmId})/salesCreditMemoLines`     | GET, POST, PATCH, DELETE   |                                   |
| Sales      | salesShipments             | `/companies({id})/salesShipments`                                     | GET                        | Read-only                         |
| Sales      | salesShipmentLines         | `/companies({id})/salesShipments({ssId})/salesShipmentLines`          | GET                        | Read-only                         |
| Purchase   | purchaseOrders             | `/companies({id})/purchaseOrders`                                     | GET, POST, PATCH, DELETE   | Actions: receive, receiveAndInvoice |
| Purchase   | purchaseOrderLines         | `/companies({id})/purchaseOrders({poId})/purchaseOrderLines`          | GET, POST, PATCH, DELETE   |                                   |
| Purchase   | purchaseInvoices           | `/companies({id})/purchaseInvoices`                                   | GET, POST, PATCH, DELETE   | Action: post                      |
| Purchase   | purchaseInvoiceLines       | `/companies({id})/purchaseInvoices({piId})/purchaseInvoiceLines`      | GET, POST, PATCH, DELETE   |                                   |
| Purchase   | purchaseReceipts           | `/companies({id})/purchaseReceipts`                                   | GET                        | Read-only                         |
| Purchase   | purchaseReceiptLines       | `/companies({id})/purchaseReceipts({prId})/purchaseReceiptLines`      | GET                        | Read-only                         |
| Finance    | accounts                   | `/companies({id})/accounts`                                           | GET, POST, PATCH, DELETE   | G/L Accounts                      |
| Finance    | generalLedgerEntries       | `/companies({id})/generalLedgerEntries`                               | GET                        | Read-only                         |
| Finance    | journals                   | `/companies({id})/journals`                                           | GET, POST, PATCH, DELETE   | Action: post                      |
| Finance    | journalLines               | `/companies({id})/journals({jId})/journalLines`                       | GET, POST, PATCH, DELETE   |                                   |
| Finance    | bankAccounts               | `/companies({id})/bankAccounts`                                       | GET, POST, PATCH, DELETE   |                                   |
| Setup      | currencies                 | `/companies({id})/currencies`                                         | GET, POST, PATCH, DELETE   |                                   |
| Setup      | paymentTerms               | `/companies({id})/paymentTerms`                                       | GET, POST, PATCH, DELETE   |                                   |
| Setup      | paymentMethods             | `/companies({id})/paymentMethods`                                     | GET, POST, PATCH, DELETE   |                                   |
| Setup      | shipmentMethods            | `/companies({id})/shipmentMethods`                                    | GET, POST, PATCH, DELETE   |                                   |
| Setup      | unitsOfMeasure             | `/companies({id})/unitsOfMeasure`                                     | GET, POST, PATCH, DELETE   |                                   |
| Setup      | dimensions                 | `/companies({id})/dimensions`                                         | GET                        | Typically read-only               |
| Setup      | dimensionValues            | `/companies({id})/dimensions({dimId})/dimensionValues`                | GET                        | Sub-resource                      |
| Setup      | taxAreas                   | `/companies({id})/taxAreas`                                           | GET, POST, PATCH, DELETE   |                                   |
| Setup      | taxGroups                  | `/companies({id})/taxGroups`                                          | GET, POST, PATCH, DELETE   |                                   |
| Employees  | employees                  | `/companies({id})/employees`                                          | GET, POST, PATCH, DELETE   |                                   |
| Employees  | employees/defaultDimensions| `/companies({id})/employees({empId})/defaultDimensions`               | GET, POST, PATCH, DELETE   | Sub-resource                      |
| Employees  | employees/picture          | `/companies({id})/employees({empId})/picture`                         | GET, PATCH, DELETE         | Binary image data                 |
| Webhooks   | subscriptions              | `/subscriptions`                                                      | GET, POST, PATCH, DELETE   | No company ID in path             |

### Automation API — `{{BC_AUTOMATION_URL}}`

| Category    | Resource          | Endpoint Path                                          | Methods                    | Notes                                  |
|-------------|-------------------|--------------------------------------------------------|----------------------------|----------------------------------------|
| Companies   | companies         | `/companies`                                           | GET, POST, PATCH, DELETE   | Manage BC companies                    |
| Users       | users             | `/companies({id})/users`                               | GET, PATCH                 | Action: invite                         |
| Users       | userPermissions   | `/companies({id})/users({userId})/userPermissions`     | GET, POST, DELETE          | Manage permission sets                 |
| Users       | userGroupMembers  | `/companies({id})/users({userId})/userGroupMembers`    | GET, POST, DELETE          | Manage group membership                |
| Extensions  | extensions        | `/companies({id})/extensions`                          | GET                        | Actions: install, uninstall, upload     |

### Admin Center API — `https://api.businesscentral.dynamics.com/admin/v2.28`

| Category      | Resource      | Endpoint Path                                                   | Methods             | Notes          |
|---------------|---------------|-----------------------------------------------------------------|---------------------|----------------|
| Environments  | environments  | `/applications/BusinessCentral/environments`                    | GET, POST, DELETE   | Async create   |
| Versions      | versions      | `/applications/BusinessCentral/countries/{cc}/rings/{ring}/versions` | GET            | Available versions |

---

## All Bound Actions

| Resource                 | Action              | Full Endpoint                                                                     | Description                          |
|--------------------------|---------------------|-----------------------------------------------------------------------------------|--------------------------------------|
| salesQuotes              | makeInvoice         | `POST .../salesQuotes({id})/Microsoft.NAV.makeInvoice`                            | Convert quote to invoice             |
| salesQuotes              | makeOrder           | `POST .../salesQuotes({id})/Microsoft.NAV.makeOrder`                              | Convert quote to order               |
| salesQuotes              | send                | `POST .../salesQuotes({id})/Microsoft.NAV.send`                                   | Email quote to customer              |
| salesOrders              | shipAndInvoice      | `POST .../salesOrders({id})/Microsoft.NAV.shipAndInvoice`                         | Ship and invoice order               |
| salesInvoices            | post                | `POST .../salesInvoices({id})/Microsoft.NAV.post`                                 | Post draft invoice                   |
| salesInvoices            | send                | `POST .../salesInvoices({id})/Microsoft.NAV.send`                                 | Email invoice to customer            |
| salesInvoices            | cancelAndSend       | `POST .../salesInvoices({id})/Microsoft.NAV.cancelAndSend`                        | Cancel and notify customer           |
| salesCreditMemos         | post                | `POST .../salesCreditMemos({id})/Microsoft.NAV.post`                              | Post draft credit memo               |
| salesCreditMemos         | send                | `POST .../salesCreditMemos({id})/Microsoft.NAV.send`                              | Email credit memo to customer        |
| purchaseOrders           | receive             | `POST .../purchaseOrders({id})/Microsoft.NAV.receive`                             | Receive items on PO                  |
| purchaseOrders           | receiveAndInvoice   | `POST .../purchaseOrders({id})/Microsoft.NAV.receiveAndInvoice`                   | Receive and invoice PO               |
| purchaseInvoices         | post                | `POST .../purchaseInvoices({id})/Microsoft.NAV.post`                              | Post draft purchase invoice          |
| journals                 | post                | `POST .../journals({id})/Microsoft.NAV.post`                                      | Post all journal lines               |
| customerPaymentJournals  | post                | `POST .../customerPaymentJournals({id})/Microsoft.NAV.post`                       | Post customer payment journal        |
| users (Automation)       | invite              | `POST .../users({id})/Microsoft.NAV.invite`                                       | Send user invitation email           |
| extensions (Automation)  | install             | `POST .../extensions({id})/Microsoft.NAV.install`                                 | Install extension                    |
| extensions (Automation)  | uninstall           | `POST .../extensions({id})/Microsoft.NAV.uninstall`                               | Uninstall extension                  |
| extensions (Automation)  | upload              | `POST .../extensions/Microsoft.NAV.upload`                                        | Upload .app file                     |

---

## OData Query Cheat Sheet

### Filter Examples

```
$filter=number eq '10000'
$filter=displayName eq 'Contoso Ltd.'
$filter=lastModifiedDateTime gt 2024-01-01T00:00:00Z
$filter=lastModifiedDateTime ge 2024-06-01T00:00:00Z and lastModifiedDateTime lt 2024-07-01T00:00:00Z
$filter=contains(displayName, 'Corp')
$filter=startswith(number, '2')
$filter=status eq 'Open'
$filter=status ne 'Canceled'
$filter=balance gt 0
$filter=amount gt 1000 and currencyCode eq 'USD'
$filter=blocked eq ' '
$filter=type eq 'Inventory'
$filter=category eq 'Income' and accountType eq 'Posting'
```

### Select Examples

```
$select=id,number,displayName
$select=id,number,displayName,balance,email
$select=id,postingDate,documentNumber,debitAmount,creditAmount
```

### Expand Examples

```
$expand=salesInvoiceLines
$expand=customerFinancialDetails
$expand=defaultDimensions
$expand=currency,paymentTerm,shipmentMethod
$expand=itemCategory,inventory
```

### Sorting

```
$orderby=displayName asc
$orderby=balance desc
$orderby=lastModifiedDateTime desc
$orderby=postingDate desc,documentNumber asc
```

### Pagination

```
$top=10
$skip=20
$top=50&$skip=100
```

### Combined

```
?$filter=balance gt 0&$select=number,displayName,balance&$top=50&$orderby=balance desc
?$filter=status eq 'Open'&$expand=salesInvoiceLines&$orderby=dueDate asc
?$filter=lastModifiedDateTime gt 2024-01-01T00:00:00Z&$top=100&$select=id,number,displayName
```

---

## Common Headers

| Header            | Value                                    | When to Use                         |
|-------------------|------------------------------------------|-------------------------------------|
| `Authorization`   | `Bearer {access_token}`                  | Every request                       |
| `Content-Type`    | `application/json`                       | POST and PATCH requests             |
| `Accept`          | `application/json`                       | All requests (optional but recommended) |
| `If-Match`        | `{@odata.etag value}`                    | PATCH and DELETE requests           |
| `Accept-Language` | `en-US`                                  | Override response language           |
| `Prefer`          | `odata.maxpagesize=50`                   | Override default page size           |
| `Prefer`          | `return=representation`                  | Return updated entity on PATCH       |

---

## Quick Links

- [README — Full Setup Guide](../README.md)
- [How to Use with AI Tools](../HOW_TO_USE_WITH_AI.md)
- [Customers](customers/customers.md)
- [Vendors](vendors/vendors.md)
- [Items](items/items.md)
- [Sales Invoices](sales/sales-invoices.md)
- [Purchase Orders](purchase/purchase-orders.md)
- [Journals](finance/journals.md)
- [Webhooks](webhooks/subscriptions.md)
- [Automation Overview](automation/_overview.md)
