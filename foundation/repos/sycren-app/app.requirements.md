# sycren-app — Hub Centrale: Requisiti di Progetto
> PRD v1.0 — Giugno 2026 — Uso interno — Allineato alla Bootstrap Roadmap v1.3

> **Età di sviluppo:** 🕐 16–17 anni (Fase 0 pre-commerciale, sviluppo parallelo a `studio.sycren.com`)
> **Responsabile:** Angelo (Lead architettura + backend), Cosimo (Supporto frontend)

---

## 1. Contesto e Obiettivo

Sycren è una piattaforma unificata per sviluppo software, cloud hosting, gestione domini e cybersecurity, strutturata in quattro divisioni operative (Studios, Cloud, Domains, Security) che convergono in un ecosistema integrato.

**sycren-app** è il cuore dell'ecosistema: una dashboard centrale accessibile via `app.sycren.com` che aggrega e gestisce tutti i servizi delle quattro divisioni attraverso un **Single Sign-On (SSO)**. L'utente accede a un unico ambiente dove può gestire progetti Studios, monitorare server Cloud, rinnovare domini e controllare la sicurezza della propria infrastruttura — senza dover navigare tra piattaforme separate.

**Obiettivo primario:** Eliminare la frammentazione tecnologica che caratterizza l'acquisto di servizi digitali da provider multipli, offrendo un'esperienza utente coerente, trasparente e centralizzata.

**Obiettivo secondario:** Abilitare la gestione unificata di fatturazione, notifiche e supporto per tutte le divisioni Sycren, riducendo l'attrito operativo per i clienti e semplificando la gestione interna per il team Sycren.

