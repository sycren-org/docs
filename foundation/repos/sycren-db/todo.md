# sycren-db — TODO Operativo
> PRD v1.2 · Giugno 2026 · 🕐 16 anni · Fase 0 (pre-commerciale) · Allineato a Bootstrap Roadmap v1.3

---

## Contesto (dalla Roadmap)

- Entrambi i founder hanno **16 anni 🕐** — Soluzione B: costruire tutto ora, vendere al **18° compleanno 🎂**
- GitHub org `sycren` attiva, repo `sycren-db` già inizializzato
- `syc ren-db` è il **secondo modulo** dell'intera piattaforma, immediatamente dopo la landing page
- Ordine di costruzione: Landing → **sycren-db** → Studio → App → Cloud → Domains → Security
- `syc ren-db` è **bloccante per tutti gli altri moduli** — nessun repository downstream può funzionare senza lo schema centralizzato
- Stima temporale roadmap: **Settimana 2–4** (finestra di 2 settimane) per completare schema, migrazioni e pubblicazione del pacchetto
- Responsabile: **Angelo** (lead), Cosimo (supporto)

### Cosa dipende da sycren-db

| Modulo | Bloccato da sycren-db? | Cosa usa |
|---|---|---|
| `studio.sycren.com` | ✅ Completamente — non esiste senza schema Projects, Users, Invoices | Tipi, client, helper |
| `app.sycren.com` | ✅ Completamente — non esiste senza schema Users, Notifications, Invoices | Tipi, client, helper |
| `cloud.sycren.com` | ✅ Completamente — non esiste senza schema Servers, Users | Tipi, client, helper |
| `domains.sycren.com` | ✅ Completamente — non esiste senza schema Domains, Users | Tipi, client, helper |
| `security.sycren.com` | ✅ Completamente — non esiste senza schema FirewallRule, ThreatLog, Servers | Tipi, client, helper |
| `syc ren-landing` | ⬜ Parziale — waitlist può essere gestita indipendentemente in Fase 0, ma convergerà su sycren-db in Fase 2 | Solo Waitlist |

---

## Setup Iniziale

- [x] Repository `github.com/sycren/sycren-db` creato (privato)
- [x] Repository aggiunto all'organizzazione GitHub `sycren`
- [ ] Clonare il repository in ambiente di sviluppo locale
- [ ] Inizializzare package.json con `name: @sycren/db` e `private: true`
- [ ] Configurare TypeScript strict mode (`tsconfig.json`)
- [ ] Installare dipendenze: Prisma 5+, `@prisma/client`, TypeScript, `tsx`, ESLint, Prettier
- [ ] Configurare `.npmrc` per GitHub Packages (`//npm.pkg.github.com/:_authToken=...`)
- [ ] Creare `.env.example` con DATABASE_URL, DIRECT_URL, MONGODB_URI, REDIS_URL
- [ ] Creare la struttura delle cartelle:
  ```
  prisma/
  src/
    types/
    utils/
  ```
- [ ] Inizializzare Prisma: `npx prisma init`

---

## Phase 1: Schema Design (priorità: CRITICA)

L'obiettivo di questa fase è definire lo schema Prisma completo con tutte le entità, relazioni, indici e vincoli.

### 1.1 Entities Core (Users, Projects, Invoices)

- [ ] Definire modello `User` con campi: id, email, passwordHash, name, avatarUrl, role, emailVerified, twoFactorEnabled, twoFactorSecret, createdAt, updatedAt
- [ ] Definire enum `UserRole`: OWNER, ADMIN, DEVELOPER, VIEWER
- [ ] Definire modello `Project` con: id, name, description, status, type, budget, deadline, milestones, clientId, createdAt, updatedAt
- [ ] Definire enum `ProjectStatus`: PENDING, ONBOARDING, IN_PROGRESS, REVIEW, COMPLETED, CANCELLED
- [ ] Definire enum `ProjectType`: LANDING_PAGE, WEB_APP, API_INTEGRATION, MAINTENANCE, OTHER
- [ ] Definire modello `ProjectMember` come tabella ponte con: id, userId, projectId, role, joinedAt
- [ ] Aggiungere vincolo `@@unique([userId, projectId])` su ProjectMember
- [ ] Definire modello `Invoice` con: id, invoiceNumber, userId, projectId, amount, currency, status, dueDate, paidAt, lines, stripePaymentIntentId, createdAt, updatedAt
- [ ] Definire enum `InvoiceStatus`: DRAFT, SENT, PAID, OVERDUE, CANCELLED, REFUNDED

