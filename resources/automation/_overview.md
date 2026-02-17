# Automation API — Overview

The Automation API provides endpoints for managing Business Central tenants programmatically, including companies, users, extensions, and permission sets. It uses a different base URL than the standard API.

---

## Base URL

```
{{BC_AUTOMATION_URL}}
```

Which resolves to:

```
https://api.businesscentral.dynamics.com/v2.0/{tenant}/{environment}/api/microsoft/automation/v2.0
```

---

## Authentication

The Automation API uses the same OAuth2 Client Credentials flow as the standard API:

```http
POST https://login.microsoftonline.com/{{BC_TENANT_ID}}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

client_id={{BC_CLIENT_ID}}
&client_secret={{BC_CLIENT_SECRET}}
&scope=https://api.businesscentral.dynamics.com/.default
&grant_type=client_credentials
```

---

## Available Resources

| Resource                  | Endpoint                                                      | Description                              |
|---------------------------|---------------------------------------------------------------|------------------------------------------|
| [Companies](companies.md) | `{{BC_AUTOMATION_URL}}/companies`                           | List and manage companies                |
| [Users](users.md)         | `{{BC_AUTOMATION_URL}}/companies({id})/users`               | Manage users and permissions             |
| [Extensions](extensions.md)| `{{BC_AUTOMATION_URL}}/companies({id})/extensions`         | Manage installed extensions              |
| [Environments](environments.md)| Admin Center API (separate)                             | Manage environments (sandbox/production) |

---

## Key Differences from Standard API

| Aspect              | Standard API                          | Automation API                              |
|---------------------|---------------------------------------|---------------------------------------------|
| Base path           | `.../api/v2.0`                        | `.../api/microsoft/automation/v2.0`         |
| Purpose             | Business data (customers, invoices)   | Tenant administration                       |
| Company context     | Required for most endpoints           | Companies are a top-level resource          |
| Bound actions       | Business actions (post, send)         | Admin actions (invite, install, uninstall)  |

---

## Important Notes

- The Automation API requires the **D365 AUTOMATION** permission set assigned to your app registration in BC
- Some operations (like installing extensions) are asynchronous — the API returns immediately with an operation status
- The Admin Center API for environments uses a completely different base URL: `https://api.businesscentral.dynamics.com/admin/v2.28`
