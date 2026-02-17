# Webhook Subscriptions

Webhook subscriptions allow your application to receive real-time notifications when data changes in Business Central. When a subscribed resource is created, updated, or deleted, BC sends a POST request to your callback URL with the change notification.

---

## Base Endpoint

```
{{BC_BASE_URL}}/subscriptions
```

> **Note:** Subscriptions are registered at the API root level, not under a specific company.

---

## How It Works

1. **Register** a subscription pointing to your callback URL
2. **Handshake**: BC sends a validation request to your callback URL — your server must echo back the `validationToken` query parameter
3. **Receive notifications**: BC sends POST requests to your callback URL when changes occur
4. **Renew** subscriptions before they expire (default 3 days)

---

## Operations

### 1. List All Subscriptions

```http
GET {{BC_BASE_URL}}/subscriptions
Authorization: Bearer {access_token}
Accept: application/json
```

**Response:**

```json
{
  "value": [
    {
      "@odata.etag": "W/\"JzE5O1NVQjEn\"",
      "subscriptionId": "a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa",
      "notificationUrl": "https://myapp.example.com/api/bc-webhooks",
      "resource": "companies({{BC_COMPANY_ID}})/customers",
      "userId": "00000000-0000-0000-0000-000000000000",
      "lastModifiedDateTime": "2025-02-17T10:00:00Z",
      "clientState": "my-secret-state-value",
      "expirationDateTime": "2025-02-20T10:00:00Z",
      "systemCreatedAt": "2025-02-17T10:00:00Z",
      "systemCreatedBy": "00000000-0000-0000-0000-000000000000"
    }
  ]
}
```

---

### 2. Get a Single Subscription

```http
GET {{BC_BASE_URL}}/subscriptions('a1a1a1a1-1111-1111-1111-aaaaaaaaaaaa')
Authorization: Bearer {access_token}
```

---

### 3. Create a Subscription

```http
POST {{BC_BASE_URL}}/subscriptions
Authorization: Bearer {access_token}
Content-Type: application/json
```

**Request Body:**

```json
{
  "notificationUrl": "https://myapp.example.com/api/bc-webhooks",
  "resource": "companies({{BC_COMPANY_ID}})/customers",
  "clientState": "my-secret-state-value"
}
```

**Response (201 Created):**

```json
{
  "@odata.etag": "W/\"JzIwO1NVQjIn\"",
  "subscriptionId": "b2b2b2b2-2222-2222-2222-bbbbbbbbbbbb",
  "notificationUrl": "https://myapp.example.com/api/bc-webhooks",
  "resource": "companies({{BC_COMPANY_ID}})/customers",
  "userId": "00000000-0000-0000-0000-000000000000",
  "lastModifiedDateTime": "2025-02-17T10:30:00Z",
  "clientState": "my-secret-state-value",
  "expirationDateTime": "2025-02-20T10:30:00Z",
  "systemCreatedAt": "2025-02-17T10:30:00Z",
  "systemCreatedBy": "00000000-0000-0000-0000-000000000000"
}
```

> **Important:** Immediately after creation, BC sends a validation request to your `notificationUrl`. Your server must respond correctly for the subscription to become active.

---

### 4. Renew (Update) a Subscription

```http
PATCH {{BC_BASE_URL}}/subscriptions('b2b2b2b2-2222-2222-2222-bbbbbbbbbbbb')
Authorization: Bearer {access_token}
Content-Type: application/json
If-Match: W/"JzIwO1NVQjIn"
```

```json
{
  "expirationDateTime": "2025-02-23T10:30:00Z"
}
```

> **Note:** Subscriptions expire after 3 days by default. You must renew them regularly (recommended: every 24 hours) to keep receiving notifications.

---

### 5. Delete a Subscription

```http
DELETE {{BC_BASE_URL}}/subscriptions('b2b2b2b2-2222-2222-2222-bbbbbbbbbbbb')
Authorization: Bearer {access_token}
If-Match: W/"JzIwO1NVQjIn"
```

**Response:** `204 No Content`

---

## Subscription Fields

| Field                    | Type     | Writable | Description                                                |
|--------------------------|----------|----------|------------------------------------------------------------|
| `subscriptionId`         | string   | No       | Unique subscription identifier                             |
| `notificationUrl`        | string   | Yes      | Your callback URL (must be HTTPS)                          |
| `resource`               | string   | Yes      | The API resource path to monitor                           |
| `userId`                 | GUID     | No       | The user who created the subscription                      |
| `lastModifiedDateTime`   | datetime | No       | Last modification timestamp                                |
| `clientState`            | string   | Yes      | Secret value echoed in notifications for verification      |
| `expirationDateTime`     | datetime | Yes      | When the subscription expires (max 3 days from creation)   |
| `systemCreatedAt`        | datetime | No       | Creation timestamp                                         |
| `systemCreatedBy`        | GUID     | No       | Created-by user ID                                         |

