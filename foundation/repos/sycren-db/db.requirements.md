# sycren-db — Database Foundation: Project Requirements
> PRD v1.2 — Giugno 2026 — Uso interno — Allineato a Bootstrap Roadmap v1.3

---

## 1. Contesto e Obiettivo

Sycren è una piattaforma unificata composta da 4 divisioni operative (Studios, Cloud, Domains, Security) che convergono in un hub centrale (`app.sycren.com`). Ogni divisione ha un proprio sottodominio e un proprio repository, ma **tutte condividono lo stesso database sottostante**.

`syc ren-db` è il pacchetto condiviso che contiene lo schema Prisma canonicale, le migrazioni versionate, i tipi TypeScript generati e le utility di database per tutte le applicazioni della piattaforma. È il fondamento su cui si basa l'intero ecosistema tecnico di Sycren.

**Perché deve essere costruito prima di tutto:**

> `syc ren-db` è il **secondo mattone** della piattaforma (dopo la landing page). Nessun altro modulo (`studio.sycren.com`, `app.sycren.com`, `cloud.sycren.com`, `domains.sycren.com`, `security.sycren.com`) può esistere senza uno schema dati centralizzato e condiviso. Costruirlo ora significa che ogni repository futuro importerà tipi e funzioni da questo pacchetto, invece di duplicare definizioni o peggio — avere schemi divergenti.

**Principio guida:**

> **Un solo schema. Un solo punto di verità. Tutti i repository lo importano.**

**Stato attuale (Giugno 2026):**
- [x] Organizzazione GitHub `sycren` creata con tutti i repository
- [x] `sycren-db` repository inizializzato (privato)
- [x] `syc ren.com` landing page in sviluppo
- [ ] Schema Prisma definito
- [ ] Migrazioni applicate
- [ ] Tipi TypeScript generati e pubblicati
- [ ] Utility DB implementate

**Età dei fondatori:** 🕐 16 anni — Fase 0 (pre-commerciale). Il pacchetto viene sviluppato durante la finestra Settimana 2–4 della roadmap, interamente prima del lancio commerciale.

---

## 2. Schema Entities — Dettaglio Completo

Lo schema Prisma è organizzato in 8 entità principali, con relazioni, indici e vincoli definiti.

### 2.1 Users

Entità centrale per autenticazione, autorizzazione e profili. Utilizzata da `app.sycren.com` (SSO) e importata da tutte le divisioni.

| Campo | Tipo | Vincoli | Descrizione |
|---|---|---|---|
| `id` | UUID | PK, default `uuid()` | Identificatore univoco |
| `email` | String | Unico, required | Email di accesso |
| `passwordHash` | String | Required | Hash bcrypt/argon2 |
| `name` | String | Required | Nome completo |
| `avatarUrl` | String? | — | URL avatar (Gravatar o upload) |
| `role` | Enum | Required, default `VIEWER` | `OWNER`, `ADMIN`, `DEVELOPER`, `VIEWER` |
| `emailVerified` | Boolean | default `false` | Flag verifica email |
| `twoFactorEnabled` | Boolean | default `false` | 2FA status |
| `twoFactorSecret` | String? | — | Secret TOTP |
| `createdAt` | DateTime | default `now()` | |
| `updatedAt` | DateTime | @updatedAt | |

**Relazioni:**
- `User` → `Project` (uno-a-molti, tramite `ProjectMember`)
- `User` → `Invoice` (uno-a-molti)
- `User` → `Notification` (uno-a-molti)
- `User` → `SecurityAlert` (uno-a-molti)

**Indici:**
- `email` (unico)
- `role` (per filtraggio rapido)

### 2.2 Projects

Rappresenta i progetti dei clienti di Sycren Studios. Centro del flusso di lavoro di `studio.sycren.com`.

