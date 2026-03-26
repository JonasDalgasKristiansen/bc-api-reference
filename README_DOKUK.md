# Dokumentation — BC API Reference Repo

> Denne fil forklarer hvad dette repository indeholder, hvorfor det eksisterer, og hvordan det bruges med AI vibe-coding værktøjer som **Lovable**, **Bolt**, **Cursor** og **GitHub Copilot**.

---

## Problemet

AI-kodningsværktøjer som Lovable kan bygge fulde webapplikationer på minutter — men de kender ikke Business Central's API. Uden korrekt dokumentation vil AI'en:

- Bygge demo-apps med falsk/hardcoded data
- Bruge forkerte feltnavne (f.eks. `description` i stedet for `displayName` på Items)
- Mangle autentificering helt
- Sende forkerte API-kald der returnerer 400/404 fejl
- Gætte på endpoint-strukturer i stedet for at bruge de rigtige

**Løsningen:** Vi giver AI'en et komplet API-referencebibliotek som den kan læse som kontekst — så den genererer korrekt, fungerende kode fra start.

---

## Hvad Er Dette Repo?

Et struktureret dokumentationsbibliotek der dækker **hele** Microsoft Dynamics 365 Business Central REST API v2.0. Det er skrevet så AI-værktøjer kan læse det og generere kode der virker mod den rigtige API — uden at en udvikler behøver at skrive noget manuelt.

### Nøgletal

| Hvad | Antal |
|------|-------|
| API endpoints dokumenteret | 80+ |
| Ressource-kategorier | 15 (Kunder, Varer, Salg, Køb, Finans, m.fl.) |
| Understøttede operationer | GET, POST, PATCH, DELETE + bound actions |
| Eksempel-forespørgsler per endpoint | 3–10 (inkl. OData $filter, $select, $expand) |
| Fejlhåndtering dokumenteret | Ja — per endpoint |

---

## Repo-Struktur

```
bc-api-reference/
│
├── README.md                    # Hurtig-guide til slutbrugere (opsætning + prompts)
├── README_DOKUK.md              # Denne fil — fuld dokumentation af repoet
├── AI_INSTRUCTIONS.md           # Regler som AI-værktøjet SKAL følge
├── HOW_TO_USE_WITH_AI.md        # Detaljeret guide til prompting med forskellige AI-værktøjer
├── DEVELOPER_GUIDE.md           # Teknisk reference for udviklere (auth, OData, pagination)
├── .env.example                 # Liste over påkrævede miljøvariabler
│
└── resources/                   # Selve API-dokumentationen
    ├── _overview.md             # Master-oversigt over ALLE endpoints + OData cheat sheet
    │
    ├── customers/               # Kunder, betalingsjournaler
    ├── vendors/                 # Leverandører, betalingsjournaler
    ├── contacts/                # Kontaktpersoner, sælgere/indkøbere
    ├── items/                   # Varer, varianter, kategorier, lagerbeholdning, lokationer
    ├── sales/                   # Tilbud, ordrer, fakturaer, kreditnotaer, forsendelser
    ├── purchase/                # Indkøbsordrer, fakturaer, kreditnotaer, modtagelser
    ├── finance/                 # Kontoplan, bankkonti, journaler, finansposter, rapporter
    ├── employees/               # Medarbejdere
    ├── projects/                # Projekter (sager), sagsopgaver
    ├── time-tracking/           # Tidsregistrering
    ├── fixed-assets/            # Anlægsaktiver
    ├── documents/               # Vedhæftninger, PDF-dokumenter
    ├── dimensions/              # Dimensioner, dimensionssætlinjer
    ├── setup/                   # Valutaer, betalingsbetingelser, enheder, lande, moms
    ├── company/                 # Virksomhedsoplysninger
    ├── automation/              # Automatiserings-API (brugere, extensions, miljøer)
    ├── webhooks/                # Webhook-abonnementer
    └── pos-system/              # Komplet POS-system guide (offline-first, synk, eksport)
```

---

## Hvad Hver Fil Gør

