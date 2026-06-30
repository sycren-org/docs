# Sycren Studios — Client Portal TODO
> PRD v1.0 · Giugno 2026 · 🕐 16 anni · Allineato a Bootstrap Roadmap v1.3

---

## Contesto (dalla Roadmap)

- Entrambi i founder hanno **16 anni 🕐**, nessuna partita IVA — Soluzione B: costruire tutto ora, vendere al **18° compleanno 🎂**
- `sycren-studio` è il **cuore commerciale** di Sycren — la prima divisione a generare ricavi
- Ordine di costruzione (dalla Roadmap v1.3): Landing → `sycren-db` → **Studio** → `sycren-app` → Cloud → Domains → Security
- `sycren-studio` è il **repository #3** dell'ecosistema, dopo landing page e database foundation
- Stima temporale: Settimana 4–8 per MVP, con milestone verificabili ogni 2 settimane
- `sycren-db` deve essere completato prima di iniziare sycren-studio (schema Prisma, migrazioni, tipi condivisi)

### Divisione del lavoro

| Area | Angelo | Cosimo |
|---|---|---|
| Architettura e backend (API, DB, Auth) | Lead | Supporto |
| UI/UX e frontend (componenti, pagine) | Supporto | Lead |
| File storage e upload | Lead | Supporto |
| Chat real-time | Lead | Supporto |
| Admin panel | Lead | Supporto |
| Integrazione Stripe | Lead | — |
| Deploy e CI/CD | Lead | Supporto |

---

## Setup Iniziale

- [ ] Inizializzare progetto Next.js 14 con App Router e TypeScript strict mode
- [ ] Configurare Tailwind CSS 3.4+ con design token (ereditati da design system condiviso)
- [ ] Installare dipendenze: Prisma Client, Zod, React Hook Form, Lucide React, date-fns
- [ ] Installare Supabase client library (`@supabase/supabase-js`, `@supabase/ssr`)
- [ ] Installare Resend SDK per email transazionali
- [ ] Configurare `next/font` con Geist Sans e Inter
- [ ] Creare `.env.example` con tutte le variabili d'ambiente
- [ ] Impostare struttura cartelle:
  ```
  src/
  ├── app/          (App Router pages and API routes)
  ├── components/   (UI components + feature components)
  ├── lib/          (utilities, API client, config)
  ├── hooks/        (React custom hooks)
  ├── types/        (TypeScript types, Zod schemas)
  └── styles/       (global CSS, design tokens)
  ```
- [ ] Configurare Vercel: collegare repo `sycren-studio`, impostare ENV, abilitare Analytics
- [ ] Configurare Cloudflare: DNS per `studio.sycren.com` (CNAME a Vercel), CDN, WAF base
- [ ] Configurare Supabase: creare progetto, ottenere URL e chiavi API
- [ ] Connettere `sycren-db` come pacchetto (npm workspace o git submodule): importare schema Prisma e tipi condivisi
- [ ] Eseguire `npx prisma migrate dev` con schema da `sycren-db`
- [ ] Verificare connessione al database Supabase

---

## Phase 1: MVP Foundation (16 anni)

### 1.1 Autenticazione e Layout Base

- [ ] Configurare Supabase Auth: sign-up (email + password), sign-in (email + password), magic link
- [ ] Creare layout base con sidebar navigazione e header
- [ ] Implementare middleware per route protette (redirect a `/login` se non autenticato)
- [ ] Creare pagina `/login` con form email/password
- [ ] Creare pagina `/register` per nuovi clienti (o redirect da landing page)
- [ ] Gestione sessione: persistenza cookie, refresh token, logout
- [ ] Pagina `/onboarding` — reindirizzamento per nuovi utenti senza progetto attivo
- [ ] Verificare flusso completo: register → login → onboarding → dashboard
- [ ] Test: registrazione nuovo utente, login, magic link, recupero password

### 1.2 Richiesta Preventivo (Quote Request)