| Campo | Tipo | Vincoli | Descrizione |
|---|---|---|---|
| `id` | UUID | PK, default `uuid()` | |
| `name` | String | Required | Nome del progetto |
| `description` | String? | — | Descrizione/testo libero |
| `status` | Enum | Required, default `PENDING` | `PENDING`, `ONBOARDING`, `IN_PROGRESS`, `REVIEW`, `COMPLETED`, `CANCELLED` |
| `type` | Enum | Required | `LANDING_PAGE`, `WEB_APP`, `API_INTEGRATION`, `MAINTENANCE`, `OTHER` |
| `budget` | Decimal? | — | Budget stimato in EUR |
| `deadline` | DateTime? | — | Scadenza concordata |
| `milestones` | JSON | default `[]` | Array di milestone con date e check |
| `clientId` | UUID | FK → `User.id` | Cliente proprietario |
| `createdAt` | DateTime | default `now()` | |
| `updatedAt` | DateTime | @updatedAt | |

**Relazioni:**
- `Project` → `User` (molti-a-molti, tramite `ProjectMember`)
- `Project` → `Invoice` (uno-a-molti)

**Indici:**
- `clientId` (FK lookup)
- `status` (filtraggio dashboard)
- `type` (filtraggio portfolio)

### 2.3 ProjectMember

Tabella ponte per relazione molti-a-molti tra `User` e `Project` con un ruolo specifico nel progetto.

| Campo | Tipo | Vincoli | Descrizione |
|---|---|---|---|
| `id` | UUID | PK, default `uuid()` | |
| `userId` | UUID | FK → `User.id`, required | Membro del team |
| `projectId` | UUID | FK → `Project.id`, required | Progetto assegnato |
| `role` | Enum | Required, default `MEMBER` | `OWNER`, `MANAGER`, `MEMBER`, `VIEWER` |
| `joinedAt` | DateTime | default `now()` | |

**Vincoli:** `@@unique([userId, projectId])` — un utente non può avere due ruoli nello stesso progetto.
**Indice:** `projectId` (per query di progetto).

### 2.4 Invoices

Gestisce la fatturazione di tutti i servizi Sycren. Utilizzata da `app.sycren.com` (billing) e importata da tutte le divisioni.

| Campo | Tipo | Vincoli | Descrizione |
|---|---|---|---|
| `id` | UUID | PK, default `uuid()` | |
| `invoiceNumber` | String | Unico, required | Formato `INV-YYYY-NNNN` |
| `userId` | UUID | FK → `User.id`, required | Cliente fatturato |
| `projectId` | UUID? | FK → `Project.id` | Progetto associato (se applicabile) |
| `amount` | Decimal | Required, default `0` | Importo totale in EUR |
| `currency` | String | default `EUR` | Valuta |
| `status` | Enum | Required, default `DRAFT` | `DRAFT`, `SENT`, `PAID`, `OVERDUE`, `CANCELLED`, `REFUNDED` |
| `dueDate` | DateTime | Required | Data scadenza |
| `paidAt` | DateTime? | — | Data pagamento |
| `lines` | JSON | default `[]` | Array di line item (description, qty, unitPrice, total) |
| `stripePaymentIntentId` | String? | — | ID Stripe per pagamento |
| `createdAt` | DateTime | default `now()` | |
| `updatedAt` | DateTime | @updatedAt | |

**Indici:**
- `userId` (storico fatture per utente)
- `invoiceNumber` (unico)
- `status` (filtraggio overdue/paid)
- `dueDate` (alert scadenze)

### 2.5 Domains

Gestisce i domini registrati tramite Sycren Domains.

| Campo | Tipo | Vincoli | Descrizione |
|---|---|---|---|
| `id` | UUID | PK, default `uuid()` | |
| `domain` | String | Unico, required | Nome dominio completo (`example.com`) |
| `userId` | UUID | FK → `User.id`, required | Proprietario |
| `registrar` | String | Required | Provider di registrazione |
| `registrationDate` | DateTime | default `now()` | |
| `expiryDate` | DateTime | Required | Data scadenza |
| `autoRenew` | Boolean | default `true` | Rinnovo automatico |
| `dnsRecords` | JSON | default `[]` | Array record DNS |
| `nameservers` | String[] | default `[]` | Nameserver configurati |
| `privacyEnabled` | Boolean | default `true` | WHOIS privacy |
| `status` | Enum | Required, default `ACTIVE` | `ACTIVE`, `EXPIRED`, `TRANSFERRING`, `PENDING` |
| `createdAt` | DateTime | default `now()` | |
| `updatedAt` | DateTime | @updatedAt | |

