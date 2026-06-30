# sycren-app — TODO Operativo
> PRD v1.0 · Giugno 2026 · Fase 0 (Pre-commerciale) · Allineato a Bootstrap Roadmap v1.3

> **Età di sviluppo:** 🕐 16–17 anni
> **Responsabile:** Angelo (Lead architettura + backend), Cosimo (UI/UX + frontend supporto)
> **Dipendenza:** `sycren-db` deve essere completato prima di iniziare sycren-app

---

## Contesto (dalla Roadmap)

- Entrambi i founder hanno **16 anni 🕐**, nessuna partita IVA — Soluzione B: costruire tutto ora, vendere al **18° compleanno 🎂**
- `sycren-app` è il **4° modulo** nell'ordine di costruzione: Landing → sycren-db → Studio → **App** → Cloud → Domains → Security
- Si sviluppa in parallelo a `studio.sycren.com` (mese 2-3, età 16-17)
- L'app è l'hub centrale SSO: tutti gli altri moduli dipendono da essa per autenticazione, notifiche e billing
- Deve essere funzionante e testata prima del lancio commerciale (18 anni)
- Stima: sviluppo su **Mese 2-3** (età 16-17), con MVP completo entro il pre-lancio

### Dipendenze tra moduli

```
sycren-db (completato) ──► sycren-app ──► studio.sycren.com (SSO)
                          ├──► cloud.sycren.com (SSO)
                          ├──► domains.sycren.com (SSO)
                          └──► security.sycren.com (SSO)
```

---

## Setup Iniziale

### Repository e Infrastruttura

- [ ] Creare repository `sycren-app` su GitHub (privato)
- [ ] Inizializzare Next.js 14+ con App Router e TypeScript strict
- [ ] Configurare Tailwind CSS 3.4+ con design token da `sycren-design-system`
- [ ] Installare dipendenze: Prisma client, Supabase JS SDK, Stripe SDK, Resend SDK, Lucide React, next-themes, React Hook Form, Zod, TanStack Table, Framer Motion
- [ ] Configurare `next.config.js` (immagini, headers CORS, redirect)
- [ ] Configurare variabili d'ambiente in `.env.local` e `.env.example`
- [ ] Impostare struttura cartelle (vedi sotto)
- [ ] Collegare repository a Vercel per deploy automatico
- [ ] Configurare dominio `app.sycren.com` su Cloudflare (DNS + SSL)
- [ ] Abilitare Supabase Auth e creare progetto su Supabase
- [ ] Configurare Stripe (prodotti, webhook endpoint, test mode)
- [ ] Verificare dominio `sycren.com` su Resend per email transazionali

### Struttura Cartelle Consigliata

```
src/
├── app/                          # App Router pages
│   ├── (auth)/                   # Auth layout group
│   │   ├── login/
│   │   ├── register/
│   │   ├── forgot-password/
│   │   └── reset-password/
│   ├── (dashboard)/              # Dashboard layout group
│   │   ├── dashboard/
│   │   ├── billing/
│   │   ├── support/
│   │   ├── settings/
│   │   └── notifications/
│   ├── api/                      # API Routes
│   │   ├── auth/
│   │   ├── billing/
│   │   ├── support/
│   │   ├── notifications/
│   │   └── webhooks/
│   └── layout.tsx
├── components/
│   ├── ui/                       # UI primitives (button, card, input, etc.)
│   ├── layout/                   # Sidebar, Header, Footer
│   ├── widgets/                  # Dashboard widgets
│   ├── billing/                  # Invoice list, payment method card
│   └── support/                  # Ticket card, chat bubble
├── lib/
│   ├── db.ts                     # Prisma client singleton
│   ├── auth.ts                   # Supabase client + JWT helpers
│   ├── stripe.ts                 # Stripe client helpers
│   ├── resend.ts                 # Email client
│   ├── notifications.ts          # Notification dispatch logic
│   └── utils.ts                  # Shared utilities
├── hooks/                        # Custom React hooks
├── types/                        # Shared TypeScript types
├── middleware.ts                 # Next.js middleware for auth + redirect
└── styles/
    └── globals.css               # CSS custom properties per tema
```