- [ ] Creare pagina `/request-quote` con form strutturato
- [ ] Campi: nome, email, azienda (opzionale), tipologia progetto (dropdown), budget (range), scadenza, descrizione (textarea), allegati (file upload)
- [ ] Validazione con Zod: email, min/max lunghezza descrizione, formati file ammessi
- [ ] Honeypot field nascosto per anti-spam
- [ ] File upload: max 5 file, 10MB ciascuno, validazione estensione e dimensione
- [ ] Upload file su Supabase Storage bucket `quote-attachments`
- [ ] POST verso API `/api/quotes` → salvataggio su DB via Prisma (modello `QuoteRequest`)
- [ ] Email di conferma al richiedente con numero pratica e riepilogo (Resend)
- [ ] Email di notifica ai founder (angelo@sycren.com, cosimo@sycren.com)
- [ ] Feedback visivo: spinner durante upload/submit → success/error messaggio
- [ ] Rate limiting: max 3 richieste per IP in 24h (middleware o edge function)
- [ ] Test: submit con dati validi, submit con file troppo grandi, submit con campi mancanti, honeypot riempito (deve fallire silenziosamente)

### 1.3 Admin Panel — Gestione Preventivi

- [ ] Creare pagina `/admin/quotes` — elenco richieste con filtro per stato
- [ ] Vista dettaglio preventivo: tutti i campi, file allegati, stato, storico attività
- [ ] Azioni admin: `REVIEWING`, `QUOTED`, `DECLINED`
- [ ] Form per generare/preparare risposta al preventivo
- [ ] Possibilità di scaricare i file allegati
- [ ] Notifica al cliente al cambio di stato (email)
- [ ] Test: navigazione elenco, cambio stato, visualizzazione dettaglio

### 1.4 Client Onboarding (Wizard)

- [ ] Creare pagina `/onboarding` — wizard a 5 step con progress bar
- [ ] Step 1 — Profilo Aziendale: ragione sociale, partita IVA, indirizzo, telefono, settore
- [ ] Step 2 — Requisiti Dettagliati: descrizione estesa, user stories, wireframe/design ref (allegati)
- [ ] Step 3 — Timeline e Milestone: date baseline proposte, revisioni, consegna. Input admin + conferma cliente
- [ ] Step 4 — Contratto e Fatturazione: preview contratto, 50% upfront, metodo pagamento
- [ ] Step 5 — Riepilogo Finale: tutti i dati in un'unica schermata, bottone "Conferma e parti"
- [ ] Salvataggio bozza ad ogni step (auto-save ogni 30 secondi)
- [ ] Riprendibile: se l'utente esce e rientra, riparte dall'ultimo step completato
- [ ] Alla conferma finale: creazione del record `Project` in DB con stato `IN_PROGRESS`
- [ ] Email di benvenuto: "Benvenuto in Sycren Studios! Il tuo progetto [nome] è partito."
- [ ] Notifica al team: "Nuovo progetto avviato: [nome]"
- [ ] Test: completamento wizard, salvataggio bozza, uscita e ripresa, validazione campi per step

### 1.5 Project Dashboard

- [ ] Creare pagina `/projects` — elenco progetti dell'utente loggato
- [ ] Creare pagina `/projects/[id]` — dashboard progetto (layout comune)
- [ ] Header progetto: nome, stato (badge colorato), date chiave, cliente
- [ ] Barra di avanzamento globale: milestone completate / milestone totali (%)
- [ ] Sezione Milestone: elenco con nome, descrizione, data prevista, data completamento, stato
- [ ] Sezione Deliverable: checklist con nome, descrizione, link, stato
- [ ] Il team può aggiornare stato milestone e deliverable
- [ ] Il cliente può approvare o richiedere modifiche ai deliverable
- [ ] Timeline visiva (Gantt semplificato o timeline lineare)
- [ ] Changelog: feed cronologico di tutte le attività del progetto (creazione, milestone, file, messaggi)
- [ ] Stati progetto visibili e aggiornabili
- [ ] Test: creazione progetto da onboarding, visualizzazione milestone, cambio stato deliverable, changelog popolato