### 1.2 Entities Divisioni (Domains, Servers, Security)

- [ ] Definire modello `Domain` con: id, domain, userId, registrar, registrationDate, expiryDate, autoRenew, dnsRecords, nameservers, privacyEnabled, status, createdAt, updatedAt
- [ ] Definire enum `DomainStatus`: ACTIVE, EXPIRED, TRANSFERRING, PENDING
- [ ] Definire modello `Server` con: id, name, provider, providerId, ipAddress, region, specs, monthlyCost, status, userId, lastPingAt, monitoringEnabled, backupEnabled, createdAt, updatedAt
- [ ] Definire enum `ServerStatus`: PROVISIONING, RUNNING, STOPPED, SUSPENDED, TERMINATED, ERROR
- [ ] Definire modello `FirewallRule` con: id, serverId, name, direction, action, protocol, port, sourceIp, priority, enabled, createdAt
- [ ] Definire enum `FirewallDirection`: INBOUND, OUTBOUND
- [ ] Definire enum `FirewallAction`: ALLOW, DENY
- [ ] Definire enum `FirewallProtocol`: TCP, UDP, ICMP, ANY
- [ ] Definire modello `ThreatLog` con: id, serverId, type, severity, sourceIp, description, mitigated, mitigatedAt, createdAt
- [ ] Definire enum `ThreatType`: DDoS, BRUTE_FORCE, SQL_INJECTION, XSS, PORT_SCAN, MALWARE, OTHER
- [ ] Definire enum `SeverityLevel`: LOW, MEDIUM, HIGH, CRITICAL

### 1.3 Entities Trasversali (Notifications, Waitlist)

- [ ] Definire modello `Notification` con: id, userId, title, message, type, channel, read, actionUrl, createdAt
- [ ] Definire enum `NotificationType`: PROJECT, BILLING, DOMAIN, SECURITY, SYSTEM, MARKETING
- [ ] Definire enum `NotificationChannel`: IN_APP, EMAIL, TELEGRAM, PUSH
- [ ] Definire modello `WaitlistEntry` con: id, email, name, projectType, message, source, convertedToClient, createdAt
- [ ] Aggiungere vincolo `@unique` su `WaitlistEntry.email`

### 1.4 Relazioni e Indici

- [ ] Definire relazione User → Project (tramite ProjectMember)
- [ ] Definire relazione User → Invoice
- [ ] Definire relazione User → Domain
- [ ] Definire relazione User → Server
- [ ] Definire relazione User → Notification
- [ ] Definire relazione Server → FirewallRule
- [ ] Definire relazione Server → ThreatLog
- [ ] Aggiungere indice su `User.email` (unique)
- [ ] Aggiungere indice su `User.role`
- [ ] Aggiungere indice su `Project.clientId`
- [ ] Aggiungere indice su `Project.status`
- [ ] Aggiungere indice su `Invoice.userId`
- [ ] Aggiungere indice su `Invoice.status`
- [ ] Aggiungere indice su `Invoice.invoiceNumber` (unique)
- [ ] Aggiungere indice su `Domain.domain` (unique)
- [ ] Aggiungere indice su `Domain.expiryDate`
- [ ] Aggiungere indice su `Server.userId`
- [ ] Aggiungere indice su `Server.status`
- [ ] Aggiungere indice su `Notification.userId`
- [ ] Aggiungere indice su `Notification.read`

---

## Phase 2: Migrations (priorità: ALTA)

Una volta che lo schema è completo e revisionato, si procede con la creazione della migrazione iniziale.

### 2.1 Prima Migrazione

- [ ] Verificare che `prisma/schema.prisma` sia completo e corretto
- [ ] Eseguire `prisma migrate dev --name init` per creare la migrazione iniziale
- [ ] Verificare il file SQL generato in `prisma/migrations/` — controllare che tutte le tabelle, FK, indici e constraint siano corretti
- [ ] Applicare la migrazione al database Supabase di sviluppo
- [ ] Verificare che tutte le tabelle siano state create correttamente (tramite `psql` o Supabase dashboard)
- [ ] Testare l'inserimento manuale di un record in ogni tabella tramite `prisma studio` o query SQL diretta

### 2.2 Versioning Strategy

- [ ] Definire convenzione nomi migrazioni: `YYYYMMDD_HHMMSS_nome-descrittivo`
- [ ] Documentare che tutte le migrazioni future devono essere backward-compatible
- [ ] Creare script per `prisma migrate deploy` in CI/CD
- [ ] Aggiungere sezione "Migration Guide" nella documentazione del pacchetto
- [ ] Configurare `prisma generate` come postinstall script in package.json