**Indici:**
- `domain` (unico, ricerca)
- `userId` (domini per utente)
- `expiryDate` (notifiche pre-scadenza)
- `status` (filtraggio)

### 2.6 Servers

Rappresenta le istanze VPS gestite da Sycren Cloud.

| Campo | Tipo | Vincoli | Descrizione |
|---|---|---|---|
| `id` | UUID | PK, default `uuid()` | |
| `name` | String | Required | Nome identificativo server |
| `provider` | String | Required | Hetzner, DigitalOcean, ecc. |
| `providerId` | String | Required | ID istanza presso provider |
| `ipAddress` | String | Required | IPv4 pubblico |
| `region` | String | Required | Zona/datacenter |
| `specs` | JSON | Required | `{ cpu, ram, disk, bandwidth }` |
| `monthlyCost` | Decimal | Required, default `0` | Costo mensile in EUR |
| `status` | Enum | Required, default `PROVISIONING` | `PROVISIONING`, `RUNNING`, `STOPPED`, `SUSPENDED`, `TERMINATED`, `ERROR` |
| `userId` | UUID | FK → `User.id`, required | Proprietario |
| `lastPingAt` | DateTime? | — | Ultimo heartbeat |
| `monitoringEnabled` | Boolean | default `true` | Uptime monitoring |
| `backupEnabled` | Boolean | default `true` | Backup automatici |
| `createdAt` | DateTime | default `now()` | |
| `updatedAt` | DateTime | @updatedAt | |

**Indici:**
- `userId` (server per utente)
- `status` (dashboard)
- `provider` (raggruppamento costi)

### 2.7 Security

Entità per la gestione della sicurezza. Include firewall rules, threat logs e report.

#### 2.7.1 FirewallRule

| Campo | Tipo | Vincoli | Descrizione |
|---|---|---|---|
| `id` | UUID | PK, default `uuid()` | |
| `serverId` | UUID | FK → `Server.id`, required | Server associato |
| `name` | String | Required | Nome regola |
| `direction` | Enum | Required | `INBOUND`, `OUTBOUND` |
| `action` | Enum | Required | `ALLOW`, `DENY` |
| `protocol` | Enum | Required | `TCP`, `UDP`, `ICMP`, `ANY` |
| `port` | Int? | — | Porta o range |
| `sourceIp` | String? | — | CIDR o IP singolo |
| `priority` | Int | default `100` | Ordine di valutazione |
| `enabled` | Boolean | default `true` | |
| `createdAt` | DateTime | default `now()` | |

**Indice:** `serverId` (regole per server).

#### 2.7.2 ThreatLog

| Campo | Tipo | Vincoli | Descrizione |
|---|---|---|---|
| `id` | UUID | PK, default `uuid()` | |
| `serverId` | UUID | FK → `Server.id`? | Server colpito |
| `type` | Enum | Required | `DDoS`, `BRUTE_FORCE`, `SQL_INJECTION`, `XSS`, `PORT_SCAN`, `MALWARE`, `OTHER` |
| `severity` | Enum | Required | `LOW`, `MEDIUM`, `HIGH`, `CRITICAL` |
| `sourceIp` | String? | — | IP attaccante |
| `description` | String | Required | Descrizione evento |
| `mitigated` | Boolean | default `false` | Azione intrapresa |
| `mitigatedAt` | DateTime? | — | |
| `createdAt` | DateTime | default `now()` | |

**Indici:**
- `serverId` (log per server)
- `severity` (filtraggio criticità)
- `createdAt` (ordinamento cronologico)

### 2.8 Notifications

Sistema di notifiche unificato per tutti i servizi Sycren. Alimentato da `app.sycren.com`.