---

## Fase 1: Autenticazione e SSO

### Setup Supabase Auth

- [ ] Abilitare provider email/password su Supabase
- [ ] Abilitare provider OAuth (Google, GitHub) su Supabase
- [ ] Configurare URL di redirect Supabase per `app.sycren.com`
- [ ] Creare client Supabase lato server (Server Component)
- [ ] Creare client Supabase lato browser (Client Component)
- [ ] Testare flusso registrazione → verifica email → login

### Middleware e Session Management

- [ ] Implementare middleware Next.js per proteggere rotte dashboard
- [ ] Gestire JWT: creazione, verifica, refresh automatico
- [ ] Configurare cookie di sessione per subdomain `.sycren.com`
- [ ] Implementare redirect SSO: login → dashboard con redirect_to parametro
- [ ] Implementare endpoint `/api/auth/sso` per verifica JWT cross-subdomain
- [ ] Testare flusso SSO: login su app → redirect a studio/cloud con token valido
- [ ] Gestire logout globale (invalida tutte le sessioni)

### UI Auth

- [ ] Pagina Login (email/password, OAuth buttons, "Forgot password?" link)
- [ ] Pagina Registrazione (email, password, confirm password, terms checkbox)
- [ ] Pagina Forgot Password (inserimento email → email reset link)
- [ ] Pagina Reset Password (nuova password + conferma)
- [ ] Componente AuthGuard (layout wrapper per rotte protette)
- [ ] Stato di caricamento auth (skeleton spinner durante verifica sessione)
- [ ] Messaggi di errore localizzati (email già registrata, credenziali errate)

### 2FA (P1)

- [ ] Implementare TOTP setup (QR code, secret generation)
- [ ] Aggiungere pagina verifica 2FA durante login
- [ ] Gestire recovery codes (generazione, visualizzazione, download)
- [ ] UI toggle 2FA in impostazioni account

### Access Log e Sessioni Attive

- [ ] Registrare ogni accesso (IP, user-agent, timestamp, metodo auth)
- [ ] Pagina "Sessioni attive" con elenco dispositivi/browser
- [ ] Bottone "Revoca sessione" su ogni sessione attiva
- [ ] Notifica email su login da nuovo dispositivo (P1)

---

## Fase 2: Dashboard Principale

### Layout

- [ ] Sidebar sinistra con navigazione per sezioni e divisioni
- [ ] Header superiore: logo Sycren, breadcrumb, ricerca globale (placeholder), avatar + dropdown
- [ ] Sidebar destra notifiche (toggle visibility)
- [ ] Bottom navigation per mobile (< 768px)
- [ ] Transizioni pagina con Framer Motion (layout animato)
- [ ] Breadcrumb dinamico basato su route corrente

### Widget Sistema

- [ ] Griglia widget con layout grid responsive (2 colonne desktop, 1 mobile)
- [ ] Widget "Servizi Attivi" — riepilogo card per ogni divisione usata
- [ ] Stato vuoto: "Nessun servizio attivo. Esplora i nostri servizi." → link a landing
- [ ] Skeletro caricamento per ogni widget durante fetch dati
- [ ] Drag & drop riordino widget con persistenza layout (P1)
- [ ] Widget configurabili: scegli quali mostrare/nascondere (P1)

### Widget Specifici per Divisione

- [ ] Widget "Cloud Status" — stato server (online/offline), uptime %, CPU/RAM gauge
- [ ] Widget "Domains Overview" — elenco domini con data scadenza, badge status
- [ ] Widget "Security Alerts" — ultimi 5 eventi, threat level indicator (verde/giallo/rosso)
- [ ] Widget "Studio Projects" — progetti attivi con % completamento, ultimo milestone
- [ ] Ogni widget linka alla rispettiva divisione per dettaglio completo