**Stato attuale (Giugno 2026):**
- [x] Organizzazione GitHub `sycren` creata con struttura repository completa
- [x] Dominio `sycren.com` registrato — DNS su Cloudflare
- [x] `sycren-db` schema Prisma centralizzato in sviluppo
- [ ] Landing page `sycren.com` in sviluppo (priorità #1)
- [ ] `sycren-app` in fase di pianificazione — questo documento
- [ ] `studio.sycren.com` MVP in fase di definizione

**Principio guida del bootstrap:**
> Costruisci tutto ora. Vendi al momento giusto (18 anni 🎂). La struttura segue i ricavi, non li precede.

L'app viene sviluppata durante la Fase 0 (16-17 anni) per essere pronta al lancio commerciale. Non genera ricavi fino alla maggiore età, ma è un componente abilitante per l'intero ecosistema.

---

## 2. Utenti Target

| Segmento | Caratteristiche | Divisioni di Interesse |
|---|---|---|
| **Clienti Studios** | Aziende e startup con progetti di sviluppo attivi. Necessitano di tracciare avanzamento, milestone, comunicare con il team. | Studios (primario) |
| **Clienti Cloud** | Developer e PM che gestiscono VPS, hosting e deploy. Monitorano uptime, risorse, backup. | Cloud (primario), Security |
| **Clienti Domains** | Professionisti e aziende con portafoglio domini. Gestiscono rinnovi, DNS, trasferimenti. | Domains (primario), Cloud |
| **Clienti Security** | CTO e amministratori che monitorano la sicurezza dell'infrastruttura. | Security (primario) |
| **Clienti Multi-Servizio** | Clienti che utilizzano più divisioni contemporaneamente — il target principale dell'app unificata. | Tutte |
| **Admin Sycren** | Team Sycren che gestisce clienti, fatture, ticket e configurazioni attraverso un'unica interfaccia. | Tutte (gestione) |
| **Developer Sycren** | Team di sviluppo che accede a log, deploy, monitoring e strumenti tecnici. | Cloud, Security, Studios |

### Casi d'Uso Primari

1. **Utente Business Multi-Servizio:** Un cliente con un progetto Studios in corso, un VPS Cloud attivo e un dominio gestito accede a `app.sycren.com`, vede lo stato di tutto in unica dashboard, paga una fattura unificata, apre un ticket di supporto.
2. **Admin Sycren:** Visualizza tutti i clienti attivi, i loro servizi in uso, lo stato pagamenti. Aggiunge/rimuove servizi, configura notifiche, gestisce ruoli del team.
3. **Developer:** Accede ai log di deploy Cloud, visualizza alert di sicurezza, gestisce chiavi API e configurazioni tecniche.

---

## 3. Architettura

### 3.1 Architettura dei Sottodomini

| Sottodominio | Funzione | Dipende da sycren-app |
|---|---|---|
| `app.sycren.com` | Hub centrale: SSO, dashboard, billing, notifiche | — (è il punto di ingresso) |
| `studio.sycren.com` | Gestione progetti, preventivi, client portal | Sì — SSO, notifiche, billing |
| `cloud.sycren.com` | Hosting, VPS, deploy, monitoring | Sì — SSO, notifiche, billing |
| `domains.sycren.com` | Registrar, DNS, rinnovi | Sì — SSO, notifiche, billing |
| `security.sycren.com` | WAF, monitoring, alerting | Sì — SSO, notifiche, billing |

### 3.2 Flusso SSO

```
Utente → app.sycren.com/login → Supabase Auth (email/OAuth)
  → JWT emesso con sessione cross-subdomain
  → Reindirizzamento a divisione richiesta (studio/cloud/domains/security)
  → Verifica JWT via middleware condiviso (sycren-db)
  → Accesso concesso
```

- Il JWT contiene: `user_id`, `role`, `session_id`, `iat`, `exp`
- Validità JWT: 24 ore con refresh token a 7 giorni
- Cookie di sessione condiviso su `.sycren.com` (cross-subdomain)
- Logout globale: invalidazione di tutte le sessioni attive

### 3.3 Architettura del Database

Lo schema del database è centralizzato in `sycren-db` (pacchetto Prisma condiviso). `sycren-app` consuma questo schema per tutte le operazioni.

**Entità principali gestite da sycren-app:**
- `User` — profilo utente, preferenze, ruolo
- `Session` — sessioni attive, JWT refresh token
- `Organization` — account business con membri multipli
- `OrganizationMember` — associazione utente → organizzazione con ruolo
- `Subscription` — servizi attivi per organizzazione (per divisione)
- `Invoice` — fatture emesse per servizi
- `PaymentMethod` — metodi di pagamento salvati
- `Ticket` — richieste di supporto categorizzate
- `Notification` — notifiche generate da eventi cross-divisione
- `NotificationPreference` — preferenze canale per tipo di notifica
- `AuditLog` — log di accesso e operazioni sensibili

---

## 4. Requisiti Funzionali — Dettaglio Feature

### 4.1 Autenticazione e Identity (SSO)

| ID | Requisito | Priorità |
|---|---|---|
| AUTH-01 | Registrazione con email + password via Supabase Auth | P0 |
| AUTH-02 | Login con email/password e OAuth (Google, GitHub) | P0 |
| AUTH-03 | JWT session management con refresh token | P0 |
| AUTH-04 | SSO cross-subdomain su tutti i servizi Sycren | P0 |
| AUTH-05 | Two-factor authentication (2FA) via TOTP per account business | P1 |
| AUTH-06 | Password reset flow (email via Resend) | P0 |
| AUTH-07 | Logout globale (invalida tutte le sessioni attive) | P1 |
| AUTH-08 | Sessioni attive visibili con possibilità di revoca singola | P1 |
| AUTH-09 | Access log completo (IP, user-agent, timestamp, servizio) | P1 |
| AUTH-10 | Blocco temporaneo dopo 5 tentativi falliti | P1 |

**Flusso di registrazione:**
1. Utente inserisce email + password → Supabase Auth crea user
2. Email di verifica inviata via Resend
3. Dopo verifica, redirect a onboarding: nome, organizzazione, ruolo
4. Profilo creato in `sycren-db` → User + Organization
5. JWT emesso, redirect a dashboard

### 4.2 Dashboard Principale

| ID | Requisito | Priorità |
|---|---|---|
| DASH-01 | Panoramica stato servizi attivi per ogni divisione | P0 |
| DASH-02 | Widget personalizzabili per divisione (drag & drop layout) | P1 |
| DASH-03 | Widget "Server Status" per clienti Cloud (uptime, CPU, RAM) | P1 |
| DASH-04 | Widget "Domains Overview" (scadenze, stato rinnovo) | P1 |
| DASH-05 | Widget "Security Alerts" (ultimi eventi, threat level) | P1 |
| DASH-06 | Widget "Project Progress" per clienti Studios (milestone, % completamento) | P1 |
| DASH-07 | Quick actions contestuali: Deploy, Rinnova Dominio, Apri Ticket, Richiedi Preventivo | P0 |
| DASH-08 | Barra di ricerca globale (servizi, fatture, ticket, domini) | P1 |
| DASH-09 | Feed notifiche unificato in tempo reale | P0 |
| DASH-10 | Indicatore "notifiche non lette" persistente nel sidebar | P0 |

**Layout dashboard:**
- Sidebar sinistra: navigazione principale (Dashboard, Billing, Support, Settings per divisione)
- Header superiore: breadcrumb, ricerca globale, avatar utente + menu
- Area centrale: griglia widget personalizzabile (layout salvato in DB per utente)
- Sidebar destra (opzionale): notification feed in tempo reale

### 4.3 Billing e Pagamenti

| ID | Requisito | Priorità |
|---|---|---|
| BILL-01 | Integrazione Stripe per pagamenti (Checkout + Customer Portal) | P0 |
| BILL-02 | Storico fatture completo per tutti i servizi | P0 |
| BILL-03 | Download fattura in PDF | P0 |
| BILL-04 | Gestione metodi di pagamento: carta, SEPA, bonifico | P0 |
| BILL-05 | Fatturazione unificata (tutti i servizi in un'unica fattura mensile) | P1 |
| BILL-06 | Fatturazione per servizio singolo | P1 |
| BILL-07 | Preventivi digitali con accettazione in-app (firma elettronica) | P1 |
| BILL-08 | Alert soglie consumo: cloud compute, storage, banda | P1 |
| BILL-09 | Cronologia pagamenti con stato (pagato, in attesa, fallito) | P0 |
| BILL-10 | Rinnovo automatico servizi con notifica pre-scadenza | P1 |
| BILL-11 | Cambio piano/subscription in self-service | P2 |
| BILL-12 | Fatturazione IVA (quando P.IVA attiva, Fase 2) | P1 |

**Flusso di pagamento integrato:**
1. Stripe Customer Portal → gestione metodi pagamento
2. Webhook Stripe → aggiornamento stato fattura in DB
3. Email di conferma pagamento via Resend
4. Notifica push in-app per pagamento ricevuto

### 4.4 Supporto e Ticket

| ID | Requisito | Priorità |
|---|---|---|
| SUPP-01 | Sistema di ticketing: creazione, visualizzazione, risposta | P0 |
| SUPP-02 | Categorizzazione ticket per divisione (Studios, Cloud, Domains, Security, Generale) | P0 |
| SUPP-03 | Priorità ticket: Low, Medium, High, Urgent | P0 |
| SUPP-04 | Allegati file ai ticket (max 10MB) | P1 |
| SUPP-05 | Stato ticket: Open, In Progress, Waiting Customer, Resolved, Closed | P0 |
| SUPP-06 | Notifica email al cliente su ogni risposta | P0 |
| SUPP-07 | Knowledge base integrata: articoli per categoria | P1 |
| SUPP-08 | Ricerca nella knowledge base con full-text search | P1 |
| SUPP-09 | Live chat con il team (orario lavorativo, Fase 2) | P2 |
| SUPP-10 | SLA visibili nel ticket (tempo di risposta previsto per priorità) | P2 |
| SUPP-11 | Ticket history: tutte le interazioni passate del cliente | P1 |

### 4.5 Notifiche

| ID | Requisito | Priorità |
|---|---|---|
| NOTIF-01 | Feed notifiche unificato in-app (tempo reale via polling/SSE) | P0 |
| NOTIF-02 | Notifiche push via browser (Notification API) | P1 |
| NOTIF-03 | Notifiche email transazionali via Resend | P0 |
| NOTIF-04 | Notifiche Telegram configurabili (bot personale) | P1 |
| NOTIF-05 | Preferenze notifica per tipo di evento e canale | P0 |
| NOTIF-06 | Tipi di evento: scadenza dominio, alert sicurezza, aggiornamento progetto, nuova fattura, risposta ticket, deploy completato, backup fallito | P0 |
| NOTIF-07 | Raggruppamento notifiche per divisione | P1 |
| NOTIF-08 | Marcatura lettura/non letta | P0 |
| NOTIF-09 | Notifiche silenziabili (modalità "Do Not Disturb") | P2 |

### 4.6 Gestione Ruoli e Permessi

| ID | Requisito | Priorità |
|---|---|---|
| ROLE-01 | Ruoli predefiniti: Owner, Admin, Developer, Viewer | P0 |
| ROLE-02 | Owner: accesso completo, gestione billing, aggiunta/rimozione membri | P0 |
| ROLE-03 | Admin: gestione servizi, ticket, utenti (no eliminazione account) | P0 |
| ROLE-04 | Developer: accesso tecnico (log, deploy, monitoring), no billing | P0 |
| ROLE-05 | Viewer: sola lettura su tutte le divisioni | P0 |
| ROLE-06 | Invito membri tramite email con link di accettazione | P1 |
| ROLE-07 | Revoca accesso membro con disconnessione forzata | P1 |
| ROLE-08 | Ruoli personalizzati con permessi granulari | P2 |
| ROLE-09 | Audit log delle modifiche ai permessi | P1 |

### 4.7 Impostazioni e Gestione Profilo

| ID | Requisito | Priorità |
|---|---|---|
| SETT-01 | Modifica profilo: nome, avatar, email, password | P0 |
| SETT-02 | Preferenze tema: Dark, Light, Black | P0 |
| SETT-03 | Preferenze notifica per tipo di evento | P0 |
| SETT-04 | Gestione API keys (per integrazioni esterne) | P1 |
| SETT-05 | Impostazioni organizzazione: nome, logo, indirizzo | P1 |
| SETT-06 | Esportazione dati utente (GDPR compliance) | P2 |
| SETT-07 | Eliminazione account con conferma e periodo di grazia | P1 |

---

## 5. Stack Tecnologico

| Layer | Tecnologia | Versione | Note |
|---|---|---|---|
| Framework | Next.js | 14+ (App Router) | Server Components + API Routes |
| Linguaggio | TypeScript | 5+ (strict mode) | Strict mode obbligatorio |
| Styling | Tailwind CSS | 3.4+ | Design token condivisi via `sycren-design-system` |
| ORM | Prisma | 5+ | Schema centralizzato in `sycren-db` |
| Database | PostgreSQL | 15+ | Hosted su Supabase (gratuito Fase 0) |
| Auth | Supabase Auth | Latest | Email/password + OAuth + JWT |
| Pagamenti | Stripe | Latest | Checkout + Customer Portal + Webhooks |
| Email | Resend | Latest | Transazionali: verifica, notifiche, fatture |
| Icone | Lucide React | Latest | Consistente con landing page |
| Tabelle/Analytics | TanStack Table | Latest | Per fatture, ticket, log |
| Form | React Hook Form + Zod | Latest | Validazione form lato client e server |
| Tema | next-themes | Latest | Dark/Light/Black con localStorage |
| Testing | Vitest + Playwright | Latest | Unit test + E2E |

### Variabili d'Ambiente

```bash
# Supabase
NEXT_PUBLIC_SUPABASE_URL=https://xxxxx.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=eyJxxx...

# Auth
JWT_SECRET=your-jwt-secret-here
JWT_EXPIRY=24h
JWT_REFRESH_EXPIRY=7d

# Stripe
STRIPE_SECRET_KEY=sk_live_xxx
NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY=pk_live_xxx
STRIPE_WEBHOOK_SECRET=whsec_xxx

# Resend
RESEND_API_KEY=re_xxx
NOTIFICATION_EMAIL_FROM=noreply@sycren.com
SUPPORT_EMAIL_TO=support@sycren.com

# Environment
NEXT_PUBLIC_SITE_URL=https://app.sycren.com
NEXT_PUBLIC_LAUNCH_MODE=WAITLIST  # WAITLIST | LIVE
```

---

## 6. Requisiti di Design

### 6.1 Sistema Tema — Tre Varianti

Allineato alla landing page `sycren.com` per consistenza del brand:

| Variante | Sfondo | Accent | Testo Primario | Testo Secondario |
|---|---|---|---|---|
| **Dark (Default)** | `#0A0A0F` | `#6366F1` (Indigo) | `#F0F0FF` | `#8A8AA0` |
| **Light** | `#FAFAFA` | `#4F46E5` (Indigo) | `#0F0F1A` | `#6B6B80` |
| **Black** | `#000000` | `#FFFFFF` (Bianco) | `#FFFFFF` | `#888888` |

### 6.2 Token Design (da sycren-design-system)

- `--color-bg-primary` — sfondo pagina
- `--color-bg-secondary` — sfondo card/section
- `--color-bg-tertiary` — sfondo hover/active
- `--color-text-primary` — testo principale
- `--color-text-secondary` — testo secondario
- `--color-accent` — colore accent (indigo)
- `--color-accent-hover` — accent hover
- `--color-border` — bordi e separatori
- `--color-success` — verde per stato ok
- `--color-warning` — ambra per warning
- `--color-error` — rosso per errori
- `--color-info` — azzurro per info
- `--sidebar-width` — 280px
- `--radius-sm` — 6px
- `--radius-md` — 10px
- `--radius-lg` — 16px

### 6.3 Requisiti UI

- **Responsive:** Sidebar → bottom navigation su mobile (< 768px)
- **Sidebar:** icona + label, sezione attiva highlighted, raggruppamento per divisione
- **Header:** breadcrumb dinamico, ricerca globale, avatar + dropdown utente
- **Widget griglia:** drag & drop per riordino, salvataggio layout in DB
- **Stati vuoti:** illustrazione + messaggio + CTA per sezioni senza dati
- **Caricamento:** skeleton loading per ogni card/widget
- **Errori:** toast notification + retry button
- **Accessibilità:** WCAG AA, focus visible, screen reader labels, aria attributes
- **Animazioni:** Framer Motion per transizioni pagina, hover card, apertura menu. Rispettare `prefers-reduced-motion`

---

## 7. Punti di Integrazione

### 7.1 sycren-db (Database Foundation)

- Importare schema Prisma centralizzato da `@sycren/db`
- Usare migrazioni e tipi condivisi
- Estendere schema con entità specifiche di sycren-app se necessario

### 7.2 studio.sycren.com (Client Portal Studios)

- SSO: login via sycren-app → redirect a studio con JWT
- Notifiche: eventi progetto → API notifiche sycren-app
- Billing: preventivi approvati → generazione fattura via sycren-app
- Widget dashboard: project progress, ultimi milestone

### 7.3 cloud.sycren.com (Cloud Lite)

- SSO: login via sycren-app → redirect a cloud con JWT
- Notifiche: alert uptime, deploy status → feed notifiche sycren-app
- Billing: consumo risorse → fatturazione sycren-app
- Widget dashboard: server status, CPU/RAM usage, uptime

### 7.4 domains.sycren.com (Domains)

- SSO: login via sycren-app → redirect a domains con JWT
- Notifiche: scadenza dominio → feed notifiche sycren-app
- Billing: rinnovo dominio → fatturazione sycren-app
- Widget dashboard: domini in scadenza, stato rinnovo

### 7.5 security.sycren.com (Security)

- SSO: login via sycren-app → redirect a security con JWT
- Notifiche: alert sicurezza → feed notifiche sycren-app + Telegram
- Billing: subscription security → fatturazione sycren-app
- Widget dashboard: threat level, ultimi eventi, report status

### 7.6 Stripe (Payment Processing)

- Webhook per eventi: `checkout.session.completed`, `invoice.paid`, `invoice.payment_failed`
- Stripe Customer Portal per gestione metodi di pagamento
- Price/Product ID in environment variables per ambiente (test/production)

### 7.7 Resend (Email Transazionale)

- Template email: verifica account, reset password, notifica ticket, fattura, alert
- Domain verificato: `sycren.com`
- Categorie: `transactional`, `notification`, `marketing` (futuro)

---

## 8. Criteri di Completamento

### P0 — Must Have (MVP, Lancio Fase 2)

- [ ] Registrazione e login funzionanti (email/password + OAuth)
- [ ] SSO cross-subdomain verificato su almeno 2 divisioni
- [ ] Dashboard principale con widget di base (stato servizi)
- [ ] Feed notifiche unificato funzionante
- [ ] Integrazione Stripe base (pagamento singolo, storico fatture)
- [ ] Sistema ticket: creazione, visualizzazione, risposta
- [ ] Gestione ruoli (Owner, Admin, Developer, Viewer)
- [ ] Tema Dark/Light/Black con persistenza
- [ ] Layout responsive (desktop + mobile)

### P1 — Should Have (Post-Lancio, Entro 3 mesi)

- [ ] 2FA per account business
- [ ] Widget personalizzabili con drag & drop
- [ ] Fatturazione unificata multi-servizio
- [ ] Preventivi digitali con accettazione in-app
- [ ] Notifiche push browser + Telegram
- [ ] Knowledge base integrata
- [ ] Ricerca globale (servizi, fatture, ticket, domini)
- [ ] Gestione API keys
- [ ] Invito membri tramite email
- [ ] Access log e audit trail

### P2 — Nice to Have (Roadmap Futura)

- [ ] Live chat con il team
- [ ] Ruoli personalizzati con permessi granulari
- [ ] Cambio piano/subscription in self-service
- [ ] Notifiche "Do Not Disturb" programmabili
- [ ] Esportazione dati GDPR
- [ ] Integrazione calendario (scadenze, milestone)

### Metriche di Qualità

| Metrica | Target |
|---|---|
| Lighthouse Performance (mobile) | ≥ 85 |
| LCP | < 2.5s |
| Tempo di risposta API (p95) | < 200ms |
| Copertura test unitari | > 70% |
| Bundle JS iniziale (gzipped) | < 200KB |
| Uptime | > 99.5% |
| Tempo di login (incluso redirect SSO) | < 3s |

---

## Related Notes

| Document | Description |
|---|---|
| [[app.README]] | sycren-app overview |
| [[app.todo]] | Implementation plan |
| [[db.requirements\|sycren-db requirements]] | Shared schema for User, Notification, Invoice |
| [[design-system.requirements\|Design System requirements]] | UI components consumed by app |
| [[studio.requirements\|sycren-studio requirements]] | Integrates via SSO |
| [[cloud.requirements\|sycren-cloud requirements]] | SSO consumer |
| [[domains.requirements\|sycren-domains requirements]] | SSO consumer |
| [[security.requirements\|sycren-security requirements]] | SSO consumer |
| [[foundation/ROADMAP\|Bootstrap Roadmap]] | Build order and timeline |

---

*Sycren — sycren-app PRD v1.0 — Giugno 2026*
*Documento interno. Non distribuire senza autorizzazione scritta.*
