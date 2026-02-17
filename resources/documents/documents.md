# Documents — Attachments, PDFs & Pictures

Business Central provides several document-related sub-resources that allow you to manage file attachments, generate PDFs, and handle entity pictures. These are available as sub-resources on many parent entities.

---

## Attachments

Attachments represent incoming documents linked to records. Available as both a top-level endpoint and as sub-resources.

### Top-Level Endpoint

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/attachments
```

### As Sub-Resource

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesInvoices({id})/attachments
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/purchaseInvoices({id})/attachments
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/journals({jId})/journalLines({jlId})/attachments
```

### List Attachments

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesInvoices({id})/attachments
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "value": [
    {
      "id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
      "parentId": "d1e2f3a4-b5c6-7890-1234-567890abcdef",
      "fileName": "invoice-backup.pdf",
      "byteSize": 245678,
      "parentType": "Sales Invoice",
      "lastModifiedDateTime": "2024-06-15T10:00:00Z"
    }
  ]
}
```

### Create an Attachment

```http
POST {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesInvoices({id})/attachments
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "fileName": "receipt.pdf"
}
```

Then upload the content:

```http
PATCH {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesInvoices({id})/attachments({attachmentId})/attachmentContent
Authorization: Bearer {access_token}
Content-Type: application/octet-stream
If-Match: {etag}

<binary file content>
```

### Field Definitions

| Field                    | Type     | Editable | Description                                    |
|--------------------------|----------|----------|------------------------------------------------|
| `id`                     | GUID     | No       | Unique identifier                              |
| `parentId`               | GUID     | No       | Parent entity ID                               |
| `fileName`               | string   | Yes      | File name                                      |
| `byteSize`               | integer  | No       | File size in bytes                             |
| `attachmentContent`      | stream   | Yes      | Binary content (upload via PATCH)               |
| `parentType`             | enum     | No       | Parent type: `Journal`, `Sales Order`, `Sales Quote`, `Sales Credit Memo`, `Sales Invoice`, `Purchase Invoice` |
| `lastModifiedDateTime`   | datetime | No       | Last modified timestamp                        |

---

## Document Attachments

A broader attachment type available on many more entities (customers, vendors, items, employees, projects, and all sales/purchase documents).

### Endpoints (examples)

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/customers({id})/documentAttachments
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/vendors({id})/documentAttachments
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/items({id})/documentAttachments
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/employees({id})/documentAttachments
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/projects({id})/documentAttachments
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesInvoices({id})/documentAttachments
```

### Field Definitions

| Field                    | Type     | Editable | Description                           |
|--------------------------|----------|----------|---------------------------------------|
| `id`                     | GUID     | No       | Unique identifier                     |
| `fileName`               | string   | Yes      | File name                             |
| `byteSize`               | integer  | No       | File size in bytes                    |
| `attachmentContent`      | stream   | Yes      | Binary content                        |
| `parentType`             | enum     | No       | Parent entity type                    |
| `parentId`               | GUID     | No       | Parent entity ID                      |
| `lineNumber`             | integer  | No       | Line number                           |
| `documentFlowSales`      | boolean  | Yes      | Linked to sales flow                  |
| `documentFlowPurchase`   | boolean  | Yes      | Linked to purchase flow               |
| `lastModifiedDateTime`   | datetime | No       | Last modified timestamp               |

---

## PDF Document

Generate PDF versions of sales and purchase documents.

### Available On

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesInvoices({id})/pdfDocument
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesQuotes({id})/pdfDocument
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesOrders({id})/pdfDocument
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesCreditMemos({id})/pdfDocument
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/purchaseInvoices({id})/pdfDocument
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/purchaseCreditMemos({id})/pdfDocument
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/projects({id})/pdfDocument
```

### Get PDF Document Metadata

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesInvoices({id})/pdfDocument
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "value": [
    {
      "id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
      "parentId": "d1e2f3a4-b5c6-7890-1234-567890abcdef",
      "parentType": "Sales Invoice"
    }
  ]
}
```

### Download PDF Content

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/salesInvoices({id})/pdfDocument({pdfId})/pdfDocumentContent
Authorization: Bearer {access_token}
Accept: application/pdf
```

**Response:** Binary PDF content with `Content-Type: application/pdf`.

### Field Definitions

| Field               | Type     | Description                              |
|---------------------|----------|------------------------------------------|
| `id`                | GUID     | Unique identifier                        |
| `parentId`          | GUID     | Parent document ID                       |
| `parentType`        | enum     | `Sales Invoice`, `Sales Order`, `Sales Quote`, `Sales Credit Memo`, `Purchase Invoice`, `Purchase Credit Memo` |
| `pdfDocumentContent`| stream   | Binary PDF content                       |

---

## Pictures

Pictures are image resources available on entity records (customers, vendors, items, employees, contacts).

### Available On

```
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/customers({id})/picture
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/vendors({id})/picture
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/items({id})/picture
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/employees({id})/picture
{{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/contacts({id})/picture
```

### Get Picture Metadata

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/customers({id})/picture
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "value": [
    {
      "id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
      "parentType": "Customer",
      "width": 200,
      "height": 200,
      "contentType": "image/jpeg"
    }
  ]
}
```

### Download Picture

```http
GET {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/customers({id})/picture({picId})/pictureContent
Authorization: Bearer {access_token}
Accept: image/jpeg
```

### Upload / Replace Picture

```http
PATCH {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/customers({id})/picture({picId})/pictureContent
Authorization: Bearer {access_token}
Content-Type: application/octet-stream
If-Match: {etag}

<binary image content>
```

### Delete Picture

```http
DELETE {{BC_BASE_URL}}/companies(name='{{BC_COMPANY_NAME}}')/customers({id})/picture({picId})
Authorization: Bearer {access_token}
If-Match: {etag}
```

### Field Definitions

| Field           | Type     | Description                              |
|-----------------|----------|------------------------------------------|
| `id`            | GUID     | Unique identifier                        |
| `parentType`    | enum     | `Customer`, `Item`, `Vendor`, `Employee` |
| `width`         | integer  | Image width in pixels                    |
| `height`        | integer  | Image height in pixels                   |
| `contentType`   | string   | MIME type (e.g., `image/jpeg`)           |
| `pictureContent`| stream   | Binary image content                     |

---

## Important Notes

- Use `PATCH` with `Content-Type: application/octet-stream` to upload binary content (attachments, pictures)
- There is no `POST` for pictures — first GET the picture metadata, then PATCH the content
- PDF generation may take a moment for complex documents — the API handles this transparently
- `documentAttachments` is the most broadly available attachment type — prefer it over the older `attachments` endpoint