### Quick Actions

- [ ] Barra azioni rapide sotto l'header
- [ ] Bottone "New Ticket" → apre form ticket
- [ ] Bottone "Deploy" → shortcut a cloud.sycren.com (se cliente Cloud)
- [ ] Bottone "Renew Domain" → shortcut a domains.sycren.com (se cliente Domains)
- [ ] Bottone "Request Quote" → reindirizza a studio.sycren.com
- [ ] Bottone "Pay Invoice" → shortcut a pagina billing (se fattura in attesa)

### Ricerca Globale (P1)

- [ ] Barra di ricerca nell'header con shortcut Cmd+K / Ctrl+K
- [ ] Ricerca full-text su: fatture, ticket, domini, progetti, server
- [ ] Risultati raggruppati per categoria con anteprima
- [ ] Click su risultato → naviga alla pagina specifica

---

## Fase 3: Billing e Pagamenti

### Integrazione Stripe

- [ ] Creare prodotti/prezzi su Stripe Dashboard per ogni servizio
- [ ] Implementare Stripe Checkout per acquisto singolo servizio
- [ ] Implementare Stripe Customer Portal per gestione metodi pagamento
- [ ] Configurare webhook Stripe: `checkout.session.completed`, `invoice.paid`, `invoice.payment_failed`
- [ ] Verificare firma webhook Stripe per sicurezza
- [ ] Gestire ambienti: test mode (Sviluppo) vs live mode (Produzione)

### UI Billing

- [ ] Pagina "Billing Overview" — riepilogo spese mese corrente, metodi pagamento, fatture recenti
- [ ] Lista fatture con filtro per stato (paid, pending, failed) e data
- [ ] Dettaglio fattura: numero, data, importo, servizi, PDF download
- [ ] Pagina "Payment Methods" — carte salvate, SEPA, aggiungi/rimuovi
- [ ] Pagina "Usage & Limits" — consumo risorse cloud (CPU, storage, banda) con barre progresso
- [ ] Pagina "Subscription" — piani attivi per divisione, cambio piano (P2)
- [ ] Badge sul billing in sidebar se ci sono fatture in sospeso

### Preventivi Digitali (P1)

- [ ] Visualizzazione preventivi generati da `studio.sycren.com`
- [ ] Bottone "Accept Quote" con conferma
- [ ] Generazione PDF del preventivo
- [ ] Stato: Draft, Sent, Accepted, Rejected, Expired
- [ ] Notifica al team Sycren su accettazione preventivo

### Alert Soglie (P1)

- [ ] Configurazione soglie per: CPU usage, storage, banda
- [ ] Notifica automatica al raggiungimento dell'80% della soglia
- [ ] Alert email + notifica in-app + Telegram (se configurato)

---

## Fase 4: Supporto e Notifiche

### Sistema Ticket

- [ ] Pagina "Support" con lista ticket (tab: Open, In Progress, Resolved, Closed)
- [ ] Form creazione ticket: titolo, descrizione, categoria (divisione), priorità, allegati
- [ ] Pagina dettaglio ticket: messaggi (thread chronologico), stato, priorità
- [ ] Form risposta ticket: textarea + allegati
- [ ] Cambio stato ticket da parte del cliente (risolto, riaperto)
- [ ] Badge contatore ticket aperti nel sidebar
- [ ] Notifica email al cliente su ogni risposta del team
- [ ] Notifica email al team su ogni nuovo ticket o risposta cliente
- [ ] Templated email per conferma apertura ticket

### Knowledge Base (P1)

- [ ] Pagina "Knowledge Base" con articoli categorizzati per divisione
- [ ] Ricerca full-text articoli
- [ ] Visualizzazione articolo: titolo, contenuto (Markdown), data, categoria
- [ ] Articolo correlati in fondo
- [ ] Stato vuoto: "Nessun articolo trovato" + suggerimento aprire ticket