### 1.6 File Sharing

- [ ] Creare pagina `/projects/[id]/files` — area file del progetto
- [ ] Griglia/lista file con struttura cartelle di default:
  - `/briefs`, `/design`, `/technical`, `/deliverables`, `/reference`
- [ ] Upload drag & drop con progress bar visiva
- [ ] Validazione: formati permessi, limite 50MB per file (MVP)
- [ ] Versionamento: stesso nome file → nuova versione, storico versioni accessibile
- [ ] Preview inline: PDF, immagini (JPG/PNG/SVG), documenti di testo
- [ ] Download link generabile con scadenza opzionale (24h, 7gg, 30gg)
- [ ] Commenti per file: discussione contestuale su ogni file
- [ ] Notifica email: "Nuovo file caricato in [progetto] — [nome file]"
- [ ] Permessi: il cliente vede/carica solo area propria; team ha accesso completo
- [ ] Test: upload file, download, preview, versionamento, commenti

### 1.7 Client Communication (Chat)

- [ ] Integrare Supabase Realtime per messaggistica in tempo reale
- [ ] Creare pagina `/projects/[id]/chat` — canale chat dedicato al progetto
- [ ] Invio messaggi di testo con enter (Shift+Enter per a capo)
- [ ] Allegati nei messaggi (file, immagini)
- [ ] Emoji picker (opzionale MVP, può essere differito)
- [ ] Indicatore "sta scrivendo" (presence via Realtime)
- [ ] Timestamp per ogni messaggio, separatore giornaliero
- [ ] Storico completo: scroll verso l'alto carica messaggi più vecchi (infinite scroll o paginazione)
- [ ] Discussioni per milestone: thread sotto ogni milestone nella dashboard
- [ ] Notifiche email: nuovo messaggio (batch ogni 15 min se non letto)
- [ ] Preferenze notifica: frequenza email configurabile
- [ ] Template risposte rapide per il team (opzionale MVP, può essere differito)
- [ ] Test: invio messaggio, ricezione in tempo reale, storico, notifiche email

### 1.8 Admin Panel — Gestione Progetti

- [ ] Pagina `/admin/projects` — elenco tutti i progetti (per admin/team)
- [ ] Filtri: stato, cliente, data, nome
- [ ] Vista dettaglio: tutte le info progetto, membri team assegnati
- [ ] Gestione milestone: CRUD completo per admin
- [ ] Gestione deliverable: CRUD completo per admin
- [ ] Assegnazione team: seleziona developer al progetto
- [ ] Cambio stato progetto manuale (es. `IN_PROGRESS` → `ON_HOLD` se cliente non risponde)
- [ ] Test: CRUD milestone, cambio stato, gestione team

---

## Phase 2: Business Logic (16–17 anni)

### 2.1 Quote Generation e Accettazione

- [ ] Generazione automatica preventivo PDF con template professionale (usare `@react-pdf/renderer` o Puppeteer)
- [ ] Template PDF: logo Sycren, dati cliente, descrizione, prezzi, condizioni, scadenza
- [ ] Pagina pubblica `/quotes/[token]` — link unico per cliente (no auth richiesta)
- [ ] Pagina di accettazione: riepilogo preventivo + bottone "Accetta e Firma"
- [ ] E-signature integrata (canvas per firma a mano libera o componente dedicated)
- [ ] Firma salvata come immagine + timestamp + hash di integrità
- [ ] Dopo firma: creazione contratto digitale in DB, generazione PDF contratto firmato
- [ ] Integrazione Stripe Payment Link o Stripe Checkout per 50% upfront
- [ ] Webhook Stripe: conferma pagamento → attivazione progetto
- [ ] Email al cliente: "Preventivo accettato! Procedi con il pagamento"
- [ ] Email al team: "Nuovo contratto firmato — [cliente] — [importo]"
- [ ] Test: generazione PDF, firma, pagamento Stripe, webhook, attivazione progetto