| Campo | Tipo | Vincoli | Descrizione |
|---|---|---|---|
| `id` | UUID | PK, default `uuid()` | |
| `userId` | UUID | FK → `User.id`, required | Destinatario |
| `title` | String | Required | Titolo notifica |
| `message` | String | Required | Corpo della notifica |
| `type` | Enum | Required | `PROJECT`, `BILLING`, `DOMAIN`, `SECURITY`, `SYSTEM`, `MARKETING` |
| `channel` | Enum | Required, default `IN_APP` | `IN_APP`, `EMAIL`, `TELEGRAM`, `PUSH` |
| `read` | Boolean | default `false` | Stato lettura |
| `actionUrl` | String? | — | Link di approfondimento |
| `createdAt` | DateTime | default `now()` | |

**Indici:**
- `userId` (feed notifiche)
- `read` (notifiche non lette)
- `type` (filtraggio per categoria)

### 2.9 Waitlist

Entità legacy per la raccolta contatti pre-lancio (Fase 0). Popolata dalla landing page.

| Campo | Tipo | Vincoli | Descrizione |
|---|---|---|---|
| `id` | UUID | PK, default `uuid()` | |
| `email` | String | Unico, required | Email del contatto |
| `name` | String? | — | Nome (opzionale) |
| `projectType` | String? | — | Tipo di progetto di interesse |
| `message` | String? | max 500 | Messaggio opzionale |
| `source` | String | default `landing` | Provenienza |
| `convertedToClient` | Boolean | default `false` | Flags se diventato cliente dopo lancio |
| `createdAt` | DateTime | default `now()` | |

**Indice:** `email` (unico, anti-duplicato).

---

## 3. Package Architecture

Il pacchetto `syc ren-db` è strutturato come un npm package privato, pubblicato su GitHub Packages.

```
sycren-db/
├── prisma/
│   ├── schema.prisma          # Schema canonico (unica fonte di verità)
│   └── migrations/            # Migrazioni versionate (Prisma Migrate)
├── src/
│   ├── types/                 # Tipi TypeScript generati (re-export da @prisma/client)
│   │   └── index.ts
│   ├── utils/
│   │   ├── connection.ts      # Connessione Prisma client (singleton)
│   │   ├── helpers.ts         # Funzioni helper (paginazione, filtri, parsing)
│   │   ├── seed.ts            # Seed data per sviluppo e testing
│   │   └── mongodb.ts         # Connessione MongoDB per dati non strutturati
│   └── index.ts               # Entry point del pacchetto
├── package.json               # name: @sycren/db, private: true
├── tsconfig.json              # Strict mode
└── .npmrc                     # GitHub Packages auth config
```

### 3.1 Entry Point (`src/index.ts`)

```typescript
// Re-export Prisma client
export { PrismaClient } from '@prisma/client';
export type { User, Project, Invoice, Domain, Server, FirewallRule, ThreatLog, Notification, WaitlistEntry } from '@prisma/client';

// Re-export generati
export * from './types';
export * from './utils/connection';
export * from './utils/helpers';
```

### 3.2 Connection Singleton (`src/utils/connection.ts`)

Pattern singleton per il Prisma Client, evitando connessioni multiple in ambienti serverless (Next.js Edge, Vercel).

```typescript
import { PrismaClient } from '@prisma/client';

const globalForPrisma = globalThis as unknown as { prisma: PrismaClient | undefined };

export const prisma = globalForPrisma.prisma ?? new PrismaClient({
  log: process.env.NODE_ENV === 'development' ? ['query', 'warn', 'error'] : ['error'],
});

if (process.env.NODE_ENV !== 'production') globalForPrisma.prisma = prisma;
```

### 3.3 Helpers (`src/utils/helpers.ts`)

Funzioni utility condivise:
- `paginate(page, perPage)` — Costrutto skip/take per query paginate
- `buildWhereClause(filters)` — Costruzione dinamica del where clause
- `sanitizeUser(user)` — Rimozione campi sensibili (passwordHash, secret) dall'output
- `generateInvoiceNumber()` — Generazione sequenziale `INV-YYYY-NNNN`
- `calculateDomainExpiry(domain)` — Calcolo prossima scadenza

### 3.4 Seed Data (`src/utils/seed.ts`)

Script per popolare il database di sviluppo con dati realistici:
- 3 utenti di test (owner, admin, viewer)
- 2 progetti di esempio con milestone
- 1 fattura di test
- 1 dominio fittizio
- 1 server fittizio
- 1 firewall rule
- 1 threat log
- 1 notifica di esempio