### Notifiche Unificate

- [ ] Feed notifiche in-app (sidebar destra o dropdown)
- [ ] Polling ogni 30 secondi (incrementale) o SSE per real-time
- [ ] Tipi di notifica con icona e colore distinti:
  - 🔵 Info: aggiornamento progetto, deploy completato
  - 🟢 Success: pagamento ricevuto, rinnovo completato
  - 🟡 Warning: scadenza imminente, soglia consumo
  - 🔴 Error: pagamento fallito, backup fallito, alert sicurezza
- [ ] Marcatura singola letta / "Mark all as read"
- [ ] Badge contatore non lette persistente in sidebar e favicon
- [ ] Preferenze notifica: pagina Settings con toggle per tipo × canale
- [ ] Canali: In-app (sempre attivo), Email, Push browser, Telegram

### Integrazione Telegram (P1)

- [ ] Bot Telegram per Sycren Notifications
- [ ] Flusso di collegamento: genera codice → utente invia comando al bot
- [ ] Invio notifica Telegram per eventi selezionati
- [ ] Comando bot: `/start`, `/status`, `/help`

---

## Fase 5: Gestione Ruoli e Organizzazioni

### Sistema Ruoli

- [ ] Seed DB con ruoli predefiniti: Owner, Admin, Developer, Viewer
- [ ] Implementare guardie route lato server per verifica permessi
- [ ] Implementare guardie UI (nascondi bottoni/elementi non autorizzati)
- [ ] Pagina "Team" — lista membri organizzazione con ruolo
- [ ] Invito nuovo membro: email → link accettazione → assegnazione ruolo
- [ ] Modifica ruolo membro esistente (solo Owner)
- [ ] Rimozione membro con conferma + disconnessione forzata
- [ ] Abbandono organizzazione (se non Owner)

### Impostazioni Organizzazione

- [ ] Pagina "Organization Settings" — nome, logo, indirizzo, VAT number
- [ ] Pagina "API Keys" — generazione, revoca, ultimo utilizzo
- [ ] Audit Log — tabella eventi: azione, utente, timestamp, dettagli

---

## Integrazione Divisioni

### connessione a sycren-db

- [ ] Installare dipendenza `@sycren/db` (pacchetto Prisma condiviso)
- [ ] Verificare compatibilità schema (entità User, Session, Organization, etc.)
- [ ] Configurare Prisma Client per ambiente di sviluppo (Supabase)
- [ ] Eseguire migrazioni iniziali (se necessarie)

### connessione a studio.sycren.com

- [ ] Implementare endpoint SSO: `/api/auth/sso/studio` → genera JWT valido per studio
- [ ] Ricevere eventi da studio (progetto creato, milestone completato) → notifica in-app
- [ ] Widget "Project Progress" consuma API studio.sycren.com
- [ ] Testare flusso: login app → click "Studio" in sidebar → redirect autenticato

### connessione a cloud.sycren.com

- [ ] Implementare endpoint SSO: `/api/auth/sso/cloud` → genera JWT valido per cloud
- [ ] Ricevere eventi da cloud (server down, deploy completato) → notifica in-app
- [ ] Widget "Cloud Status" consuma API cloud.sycren.com
- [ ] Testare flusso: login app → click "Cloud" → redirect autenticato

### connessione a domains.sycren.com

- [ ] Implementare endpoint SSO: `/api/auth/sso/domains` → genera JWT valido
- [ ] Ricevere eventi da domains (scadenza imminente, rinnovo completato) → notifica
- [ ] Widget "Domains" consuma API domains.sycren.com
- [ ] Testare flusso: login app → click "Domains" → redirect autenticato

### connessione a security.sycren.com