### 2.2 Milestone Avanzate

- [ ] Calendario milestone con vista mese/settimana
- [ ] Notifiche automatiche: 7 giorni prima della scadenza milestone
- [ ] Notifica di ritardo: se milestone non completata entro la data prevista + 2 giorni
- [ ] Drag & drop per riordinare milestone (admin)
- [ ] Dipendenze tra milestone: "Milestone B non può iniziare prima di Milestone A"
- [ ] Test: creazione milestone, notifica scadenza, riordino, dipendenze

### 2.3 Changelog

- [ ] Feed changelog automatico per ogni progetto
- [ ] Trigger di changelog: milestone completata, deliverable approvato, file caricato, stato cambiato, messaggio inviato (opzionale, filtrabile), contratto firmato
- [ ] Ogni entry: timestamp, autore, tipo evento, descrizione, link al contesto (es. link al file caricato)
- [ ] Filtri changelog: per tipo evento, per data, per autore
- [ ] Vista changelog nella dashboard progetto e in pagina dedicata
- [ ] Esportazione changelog in PDF/CSV
- [ ] Test: esecuzione azioni che generano changelog, verifica entry, filtri, esportazione

### 2.4 Notifiche Complete

- [ ] Sistema di notifiche in-app (dropdown header con contatore)
- [ ] Notifiche push via browser (Service Worker + Notification API) — opzionale
- [ ] Template email professionali per ogni trigger:
  - Nuovo file caricato
  - Nuovo messaggio chat
  - Deliverable in review
  - Deliverable approvato / modifiche richieste
  - Milestone completata
  - Milestone in ritardo
  - Stato progetto cambiato
  - Contratto firmato
  - Pagamento ricevuto
  - Progetto completato
- [ ] Centro preferenze notifiche per cliente: attiva/disattiva singoli trigger
- [ ] Frequenza email: immediata, riepilogo giornaliero, riepilogo settimanale, mai
- [ ] Test: invio email per ogni trigger, preferenze salvate, riepilogo giornaliero/settimanale

### 2.5 Stripe Integration

- [ ] Account Stripe connesso a Sycren
- [ ] Checkout per pagamento singolo (50% upfront su accettazione preventivo)
- [ ] Webhook Stripe: `checkout.session.completed`, `invoice.paid`, `invoice.payment_failed`
- [ ] Creazione fattura in DB allo stato `PAID`
- [ ] Gestione pagamenti ricorrenti (retainer): Stripe Subscription
- [ ] Portale cliente Stripe (Customer Portal) per gestione metodi pagamento e fatture
- [ ] Email ricevuta di pagamento con riepilogo
- [ ] Test: checkout completato, webhook ricevuto, fattura creata, subscription creata/rinnovata

---

## Phase 3: Polish (17 anni)

### 3.1 Progetto Archivio e Storico

- [ ] Archiviazione automatica: 30 giorni dopo completamento → stato `ARCHIVED`
- [ ] Pagina `/archive` — elenco progetti completati con filtri
- [ ] Vista dettaglio archivio: tutti i deliverable, changelog completo, documentazione
- [ ] Documentazione finale PDF generata automaticamente:
  - Riepilogo progetto (descrizione, date, team)
  - Milestone completate con date
  - Deliverable finali con link
  - Credenziali (se applicabile) in appendice cifrata
  - Istruzioni di manutenzione
- [ ] Storico fatture: elenco fatture per progetto, stato pagamento, download PDF
- [ ] Contratti firmati: copia digitale con firma, data, condizioni
- [ ] Bottone "Riattiva progetto" → nuova richiesta preventivo per manutenzione/evolutive
- [ ] Test: archiviazione automatica, generazione PDF, riattivazione

### 3.2 Reporting e Statistiche