Eseguibile via `npx tsx src/utils/seed.ts`.

### 3.5 MongoDB Connection (`src/utils/mongodb.ts`)

Connessione separata per MongoDB, utilizzato per dati non strutturati (log estesi, configurazioni dinamiche, contenuti CMS).

```typescript
import { MongoClient } from 'mongodb';

const client = new MongoClient(process.env.MONGODB_URI!);
export const mongoDb = client.db('sycren');
```

---

## 4. Technical Stack

| Layer | Tecnologia | Versione | Note |
|---|---|---|---|
| ORM | Prisma | 5.x | Schema-first, type-safe, migrazioni integrate |
| Database primario | PostgreSQL (via Supabase) | 15+ | Provider gratuito (500MB) in Fase 0 |
| Database secondario | MongoDB | 7+ | Dati non strutturati (logs, configs) |
| Cache / Sessioni | Redis | 7+ | Session store, rate limiting, job queue |
| Linguaggio | TypeScript | 5+ (strict) | Tipizzazione rigorosa su tutto il pacchetto |
| Package manager | npm / pnpm | — | Pubblicazione su GitHub Packages |
| Runtime | Node.js | 20+ LTS | Compatibilità con Next.js Edge |

### 4.1 Supabase Configuration

```env
# .env
DATABASE_URL="postgresql://postgres:[PASSWORD]@db.[REF].supabase.co:5432/postgres"
DIRECT_URL="postgresql://postgres:[PASSWORD]@db.[REF].supabase.co:5432/postgres"
MONGODB_URI="mongodb+srv://..."
REDIS_URL="redis://..."
```

Supabase è scelto per:
- Tier gratuito generoso (500MB database, auth integrata)
- Backup automatici giornalieri
- SSL nativo
- Connection pooling via PgBouncer (opzionale)
- Facile upgrade a tier a pagamento quando necessario

---

## 5. Integration Points

Ogni repository dell'ecosistema Sycren dipende da `syc ren-db`.

| Repository | Dipendenza | Cosa Importa |
|---|---|---|
| `sycren-landing` | Solo Waitlist (diretta via env) | Nei primi tempi la landing usa un proprio DB / API, in Fase 2 migra a `syc ren-db` |
| `sycren-app` | **Diretta** | Tipi `User`, `Notification`, `Invoice` + utility connessione + helper |
| `sycren-studio` | **Diretta** | Tipi `Project`, `ProjectMember`, `Invoice`, `User` + helper + seed |
| `sycren-cloud` | **Diretta** | Tipi `Server`, `User`, `Invoice` + helper connessione |
| `sycren-domains` | **Diretta** | Tipi `Domain`, `User`, `Invoice` + helper |
| `sycren-security` | **Diretta** | Tipi `FirewallRule`, `ThreatLog`, `Server`, `User` + helper |

**Come viene installato:**

```bash
npm install @sycren/db
# o da GitHub Packages:
npm install @sycren/db --registry=https://npm.pkg.github.com
```

### 5.1 Schema Evolution Flow

1. `syc ren-db` aggiorna lo schema Prisma → genera migrazione
2. Pacchetto pubblicato con nuova versione
3. Repository downstream aggiornano dipendenza `npm update @sycren/db`
4. Prisma Client rigenerato localmente in ogni repo (o pre-generato nel pacchetto)
5. Migrazione applicata al DB condiviso

---

## 6. Development Guidelines

### 6.1 Migration Strategy

- **Tutte le migrazioni devono essere backward-compatible** — non eliminare colonne senza deprecation period
- Le migrazioni vengono create con `npx prisma migrate dev --name <descrizione>`
- In produzione: `npx prisma migrate deploy` (nessuna generazione automatica)
- Schema changes che richiedono data migration vanno accompagnate da script dedicato in `/prisma/migrations/scripts/`

### 6.2 Naming Conventions