- [ ] Implementare endpoint SSO: `/api/auth/sso/security` → genera JWT valido
- [ ] Ricevere eventi da security (attacco rilevato, report generato) → notifica
- [ ] Widget "Security Alerts" consuma API security.sycren.com
- [ ] Testare flusso: login app → click "Security" → redirect autenticato

---

## Design e UI

### Sistema Tema

- [ ] Definire CSS custom properties per Dark, Light, Black (allineato a landing.md)
- [ ] Implementare ThemeProvider con next-themes
- [ ] Toggle tema in Settings (tre bottoni: Dark, Light, Black)
- [ ] Persistere scelta tema in localStorage
- [ ] Default: `prefers-color-scheme` alla prima visita → Dark se non specificato
- [ ] Verificare contrasto WCAG AA per tutti e tre i temi

### Componenti UI Condivisi

- [ ] Button (primario, secondario, ghost, danger, con icona, loading state)
- [ ] Card (con titolo, contenuto, footer opzionale)
- [ ] Input, Textarea, Select (con label, errore, helper text)
- [ ] Modal/Dialog (confirmazione, form, alert)
- [ ] Toast/Notification (success, error, warning, info)
- [ ] Badge (per stati: active, pending, expired, etc.)
- [ ] Table (con sorting, filtering, pagination via TanStack Table)
- [ ] Tabs (per categorizzare contenuti)
- [ ] Dropdown Menu (avatar menu, azioni tabella)
- [ ] Skeleton Loader (per ogni tipo di card/widget)
- [ ] Empty State (illustrazione + messaggio + CTA)
- [ ] Pagination (per liste lunghe: fatture, ticket, log)

### Design Tokens (CSS Custom Properties)

```css
:root {
  --color-bg-primary: #0A0A0F;
  --color-bg-secondary: #12121A;
  --color-bg-tertiary: #1A1A26;
  --color-text-primary: #F0F0FF;
  --color-text-secondary: #8A8AA0;
  --color-accent: #6366F1;
  --color-accent-hover: #5457E5;
  --color-border: #2A2A3A;
  --color-success: #34D399;
  --color-warning: #FBBF24;
  --color-error: #EF4444;
  --color-info: #38BDF8;
}
/* Light e Black variants da aggiungere */
```

### Responsive

- [ ] Sidebar collassabile su tablet (< 1024px) — icon-only mode
- [ ] Bottom navigation su mobile (< 768px) con 5 icone: Dashboard, Billing, Support, Notifications, Settings
- [ ] Widget griglia: 2 colonne → 1 colonna su mobile
- [ ] Tabelle scrollabili orizzontalmente su mobile
- [ ] Touch target minimo 44×44px su mobile
- [ ] Test su viewport: 375px (mobile), 768px (tablet), 1280px (desktop), 1920px (wide)

---

## API Routes

### Auth

- [ ] `POST /api/auth/register` — registrazione nuovo utente
- [ ] `POST /api/auth/login` — login (delega a Supabase)
- [ ] `POST /api/auth/logout` — logout globale
- [ ] `POST /api/auth/refresh` — refresh JWT token
- [ ] `POST /api/auth/sso` — genera JWT per subdomain specifico
- [ ] `POST /api/auth/2fa/setup` — attiva 2FA (P1)
- [ ] `POST /api/auth/2fa/verify` — verifica codice 2FA (P1)

### Billing

- [ ] `GET /api/billing/invoices` — lista fatture (con filtro)
- [ ] `GET /api/billing/invoices/[id]` — dettaglio fattura + PDF
- [ ] `GET /api/billing/payment-methods` — metodi di pagamento
- [ ] `POST /api/billing/create-checkout` — crea Stripe Checkout session
- [ ] `POST /api/billing/create-portal` — crea Stripe Customer Portal link
- [ ] `POST /api/webhooks/stripe` — webhook Stripe events

### Support