- [ ] Dashboard admin con overview in tempo reale:
  - Richieste preventivo in sospeso (oggi, questa settimana, questo mese)
  - Progetti attivi totali
  - MRR stimato (somma retainer attivi)
  - Tasso di conversione preventivo → progetto
- [ ] Report per progetto:
  - Tempo totale speso (se time tracking implementato)
  - Milestone completate vs pianificate
  - Stato finanziario: preventivato, fatturato, saldo
- [ ] Report per cliente:
  - Storico progetti
  - Fatturato totale
  - Retainer attivi
- [ ] Esportazione report in CSV/PDF
- [ ] Test: visualizzazione dashboard admin, dati corretti, esportazione

### 3.3 Mobile Responsive

- [ ] Audit mobile completo: testare tutte le pagine a 375px, 768px, 1024px, 1440px
- [ ] Navigation: sidebar collassa in hamburger menu su mobile
- [ ] File sharing: griglia adattiva, upload ottimizzato per touch
- [ ] Chat: input bar ancorata in basso, tastiera non copre il campo
- [ ] Wizard onboarding: step full-screen su mobile
- [ ] Dashboard: metriche in cards verticali invece che griglia orizzontale
- [ ] Modali e popover: gestione touch e scroll su mobile
- [ ] Test: navigazione completa su viewport mobile, touch events, upload da mobile

### 3.4 UI/UX Finale

- [ ] Audit UX completo con checklist:
  - Tutti gli stati vuoti hanno illustrazione e CTA
  - Tutti gli errori hanno messaggio leggibile
  - Tutti i form hanno label visibili
  - Tutte le azioni hanno feedback (loading, success, error)
  - Tastiera: tab order corretto, focus visibile, skip link
- [ ] Responsive email templates (testati su Gmail, Outlook, Apple Mail, mobile)
- [ ] Animazioni e transizioni coerenti (Framer Motion per micro-interazioni)
- [ ] Dark mode support (ereditato da design system)
- [ ] Verifica WCAG AA: contrasto, aria-label, role, heading hierarchy
- [ ] Test: navigazione da tastiera, screen reader (VoiceOver/NVDA), zoom 200%

---

## Integrazione e Infrastruttura

### sycren-db

- [ ] Importare `sycren-db` come dipendenza (npm workspace o submodule)
- [ ] Verificare che tutti i modelli necessari esistano in schema Prisma (User, QuoteRequest, Project, Milestone, Deliverable, Message, File, Contract, Invoice, ChangelogEntry)
- [ ] Eseguire migrazioni in sviluppo e staging
- [ ] Seed data per test: utente demo, progetto demo, milestone, deliverable, messaggi
- [ ] Test: query Prisma su tutti i modelli, relazioni funzionanti

### sycren-app SSO (Fase 2)

- [ ] In Fase 0: auth diretta con Supabase Auth (nessuna dipendenza da `sycren-app`)
- [ ] Preparare migrazione futura: redirect OAuth a `app.sycren.com` per login
- [ ] Schema User condiviso: assicurarsi che lo stesso user possa accedere a studio e app
- [ ] Verifica: lo stesso account funziona su entrambi i servizi (quando SSO sarà attivo)

### Stripe Integration

- [ ] Account Stripe in modalità test
- [ ] Prodotti Stripe: "Studio Project Downpayment", "Studio Retainer Basic", "Studio Retainer Standard", "Studio Retainer Premium"
- [ ] Webhook endpoint `/api/stripe/webhook` con verifica firma
- [ ] Test: pagamento test con carta Stripe (4242...), verifica webhook, creazione fattura
- [ ] Mettere in produzione solo in Fase 2 (non prima del 18° compleanno)

### Resend (Email)

- [ ] Account Resend con dominio verificato `sycren.com`
- [ ] Template email per tutti i trigger (Phase 1 + Phase 2)
- [ ] Test: deliverability, spam score, rendering su client principali
- [ ] Rate limiting: max 3.000 email/mese nel piano gratuito — monitorare soglia