### 2.3 Future Migrations Policy

- [ ] Mai eliminare una colonna senza deprecation period di almeno 2 release
- [ ] Data migration complesse vanno accompagnate da script dedicato in `/prisma/migrations/scripts/`
- [ ] Ogni migrazione deve essere testata su un database di staging prima della produzione
- [ ] Non rinominare campi — creare nuovo campo e deprecare il vecchio

---

## Phase 3: Types & Utilities (priorità: ALTA)

Dopo la migrazione applicata, si generano i tipi TypeScript e si costruiscono le utility condivise.

### 3.1 Tipi Generati

- [ ] Eseguire `prisma generate` per generare i tipi TypeScript
- [ ] Creare `src/types/index.ts` con re-export di tutti i tipi generati:
  - `PrismaClient`, `Prisma` (namespace)
  - Tutti i modelli: User, Project, ProjectMember, Invoice, Domain, Server, FirewallRule, ThreatLog, Notification, WaitlistEntry
  - Tutti gli enum: UserRole, ProjectStatus, ProjectType, ecc.
  - Tutti gli input type: UserCreateInput, UserUpdateInput, ProjectWhereUniqueInput, ecc.
- [ ] Verificare che i tipi siano corretti con `tsc --noEmit`

### 3.2 Connection Singleton

- [ ] Implementare `src/utils/connection.ts` con pattern singleton per PrismaClient
- [ ] Aggiungere log query in development, solo error in production
- [ ] Gestire graceful shutdown (prisma.$disconnect() su SIGTERM/SIGINT)
- [ ] Verificare che funzioni in ambiente serverless (Next.js Edge) — nessuna connessione persistente cross-request

### 3.3 Helper Functions

- [ ] Implementare `paginate(page, perPage)` — restituisce { skip, take } per query Prisma
- [ ] Implementare `buildWhereClause(filters)` — costruisce where clause da oggetto filtri
- [ ] Implementare `sanitizeUser(user)` — rimuove passwordHash, twoFactorSecret dall'output
- [ ] Implementare `generateInvoiceNumber()` — formato `INV-2026-0001`
- [ ] Implementare `calculateDomainExpiry(registrationDate, term)` — calcolo prossima scadenza
- [ ] Implementare `formatCurrency(amount, currency)` — formattazione importi

### 3.4 Seed Data

- [ ] Creare `src/utils/seed.ts` con funzione `main()` per popolare il database
- [ ] Inserire 3 utenti di test:
  - `owner@syc ren.com` (OWNER) — Angelo
  - `admin@syc ren.com` (ADMIN) — Cosimo
  - `client@example.com` (VIEWER) — Cliente test
- [ ] Inserire 2 progetti di esempio con milestone
- [ ] Inserire 1 fattura di test (SENT, in attesa di pagamento)
- [ ] Inserire 1 dominio fittizio (`example.com`, expiry tra 30 giorni)
- [ ] Inserire 1 server fittizio (Hetzner, RUNNING)
- [ ] Inserire 1 firewall rule (ALLOW TCP/22)
- [ ] Inserire 1 threat log (BRUTE_FORCE, MEDIUM)
- [ ] Inserire 1 notifica di esempio
- [ ] Aggiungere script `"seed": "tsx src/utils/seed.ts"` in package.json
- [ ] Documentare che il seed è per sviluppo locale, mai usare in produzione

### 3.5 MongoDB Connection

- [ ] Implementare `src/utils/mongodb.ts` con MongoClient singleton
- [ ] Gestire errori di connessione con retry logic
- [ ] Esportare `mongoDb` per uso nei repository downstream

### 3.6 Redis Connection

- [ ] Implementare utility per connessione Redis (ioredis o redis npm)
- [ ] Configurare Redis per caching delle sessioni e rate limiting
- [ ] Gestire errori di connessione (graceful degradation)

---

## Phase 4: Package Setup (priorità: MEDIA)

Una volta che schema, migrazioni e utility sono pronte, si prepara il pacchetto per la pubblicazione.

### 4.1 Package.json

- [ ] Completare package.json con campi obbligatori:
  ```json
  {
    "name": "@sycren/db",
    "version": "1.0.0",
    "private": true,
    "main": "dist/index.js",
    "types": "dist/index.d.ts",
    "scripts": {
      "build": "tsc",
      "dev": "tsc --watch",
      "generate": "prisma generate",
      "migrate:dev": "prisma migrate dev",
      "migrate:deploy": "prisma migrate deploy",
      "seed": "tsx src/utils/seed.ts",
      "lint": "eslint src/",
      "typecheck": "tsc --noEmit"
    }
  }
  ```