- [ ] `GET /api/support/tickets` — lista ticket (con filtro stato/divisione)
- [ ] `POST /api/support/tickets` — crea nuovo ticket
- [ ] `GET /api/support/tickets/[id]` — dettaglio ticket + messaggi
- [ ] `POST /api/support/tickets/[id]/reply` — aggiunge risposta
- [ ] `POST /api/support/tickets/[id]/status` — cambia stato
- [ ] `GET /api/support/kb` — lista articoli knowledge base (P1)
- [ ] `GET /api/support/kb/[id]` — dettaglio articolo (P1)

### Notifications

- [ ] `GET /api/notifications` — lista notifiche (paginata)
- [ ] `PATCH /api/notifications/[id]/read` — marca come letta
- [ ] `PATCH /api/notifications/read-all` — marca tutte come lette
- [ ] `GET /api/notifications/preferences` — preferenze notifica
- [ ] `PUT /api/notifications/preferences` — aggiorna preferenze
- [ ] `POST /api/notifications/telegram/link` — genera codice linking Telegram (P1)

### Team & Settings

- [ ] `GET /api/team` — lista membri organizzazione
- [ ] `POST /api/team/invite` — invita nuovo membro
- [ ] `PATCH /api/team/[id]/role` — cambia ruolo membro
- [ ] `DELETE /api/team/[id]` — rimuovi membro
- [ ] `GET /api/settings/profile` — profilo utente
- [ ] `PUT /api/settings/profile` — aggiorna profilo
- [ ] `GET /api/settings/organization` — impostazioni organizzazione
- [ ] `PUT /api/settings/organization` — aggiorna organizzazione
- [ ] `GET /api/settings/api-keys` — lista API keys
- [ ] `POST /api/settings/api-keys` — genera API key
- [ ] `DELETE /api/settings/api-keys/[id]` — revoca API key

---

## Testing

### Unit Test (Vitest)

- [ ] Test utility auth (JWT verify, role check)
- [ ] Test validazione form (Zod schemas per ogni form)
- [ ] Test API helpers (Stripe, Resend mockati)
- [ ] Test utility funzioni (formatta data, currency, etc.)
- [ ] Test middleware auth (redirect se non autenticato)

### Integration Test

- [ ] Test flusso registrazione → verifica email → login
- [ ] Test flusso SSO (login app → redirect divisione)
- [ ] Test creazione ticket → risposta → cambio stato
- [ ] Test billing: creazione checkout → webhook → aggiornamento fattura
- [ ] Test gestione membri (invito → accettazione → cambio ruolo → rimozione)

### E2E Test (Playwright)

- [ ] Test login con credenziali valide/invalide
- [ ] Test navigazione dashboard (sidebar, widget, quick actions)
- [ ] Test creazione ticket completo
- [ ] Test visualizzazione fattura e download PDF
- [ ] Test cambio tema (Dark → Light → Black)
- [ ] Test responsivo (375px, 768px, 1280px)
- [ ] Test accessibilità (tab navigation, screen reader labels)

---

## Design e UI Checklist Finale

- [ ] Tema Dark/Light/Black funzionante e persistente
- [ ] Sidebar responsive: full su desktop, icon-only su tablet, bottom nav su mobile
- [ ] Breadcrumb dinamico su tutte le pagine
- [ ] Skeleton loading su ogni pagina/widget durante fetch
- [ ] Stato vuoto per ogni lista (fatture, ticket, notifiche, membri)
- [ ] Stato errore con retry button per chiamate API fallite
- [ ] Paginazione su liste > 20 elementi
- [ ] Toast notification per azioni (ticket creato, fattura pagata, etc.)
- [ ] Confirm dialog per azioni distruttive (rimuovi membro, revoca API key)
- [ ] Tastiera navigabile (Tab, Enter, Escape modali, Cmd+K ricerca)
- [ ] Prefers-reduced-motion: disabilita animazioni se attivo
- [ ] Focus visible ring su tutti gli elementi interattivi
- [ ] Label e aria-label su tutti gli elementi form

---

## Criteri di Verifica (Pre-Lancio Commerciale)