| Entità | Convenzione | Esempio |
|---|---|---|
| Modelli Prisma | PascalCase, singolare | `User`, `ProjectMember` |
| Campi | camelCase | `createdAt`, `passwordHash` |
| Enum | PascalCase, valori UPPER | `ProjectStatus.PENDING` |
| Chiavi esterne | `[relatedModel]Id` in camelCase | `userId`, `projectId` |
| Indici composti | `@@unique([field1, field2])` | `@@unique([userId, projectId])` |
| Relazioni | Nome relazione esplicito | `owner`, `members`, `invoices` |

### 6.3 TypeScript Strict Mode

```json
{
  "compilerOptions": {
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "exactOptionalPropertyTypes": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true
  }
}
```

### 6.4 Git Workflow

- Branch `main` — release candidate, sempre deployabile
- Branch `develop` — integrazione giornaliera
- Feature branches: `feat/nome-feature`
- Commit messages: Conventional Commits (`feat:`, `fix:`, `chore:`, `docs:`)
- Nessun commit diretto su `main`

### 6.5 CI/CD (GitHub Actions)

Su push su `main`:
- `npm run lint` (ESLint + Prettier)
- `npm run typecheck` (tsc --noEmit)
- `npm run test` (se presenti)
- Pubblicazione automatica su GitHub Packages con bump di versione

---

## 7. Completion Criteria

- [ ] Schema Prisma completo con tutte le 8 entità e relazioni definite
- [ ] Prima migrazione applicata al database Supabase
- [ ] Tipi TypeScript generati e re-exportati dal pacchetto
- [ ] Connection singleton Prisma implementato
- [ ] Helper functions implementate (paginazione, sanitize, generazione fatture)
- [ ] Seed data script funzionante con dati realistici
- [ ] Connessione MongoDB implementata
- [ ] Connessione Redis implementata
- [ ] Package entry point (`src/index.ts`) completo con re-export
- [ ] Pacchetto pubblicato su GitHub Packages come `@sycren/db`
- [ ] README.md aggiornato con istruzioni di installazione e utilizzo
- [ ] Migration guide documentata per sviluppatori
- [ ] CI/CD configurato su GitHub Actions

---

## 8. Dependencies Tree — Come si collega alla Roadmap

```
┌──────────────────────────────────────────────────────────┐
│                    FASE 0 (16 anni 🕐)                    │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐           │
│  │ Landing  │    │ sycren-db │    │ Studio   │           │
│  │ (sett 1) │───►│ (sett 2-4)│───►│ (sett 4-8)│           │
│  └──────────┘    └──────────┘    └──────────┘           │
│                        │                                 │
│                        ▼                                 │
│                  ┌──────────┐    ┌──────────┐           │
│                  │ App/SSO  │    │ Cloud    │           │
│                  │ (mese 2) │    │ (mese 3) │           │
│                  └──────────┘    └──────────┘           │
│                        │            │                    │
│                        ▼            ▼                    │
│                  ┌──────────┐    ┌──────────┐           │
│                  │ Domains  │    │ Security │           │
│                  │ (mese 4) │    │ (mese 5+)│           │
│                  └──────────┘    └──────────┘           │
└──────────────────────────────────────────────────────────┘
```

`syc ren-db` è il fondamento. Senza di esso, nessun modulo downstream può funzionare con dati coerenti.

---

## Related Notes

| Document | Description |
|---|---|
| [[db.README]] | sycren-db overview |
| [[db.todo]] | Implementation plan |
| [[app.requirements\|sycren-app requirements]] | Consumer — User, Notification, Invoice |
| [[studio.requirements\|sycren-studio requirements]] | Consumer — User, Project, Invoice |
| [[cloud.requirements\|sycren-cloud requirements]] | Consumer — User, Server |
| [[domains.requirements\|sycren-domains requirements]] | Consumer — User, Domain, DnsRecord |
| [[security.requirements\|sycren-security requirements]] | Consumer — User, SecurityRule, ThreatLog |
| [[discord-bot.requirements\|sycren-discord-bot requirements]] | Optional consumer — analytics logging |
| [[foundation/ROADMAP\|Bootstrap Roadmap]] | Build order — sycren-db is module #2 |

---

*Sycren — sycren-db PRD v1.2 — Giugno 2026*
*Documento interno. Non distribuire senza autorizzazione scritta.*