| Fil | Målgruppe | Formål |
|-----|-----------|--------|
| **AI_INSTRUCTIONS.md** | AI-værktøjet | 8 regler AI'en skal følge: brug rigtige API-kald, bed om credentials, brug korrekt auth-flow, brug præcise feltnavne, håndtér pagination, osv. |
| **HOW_TO_USE_WITH_AI.md** | Brugeren | Step-by-step guide til at forbinde repoet med Lovable, Bolt, Cursor eller Copilot — inkl. prompt-eksempler |
| **DEVELOPER_GUIDE.md** | Udviklere | Fuld teknisk reference: OAuth2 flow, token-caching, OData queries, pagination, fejlhåndtering, rate limits |
| **resources/_overview.md** | AI + Udviklere | Master-tabel over alle 80+ endpoints med HTTP-metoder, URL-paths, og bound actions |
| **resources/\*/\*.md** | AI-værktøjet | Detaljeret dokumentation per endpoint — feltdefinitioner med datatyper, fulde HTTP-eksempler, JSON payloads, OData mønstre, kendte fejl |
| **resources/pos-system/** | AI-værktøjet | Komplet arkitekturguide til et offline-first POS system med BC-synkronisering |

---

## Hvorfor Give Det Til Lovable?

### Uden dette repo

AI'en har ingen viden om Business Central's API. Resultatet:

1. Den bygger en app med **falsk data** og placeholder-endpoints
2. Eller den gætter på API-strukturen og laver **forkerte kald** (`description` i stedet for `displayName`, `balance` i `$select` som giver 400-fejl)
3. Den mangler **autentificering** eller bruger forkert auth-flow
4. Du bruger timer på at debugge og rette

### Med dette repo

AI'en læser dokumentationen og genererer **korrekt, fungerende kode fra første forsøg**:

1. Korrekt OAuth2 Client Credentials flow med token-caching
2. Rigtige feltnavne og datatyper på alle API-kald
3. Korrekt OData $filter, $select og $expand syntax
4. Korrekt håndtering af company GUID-opslag
5. Fejlhåndtering der matcher de faktiske fejlkoder fra BC
6. Credentials læst fra miljøvariabler — aldrig hardcoded

### Tidsbesparelse

| Opgave | Uden repo | Med repo |
|--------|-----------|----------|
| Simpel kunde-oversigt | Timer (debugging af forkerte feltnavne) | Minutter |
| Salgs-dashboard med fakturaer | Dage | Under en time |
| Komplet POS-system med offline-synk | Uger af udvikling | Bygget i én session |

---

## Eksempel: POS-System Bygget Med Lovable

For at demonstrere styrken ved dette repo har vi bygget et **komplet POS-system** med Business Central synkronisering — udelukkende via en prompt til Lovable.

### Hvad POS-systemet kan

- **Offline-first**: Hele kassen virker uden forbindelse til Business Central
- **Lokal dataspejling**: Alle varer, kunder, priser og moms importeres fra BC og gemmes lokalt
- **Touch-venligt POS UI**: Produktgitter, søgning, stregkodescanning, indkøbskurv, betaling
- **Kundevalg ved checkout**: Søg i lokale kunder, opret walk-in kunde direkte i BC, eller fortsæt anonymt
- **Kvittering**: On-screen kvittering efter hvert salg med print-funktion
- **Eksport til BC**: Ét klik eksporterer alle usynkede salg som salgsfakturaer i Business Central
- **Idempotent synk**: `externalDocumentNumber` forhindrer duplikerede poster ved geneksport
- **Admin dashboard**: Forbindelsesstatus, synkroniseringslog, tvunget synk

### Prompten vi brugte

```
Full POS System with Business Central Sync

Build a full web-based POS system that syncs with Microsoft Business Central.

The app must:
- Import all products, prices, customers, taxes, and inventory from Business Central
- Store all imported data locally in the backend
- Work fully offline — no live BC calls during checkout
- Save all sales transactions in the backend database
- Include an "Export to Business Central" button to sync unsynced sales once BC is online again

Requirements:
- Modern POS UI (touch-friendly, barcode scan, product search, cart, payments, receipts)
- Backend with local database (store BC mirror + offline sales)
- Scheduled sync from BC → local database
- Track sync status per transaction
- Export unsynced sales as Sales Invoices — post and email automatically.
  See resources/pos-system/pos-system.md → Export Flow
- Prevent duplicate exports (idempotent sync logic using externalDocumentNumber)
- Decrement local inventory immediately at checkout. Re-sync inventory from BC
  after each export. See resources/pos-system/pos-system.md → Inventory Sync
- When the cashier presses Checkout, open a two-step wizard.
  Step 1 — Customer — presents three options:
    (1) search and select an existing customer from the local DB (no BC call),
    (2) Walk-in Customer — opens a quick-create wizard that POSTs to BC,
    (3) Continue Anonymous — no customer attached.
  Step 2 is Payment.
  See resources/pos-system/pos-system.md → Checkout: Customer Selection & Creation
- Cash payment button at checkout with on-screen receipt.
  See resources/pos-system/pos-system.md → Checkout: Cash Payment & Receipt
- Admin dashboard with: BC connection status, last sync timestamp, error logs,
  force sync button

Important:
- Read AI_INSTRUCTIONS.md first — follow all rules
- Read resources/pos-system/pos-system.md for exact BC queries and architecture
- Credentials stored as Lovable Secrets (BC_TENANT_ID, BC_CLIENT_ID,
  BC_CLIENT_SECRET, BC_ENVIRONMENT, BC_COMPANY_NAME)

API Reference: github.com/JonasDalgasKristiansen/bc-api-reference
```

### Arkitektur

```
┌─────────────────────┐      ┌──────────────────┐      ┌────────────────────┐
│   React Frontend    │◄────►│  Express Backend  │◄────►│ Business Central   │
│   (POS Terminal)    │      │  (SQLite DB)      │      │ (REST API v2.0)    │
│                     │      │                   │      │                    │
│  - Produktgitter    │      │  - Lokalt spejl   │      │  - Varer           │
│  - Kurv / checkout  │      │  - Offline salg   │      │  - Kunder          │
│  - Kundesøgning     │      │  - Synk-motor     │      │  - Salgsfakturaer  │
│  - Kvitteringsview  │      │  - Cron scheduler │      │  - Moms / Betaling │
│  - Admin dashboard  │      │  - Eksport-kø     │      │  - Lagerbeholdning │
└─────────────────────┘      └──────────────────┘      └────────────────────┘
```

### Hvorfor det virker

AI'en kunne bygge alt dette fordi repoet indeholder:

1. **Præcise feltnavne** — AI'en bruger `displayName` på items, ikke `description` (som ville give en 400-fejl)
2. **Komplette import-queries** — Eksakte `$select`-parametre der virker, inkl. kendte fælder der IKKE må bruges (`balance`, `overdueAmount`)
3. **Eksport-flow dokumentation** — Trin-for-trin guide til at oprette salgsfakturaer, poste dem og sende email
4. **Inventory-synk logik** — Hvornår og hvordan lagerbeholdning opdateres lokalt vs. fra BC
5. **Checkout-flow specifikation** — Kundevalg, walk-in oprettelse, anonymt salg, betalingsflow
6. **Idempotent synk-mønster** — Brug af `externalDocumentNumber` til at forhindre duplikater

---

## Dækkede API-Områder

| Kategori | Endpoints | Hvad du kan bygge |
|----------|-----------|-------------------|
| **Kunder** | customers, customerFinancialDetails, customerPaymentJournals | CRM, kundeoversigter, betalingsregistrering |
| **Leverandører** | vendors, vendorPaymentJournals | Leverandørstyring, indkøbsflow |
| **Varer** | items, itemVariants, itemCategories, itemInventory, locations | Produktkataloger, lagerstyring, POS |
| **Salg** | salesQuotes, salesOrders, salesInvoices, salesCreditMemos, salesShipments | Salgsdashboards, ordrestyring, fakturering |
| **Køb** | purchaseOrders, purchaseInvoices, purchaseCreditMemos, purchaseReceipts | Indkøbsstyring, modtagelser |
| **Finans** | accounts, journals, generalLedgerEntries, bankAccounts, financialReports | Regnskabsoversigter, bogføring, rapportering |
| **Medarbejdere** | employees | HR-oversigter, medarbejderregistrering |
| **Projekter** | projects, jobTasks | Projektstyring, sagsregistrering |
| **Tidsregistrering** | timeRegistrationEntries | Tidsregistreringsapps |
| **Anlægsaktiver** | fixedAssets | Anlægskartotek |
| **Dokumenter** | attachments, pdfDocument | Dokumenthåndtering, PDF-download |
| **Setup** | currencies, paymentTerms, paymentMethods, unitsOfMeasure, taxAreas, m.fl. | Opsætningsdata, stamdata |
| **Webhooks** | subscriptions | Realtids-notifikationer fra BC |
| **Automation** | users, extensions, environments | Administration, deployments |

---

## Hvem Er Repoet Til?

| Rolle | Hvad de bruger |
|-------|---------------|
| **Forretningsbrugere** | README.md → opsætning + prompt-eksempler. Ingen kode nødvendig. |
| **AI-værktøjer** | AI_INSTRUCTIONS.md + resources/ → genererer korrekt kode automatisk |
| **Udviklere** | DEVELOPER_GUIDE.md → teknisk reference hvis man bygger manuelt |
| **Præsentationer** | README_DOKUK.md (denne fil) → forklarer repoet og dets værdi |

---

## Konklusion

Dette repo er en **bro mellem Business Central og AI-kodningsværktøjer**. I stedet for at en udvikler manuelt skal lære BC's API, skrive autentificering, og debugge feltnavne — giver vi AI'en al den viden den behøver, og lader den bygge løsningen.

Resultatet: Funktionelle Business Central-integrationer bygget på minutter i stedet for uger.
