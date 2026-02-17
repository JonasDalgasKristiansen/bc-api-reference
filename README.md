# Business Central API Reference

> A complete API reference for Microsoft Dynamics 365 Business Central — designed for AI coding tools like **Lovable**, **Bolt**, **Cursor**, and **GitHub Copilot**.

Connect this repo to your AI tool, add your credentials, and it can build fully working Business Central integrations for you — no manual coding needed.

---

## What Is This?

This repository contains detailed documentation for every Business Central API endpoint — customers, invoices, items, time tracking, and more. It's structured so AI tools can read it and generate correct, working code.

**You don't need to understand the technical details.** The AI reads the documentation and handles everything. You just need to:

1. Set up a connection between BC and the API (one-time, ~10 minutes)
2. Add your credentials to Lovable/Bolt
3. Tell the AI what you want to build

---

## Setup Guide (One-Time)

### Step 1: Create an App Registration in Azure

This gives your app permission to talk to Business Central.

1. Go to [portal.azure.com](https://portal.azure.com) and sign in with your work account
2. Search for **"App registrations"** in the top search bar and click it
3. Click **+ New registration**
4. Fill in:
   - **Name:** `BC API Integration` (or any name you like)
   - **Supported account types:** Select `Accounts in this organizational directory only`
   - Leave **Redirect URI** blank
5. Click **Register**
6. You'll see a summary page — **save these two values:**
   - **Application (client) ID** → this is your `BC_CLIENT_ID`
   - **Directory (tenant) ID** → this is your `BC_TENANT_ID`

### Step 2: Create a Secret

1. On the same app page, click **Certificates & secrets** in the left menu
2. Click **+ New client secret**
3. Enter a description (e.g., `BC Secret`) and pick an expiry (e.g., 12 months)
4. Click **Add**
5. **Copy the Value immediately** (it won't be shown again) → this is your `BC_CLIENT_SECRET`

### Step 3: Give the App Permission to Access BC

1. On the same app page, click **API permissions** in the left menu
2. Click **+ Add a permission**
3. Select **Dynamics 365 Business Central**
4. Click **Application permissions**
5. Check **Financials.ReadWrite.All**
6. Click **Add permissions**
7. Click **Grant admin consent** (you may need your IT admin to do this)
8. Confirm the green checkmark appears

### Step 4: Register the App Inside Business Central

1. Open **Business Central** in your browser
2. In the search bar at the top, type **Microsoft Entra Applications** and open it
3. Click **+ New**
4. Paste your **Application (client) ID** from Step 1 into the **Client ID** field
5. Enter a description (e.g., `API Integration App`)
6. Set **State** to **Enabled**
7. Under **User Permission Sets**, add: `D365 FULL ACCESS`
8. Close the page

### Step 5: Find Your Company Name

1. In Business Central, click the **Settings** gear icon (top right)
2. Click **Company Information**
3. The **Name** field is your `BC_COMPANY_NAME` (e.g., `CRONUS Danmark A/S`)

### Step 6: Find Your Environment Name

1. Look at your Business Central URL in the browser
2. It looks like: `https://businesscentral.dynamics.com/your-tenant/YourEnvironment/...`
3. The part after your tenant ID is your environment name — usually `Production` or `Sandbox`

---

## Add Your Credentials to Lovable / Bolt

Now add the 5 values you collected as **Secrets** in your AI tool:

### In Lovable

Go to **Settings → Secrets** and add each one:

| Secret Name         | What to Paste                                  |
|---------------------|------------------------------------------------|
| `BC_TENANT_ID`      | The Directory (tenant) ID from Step 1          |
| `BC_CLIENT_ID`      | The Application (client) ID from Step 1        |
| `BC_CLIENT_SECRET`  | The secret Value from Step 2                   |
| `BC_ENVIRONMENT`    | Your environment name from Step 6 (e.g., `Sandbox`) |
| `BC_COMPANY_NAME`   | Your company name from Step 5 (e.g., `CRONUS Danmark A/S`) |

### In Bolt

Go to **Settings → Environment Variables** and add the same 5 values.

---

## How to Prompt the AI

Once your credentials are saved, you can start building. Structure your prompts like the examples below — describe the app you want, then add the important instructions block at the end.

### Example Prompts

**Time Registration App:**
```
## App
Create a time registration app that connects to Business Central.
Main page should have:
- Choose employee
- Choose project
- Select date
- Enter hours

## Important
- Read AI_INSTRUCTIONS.md first
- If in doubt, always re-read the GitHub repo
- Enable Lovable Cloud
- Add TenantID, ClientID, ClientSecret, Environment & Company Name as Secrets
- Add good error logging

## GitHub Repo
github.com/JonasDalgasKristiansen/bc-api-reference
```

**Customer Overview:**
```
## App
Build a customer overview app connected to Business Central.
- Show a searchable list of all customers with name, balance, and phone number
- Click a customer to see their open invoices
- Show total outstanding amount at the top

## Important
- Read AI_INSTRUCTIONS.md first
- If in doubt, always re-read the GitHub repo
- Enable Lovable Cloud
- Add TenantID, ClientID, ClientSecret, Environment & Company Name as Secrets
- Add good error logging

## GitHub Repo
github.com/JonasDalgasKristiansen/bc-api-reference
```

**Sales Dashboard:**
```
## App
Create a sales dashboard that shows:
- Total open sales invoices (count and amount)
- List of recent sales orders
- A button to post draft invoices
- A chart showing invoiced amounts per month

## Important
- Read AI_INSTRUCTIONS.md first
- If in doubt, always re-read the GitHub repo
- Enable Lovable Cloud
- Add TenantID, ClientID, ClientSecret, Environment & Company Name as Secrets
- Add good error logging

## GitHub Repo
github.com/JonasDalgasKristiansen/bc-api-reference
```

> **Tip:** Always keep the `## Important` and `## GitHub Repo` sections at the bottom of every prompt. The AI needs these to find the documentation and use real data instead of fake demos.

---

## Troubleshooting

| Problem | Likely Cause | Fix |
|---------|-------------|-----|
| AI builds a demo app with fake data | It didn't read the instructions | Add `"Read AI_INSTRUCTIONS.md first"` to your prompt |
| "Unauthorized" or "401" error | Credentials are wrong or missing | Double-check all 5 secrets in Settings → Secrets |
| "404 Not Found" error | Wrong company name or environment name | Verify `BC_COMPANY_NAME` matches exactly (case-sensitive) |
| "The Employee does not exist" | AI used wrong field for time entries | This is fixed in the repo — tell Lovable to re-read the time tracking docs |
| AI asks for a company GUID | It's reading old patterns | Tell it: "Use company name, not GUID: `companies(name='...')`" |

---

## What's in This Repo

| File / Folder | What It Contains | Who It's For |
|---------------|-----------------|--------------|
| `AI_INSTRUCTIONS.md` | Rules the AI must follow (credentials, architecture, field names) | The AI tool |
| `HOW_TO_USE_WITH_AI.md` | Detailed prompting guide and tips | You (for better prompts) |
| `DEVELOPER_GUIDE.md` | Full technical documentation (auth, tokens, OData, pagination, etc.) | Developers |
| `.env.example` | List of required credential variable names | Reference |
| `resources/_overview.md` | Master list of all 80+ API endpoints | The AI tool |
| `resources/` folder | Detailed docs for every API endpoint (fields, examples, errors) | The AI tool |

---

## License

MIT — see [LICENSE](LICENSE) for details.