### CI/CD

- [ ] GitHub Actions: lint su ogni push (ESLint + Prettier)
- [ ] GitHub Actions: type check su ogni push (tsc —noEmit)
- [ ] GitHub Actions: test su ogni push (se test implementati)
- [ ] GitHub Actions: deploy automatico su Vercel su push a `main`
- [ ] Preview deploy: Vercel Preview per ogni PR
- [ ] Test: push a branch → CI passa, push a main → deploy automatico

---

## Verifica e QA

### Test Manuali — Flussi Critici

- [ ] **Flusso #1 — Nuovo cliente:** Register → Request Quote → Ricevi email conferma → Admin risponde → Accetta preventivo → Firma → Paga → Onboarding → Dashboard attiva
- [ ] **Flusso #2 — Cliente esistente:** Login → Vedi progetto → Controlla milestone → Scarica file → Invia messaggio chat → Approva deliverable
- [ ] **Flusso #3 — Admin:** Login → Vedi richieste in sospeso → Rispondi → Crea milestone → Carica deliverable → Comunica con cliente → Completa progetto
- [ ] **Flusso #4 — Notifiche:** Admin carica file → Cliente riceve email → Cliente commenta → Admin riceve notifica in-app ed email
- [ ] **Flusso #5 — Archiviazione:** Progetto completato → 30 giorni → Archiviato → Cliente visualizza archivio → Scarica documentazione finale

### Criteri di Verifica Generali

- [ ] Tutti i form validano input lato client (Zod) e lato server (Zod/Prisma)
- [ ] Nessuna route protetta accessibile senza autenticazione
- [ ] Upload file sicuro: validazione tipo MIME, limite dimensione, scanning (se disponibile)
- [ ] Rate limiting su endpoint pubblici (quote request, login attempts)
- [ ] Tutte le API route gestiscono errori gracefully (try/catch, messaggi descrittivi)
- [ ] CSRF protection attiva
- [ ] Headers di sicurezza: Content-Security-Policy, X-Frame-Options, X-Content-Type-Options
- [ ] Nessun segreto in codice o in repo — solo variabili d'ambiente
- [ ] `.env.example` completo con commenti
- [ ] Logging errori lato server (Vercel Logs o Supabase Logs)

---

## Oltre Studio — Cosa Segue (con Età)

Completato `sycren-studio`, l'ordine di sviluppo prosegue:

| # | Modulo | Età 🕐 | Dipende da |
|---|---|---|---|
| 1 | Landing Page `sycren.com` | **16** | — |
| 2 | Database Foundation `sycren-db` | **16** | — |
| 3 | **Client Portal `studio.sycren.com`** | **16** | sycren-db |
| 4 | Hub Centrale `app.sycren.com` (SSO) | **16–17** | sycren-db |
| 5 | Cloud Lite `cloud.sycren.com` | **17** | sycren-db, app |
| 6 | Domains `domains.sycren.com` | **17–18** | sycren-db, app |
| 7 | Security `security.sycren.com` | **18+** | sycren-db, app |
| 🎂 | **Lancio Commerciale (P.IVA, Vendite)** | **18** | Tutti i moduli pronti |

---

## Related Notes

| Document | Description |
|---|---|
| [[studio.README]] | sycren-studio overview |
| [[studio.requirements]] | Full PRD |
| [[db.README\|sycren-db]] | Blocking dependency |
| [[design-system.README\|sycren-design-system]] | UI dependency |
| [[infra.README\|sycren-infra]] | Docker and deployment |
| [[app.README\|sycren-app]] | SSO integration (Fase 2) |
| [[cloud.README\|sycren-cloud]] | Hosting for client projects |
| [[landing.README\|sycren-landing]] | Source of quote requests |
| [[foundation/ROADMAP\|Bootstrap Roadmap]] | Timeline — module #3 |

---

*Sycren Studios — TODO v1.0 · Giugno 2026 · Allineato a Bootstrap Roadmap v1.3*