### 4.2 Build & Publish

- [ ] Configurare `tsconfig.json` per build in `/dist`
- [ ] Verificare che `npm run build` produca output corretto
- [ ] Verificare che i tipi siano correttamente generati in `dist/index.d.ts`
- [ ] Pubblicare su GitHub Packages: `npm publish --registry=https://npm.pkg.github.com`
- [ ] Verificare installazione in un repo downstream di test
- [ ] Configurare GitHub Actions per pubblicazione automatica su push a `main`
- [ ] Configurare versionamento semantico (major.minor.patch)

### 4.3 CI/CD Pipeline

- [ ] Creare `.github/workflows/publish.yml`:
  ```yaml
  name: Publish @sycren/db
  on:
    push:
      branches: [main]
  jobs:
    publish:
      runs-on: ubuntu-latest
      steps:
        - uses: actions/checkout@v4
        - uses: actions/setup-node@v4
          with:
            node-version: 20
            registry-url: https://npm.pkg.github.com
        - run: npm ci
        - run: npm run lint
        - run: npm run typecheck
        - run: npm run build
        - run: npm publish
          env:
            NODE_AUTH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
  ```
- [ ] Aggiungere workflow per test su PR (lint + typecheck)
- [ ] Verificare che i secrets siano configurati nell'organizzazione GitHub

### 4.4 Documentation

- [ ] Aggiornare README.md con:
  - Cos'è sycren-db e perché esiste
  - Come installare (`npm install @sycren/db`)
  - Come configurare (.env, DATABASE_URL)
  - Come eseguire migrazioni (`npx prisma migrate dev`)
  - Come generare tipi (`npx prisma generate`)
  - Esempi di utilizzo (connessione, query, helpers)
  - Link al PRD (`requirements.md`) e TODO (`todo.md`)
- [ ] Creare `MIGRATION_GUIDE.md` con:
  - Come creare una nuova migrazione
  - Best practices (backward compatibility, naming)
  - Come eseguire data migration
  - Esempi di rollback

---

## Verification Criteria

Prima di dichiarare `syc ren-db` completo e passare al modulo successivo (`studio.sycren.com`):

- [ ] `prisma migrate dev` esegue senza errori
- [ ] `prisma generate` produce output senza warning
- [ ] `npm run build` produce `/dist` completo
- [ ] `npm run lint` passa senza errori
- [ ] `npm run typecheck` passa senza errori
- [ ] Seed popola il database con tutti i dati di test
- [ ] `prisma studio` mostra tutte le tabelle popolate
- [ ] Tutti i tipi sono esportati correttamente da `src/index.ts`
- [ ] Helper functions sono testate manualmente
- [ ] MongoDB connessione funzionante
- [ ] Redis connessione funzionante
- [ ] Pacchetto installabile via GitHub Packages
- [ ] README.md completo e aggiornato
- [ ] MIGRATION_GUIDE.md completo
- [ ] CI/CD passa su push a `main`

---

## Oltre sycren-db — Cosa Segue (con Età)

Completato `syc ren-db`, l'ordine di sviluppo prosegue:

| # | Modulo | Età 🕐 | Responsabile | Dipende da |
|---|---|---|---|---|
| 1 | Landing Page `syc ren.com` | **16** | Angelo (lead), Cosimo (lead) | — (parallelo) |
| 2 | **syc ren-db** | **16** | Angelo (lead), Cosimo (supporto) | Landing (non bloccante) |
| 3 | Client Portal `studio.sycren.com` | **16** | Angelo (lead), Cosimo (lead) | **syc ren-db** ✅ |
| 4 | Hub Centrale / SSO `app.sycren.com` | **16–17** | Angelo (lead), Cosimo (supporto) | **syc ren-db** ✅ |
| 5 | Cloud Lite `cloud.sycren.com` | **17** | Angelo (lead) | **syc ren-db** ✅ |
| 6 | Domains `domains.sycren.com` | **17–18** | — | **syc ren-db** ✅ |
| 7 | Security `security.sycren.com` | **18+** | — | **syc ren-db** ✅ |
| 🎂 | **Lancio Commerciale (P.IVA, Vendite)** | **18** | Entrambi | Tutti i moduli pronti |

---

*Sycren — sycren-db TODO v1.2 · Giugno 2026 · Allineato a Bootstrap Roadmap v1.3*