---

## Handshake Validation

When you create a subscription, BC sends a validation request to your `notificationUrl`:

```http
POST https://myapp.example.com/api/bc-webhooks?validationToken=abc123xyz
Content-Length: 0
```

**Your server must respond:**

```
HTTP/1.1 200 OK
Content-Type: text/plain

abc123xyz
```

Requirements:
- Return HTTP 200
- Return the `validationToken` query parameter value as the response body
- Content-Type should be `text/plain`
- Must respond within 5 seconds

If validation fails, the subscription is NOT created.

---

## Notification Payload

When a change occurs, BC sends a POST to your `notificationUrl`:

```http
POST https://myapp.example.com/api/bc-webhooks
Content-Type: application/json
```

```json
{
  "value": [
    {
      "subscriptionId": "b2b2b2b2-2222-2222-2222-bbbbbbbbbbbb",
      "clientState": "my-secret-state-value",
      "expirationDateTime": "2025-02-20T10:30:00Z",
      "resource": "companies({{BC_COMPANY_ID}})/customers(c1d2e3f4-a5b6-7890-cdef-999999999999)",
      "changeType": "updated",
      "lastModifiedDateTime": "2025-02-17T14:30:00Z"
    }
  ]
}
```

### Change Types

| changeType  | Description                      |
|-------------|----------------------------------|
| `created`   | A new record was created         |
| `updated`   | An existing record was modified  |
| `deleted`   | A record was deleted             |
| `collection`| Multiple changes in batch        |

---

## Handling Notifications

Your webhook handler should:

1. **Verify `clientState`** — compare the received value against your stored secret
2. **Respond with 200 OK** immediately (within 30 seconds)
3. **Process asynchronously** — fetch the changed record using the `resource` path if you need the latest data
4. **Handle duplicates** — notifications may be sent more than once

### Example Handler (pseudocode)

```python
@app.route('/api/bc-webhooks', methods=['POST', 'GET'])
def bc_webhook():
    # Handle handshake validation
    validation_token = request.args.get('validationToken')
    if validation_token:
        return Response(validation_token, content_type='text/plain')

    # Handle notification
    payload = request.json
    for notification in payload.get('value', []):
        if notification['clientState'] != MY_SECRET:
            return Response(status=403)

        # Queue async processing
        process_change.delay(
            resource=notification['resource'],
            change_type=notification['changeType']
        )

    return Response(status=200)
```

---

## Supported Resources

You can create subscriptions for these resources:

| Resource                     | Example `resource` value                              |
|------------------------------|-------------------------------------------------------|
| Customers                    | `companies({id})/customers`                           |
| Vendors                      | `companies({id})/vendors`                             |
| Items                        | `companies({id})/items`                               |
| Sales Invoices               | `companies({id})/salesInvoices`                       |
| Sales Orders                 | `companies({id})/salesOrders`                         |
| Sales Quotes                 | `companies({id})/salesQuotes`                         |
| Sales Credit Memos           | `companies({id})/salesCreditMemos`                    |
| Purchase Invoices            | `companies({id})/purchaseInvoices`                    |
| Purchase Orders              | `companies({id})/purchaseOrders`                      |
| General Ledger Entries       | `companies({id})/generalLedgerEntries`                |
| Journals                     | `companies({id})/journals`                            |

---

## Renewal Strategy

Subscriptions expire after **3 days** (72 hours). Implement a renewal strategy:

```
Recommended approach:
1. Store subscription ID and expiration in your database
2. Run a scheduled job (cron/timer) every 12-24 hours
3. PATCH the subscription to extend the expiration
4. If renewal fails, re-create the subscription
```

```http
# Renew: set new expiration 3 days from now
PATCH {{BC_BASE_URL}}/subscriptions('{subscriptionId}')
Authorization: Bearer {access_token}
Content-Type: application/json
If-Match: *
```

```json
{
  "expirationDateTime": "2025-02-23T10:00:00Z"
}
```

---

## Important Notes

- `notificationUrl` must be **HTTPS** with a valid SSL certificate
- Notifications contain only the resource path and change type — **not the full record data**. Fetch the record separately if needed.
- If your endpoint returns an error status multiple consecutive times, BC will disable the subscription
- Multiple changes may be batched into a single notification payload
- The `clientState` is a shared secret between you and BC — always verify it

---

## Common Errors

| Status | Error                  | Cause & Resolution                                                                 |
|--------|------------------------|------------------------------------------------------------------------------------|
| `401`  | Unauthorized           | Access token is missing, expired, or invalid.                                      |
| `404`  | Not Found              | Subscription ID does not exist or has expired.                                     |
| `400`  | Bad Request            | Invalid resource path or notificationUrl is not HTTPS.                             |
| `409`  | Conflict               | ETag mismatch. Re-fetch and retry.                                                 |
| `422`  | Validation Failed      | Handshake validation failed — your server did not respond correctly.                |