Prima del lancio (18 anni 🎂), verificare che:

### Funzionalità Core

- [ ] Registrazione e login funzionanti con email/password e OAuth
- [ ] SSO verificato: login su app → redirect a studio/cloud con sessione valida
- [ ] Logout globale funzionante (invalida sessioni su tutti i subdomain)
- [ ] Dashboard mostra servizi attivi delle divisioni collegate
- [ ] Feed notifiche mostra eventi in tempo reale
- [ ] Creazione ticket → risposta → chiusura → riapertura
- [ ] Fattura generata → visualizzata → download PDF
- [ ] Pagamento via Stripe → webhook → aggiornamento stato
- [ ] Invito membro → accettazione → ruolo assegnato correttamente

### Integrazioni

- [ ] Integrazione con `studio.sycren.com` (SSO + widget + notifiche)
- [ ] Integrazione con `cloud.sycren.com` (SSO + widget + notifiche)
- [ ] Integrazione con `domains.sycren.com` (SSO + widget + notifiche)
- [ ] Integrazione con `security.sycren.com` (SSO + widget + notifiche)
- [ ] Webhook Stripe riceve e processa eventi correttamente
- [ ] Email transazionali (Resend) consegnate con template corretto

### Performance e Qualità

- [ ] Lighthouse Performance ≥ 85 (mobile)
- [ ] LCP < 2.5s
- [ ] Nessun errore console (produzione)
- [ ] Tutti i form hanno validazione client-side + server-side
- [ ] Test E2E: flussi critici passano al 100%
- [ ] Test responsivo: 375px, 768px, 1280px, 1920px
- [ ] Accessibilità: tab navigation completa, label, aria attributes
- [ ] Bundle JS iniziale < 200KB gzipped
- [ ] API p95 response time < 200ms

### Deploy

- [ ] `app.sycren.com` live su Vercel con SSL
- [ ] DNS configurato su Cloudflare (A record, CNAME, SSL/TLS)
- [ ] Environment variables configurate su Vercel
- [ ] Webhook Stripe configurato per endpoint produzione
- [ ] Supabase project in production mode
- [ ] Database migrazioni applicate
- [ ] Monitoraggio errori configurato (Vercel Analytics + Sentry opzionale)

---

## Dopo il Lancio — Prossimi Step

Completata sycren-app e aperte le vendite, le priorità sono:

| # | Cosa | Quando |
|---|---|---|
| 1 | Onboarding primi clienti su `studio.sycren.com` + `app.sycren.com` | Mese 1 post-lancio |
| 2 | Raccolta feedback su dashboard e widget | Continuo |
| 3 | Implementazione feature P1 (2FA, drag & drop widget, preventivi) | Mese 2-3 post-lancio |
| 4 | Miglioramento performance (se necessario) | Continuo |
| 5 | `cloud.sycren.com` lite (VPS reselling) | Età 17 |
| 6 | `domains.sycren.com` | Età 17-18 |
| 7 | `security.sycren.com` | Età 18+ |

---

## Related Notes

| Document | Description |
|---|---|
| [[app.README]] | sycren-app overview |
| [[app.requirements]] | Full PRD |
| [[db.requirements\|sycren-db requirements]] | Blocking dependency |
| [[design-system.requirements\|Design System requirements]] | UI dependency |
| [[studio.README\|sycren-studio]] | Parallel development |
| [[cloud.README\|sycren-cloud]] | Downstream SSO consumer |
| [[domains.README\|sycren-domains]] | Downstream SSO consumer |
| [[security.README\|sycren-security]] | Downstream SSO consumer |
| [[foundation/ROADMAP\|Bootstrap Roadmap]] | Timeline — module #4 |

---

*Sycren — sycren-app TODO v1.0 · Giugno 2026 · Allineato a Bootstrap Roadmap v1.3*
*Documento interno. Non distribuire senza autorizzazione scritta.*
