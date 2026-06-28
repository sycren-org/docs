# sycren-domains — TODO & Implementation Plan
> PRD v1.0 · Giugno 2026 · Fase 0 (Pre-Lancio) · Allineato a Bootstrap Roadmap v1.3

---

## Contesto (dalla Roadmap)

- **Età di realizzazione:** 🕐 17-18 anni — modulo #6 su 7 nell'ordine di costruzione
- **Priorità:** Bassa. Da costruire dopo Landing → sycren-db → sycren-studio → sycren-app → sycren-cloud
- **Strategia:** Wrapper UI su Cloudflare API — nessun registrar diretto
- **Integrazione:** Dati e autenticazione dipendono da `sycren-db` e `sycren-app` (devono esistere prima)
- **Modello economico:** Markup su prezzi Cloudflare (15% stimato) + pacchetti premium DNS
- **Posizionamento:** Non prioritario per il lancio commerciale (Fase 2), ma ad alto valore strategico per il lock-in dell'ecosistema

### Dipendenze Bloccanti

| Dipendenza | Stato Richiesto | Impatto |
|---|---|---|
| `sycren-db` | Schema Prisma completo con modelli Domain/DnsRecord | Database e ORM non disponibili prima |
| `sycren-app` | SSO funzionante, API gateway attivo | Login centralizzato e notifiche non disponibili |
| `sycren-cloud` | Almeno MVP live | Integrazione "Point to Cloud" non possibile |
| Cloudflare Account | API token con permessi DNS/Registrar | Deve essere già configurato (usato per sycren.com) |

### Timeline Stimata

| Fase | Durata | Età |
|---|---|---|
| Setup iniziale + integrazioni | 1 settimana | 17-18 |
| Phase 1 — MVP | 2-3 settimane | 17-18 |
| Phase 2 — Advanced Features | 2-3 settimane | 17-18 |
| Testing + Deploy | 1 settimana | 17-18 |
| **Totale** | **~6-8 settimane** | **17-18 🕐** |

---

## Setup Iniziale

- [ ] Inizializzare progetto Next.js 14 con App Router e TypeScript strict
- [ ] Configurare Tailwind CSS 3.4+ con design token Sycren
- [ ] Installare dipendenze: `@cloudflare/cloudflare-api`, React Hook Form 7, Zod 3, shadcn/ui, Lucide React
- [ ] Configurare next/font con Geist Mono e Inter
- [ ] Collegare `sycren-db` come pacchetto condiviso (schema Prisma, types, utilities)
- [ ] Estrarre modelli `Domain`, `DnsRecord`, `Transfer`, `WhoisLog` dal database
- [ ] Eseguire migrazioni Prisma per le nuove tabelle Domains
- [ ] Creare `.env.example` con variabili Cloudflare, database, auth
- [ ] Impostare struttura cartelle: `app/`, `components/`, `lib/`, `types/`, `hooks/`
- [ ] Configurare Vercel: collegare repo, impostare ENV, abilitare Analytics
- [ ] Configurare sottodominio `domains.sycren.com` su Cloudflare DNS (CNAME a Vercel)
- [ ] Integrare NextAuth.js / Lucia Auth con SSO `app.sycren.com`

---

## Phase 1: MVP (Core Features)

**Obiettivo:** Aprire `domains.sycren.com` con funzionalità essenziali per cercare, registrare, gestire DNS base e rinnovare domini.

### UI Components

- [ ] `SearchBar` — Input con debounce, autocomplete TLD, pulsante "Search"
- [ ] `DomainCard` — Risultato ricerca: nome, TLD, prezzo, badge disponibilità, CTA "Register"
- [ ] `RegistrationForm` — Multi-step: durata, WHOIS contacts, review, checkout
- [ ] `DomainsList` — Tabella dashboard: nome, status, scadenza, azioni rapide
- [ ] `DomainStatusBadge` — Active, Expiring soon, Expired, Transfer in progress
- [ ] `RecordRow` — Singolo record DNS nella tabella con edit/delete inline
- [ ] `DnsRecordForm` — Form dinamico che cambia campi in base al tipo record
- [ ] `PricingBadge` — Prezzo per TLD con markup visualizzato
- [ ] `EmptyState` — "No domains yet — search for your first domain"
- [ ] `SkeletonLoader` — Loading state per liste e tabelle

### Layout

- [ ] Header/Navbar con logo Sycren Domains, navigazione (Search, Dashboard, Tools)
- [ ] Sidebar dashboard: "My Domains", "Transfers", "Tools", "Settings"
- [ ] Footer con link a privacy policy, terms, `app.sycren.com`

### 1.1 Domain Search

- [ ] Barra di ricerca nella homepage con placeholder "Search for your domain..."
- [ ] Verifica disponibilità real-time via Cloudflare API (GET `/zones/:zone_id/dns/check`)
- [ ] Display risultati: nome dominio, TLD, prezzo annuale, badge Available / Registered
- [ ] TLD popolari in griglia sotto la search bar (.com, .it, .io, .dev, .app, .net)
- [ ] Suggerimenti per domini non disponibili (TLD alternativi)
- [ ] Gestione errori: rate limit Cloudflare, timeout, dominio non valido
- [ ] Loading skeleton durante la ricerca
- [ ] Storico ricerche recenti (localStorage per utenti non loggati, DB per loggati)

### 1.2 Domain Registration

- [ ] Flusso registrazione guidato (wizard a step)
  - Step 1: Seleziona durata (1, 2, 3, 5, 10 anni) — mostra prezzo totale
  - Step 2: Inserisci contatti WHOIS (Registrant, Admin, Tech) o usa dati profilo
  - Step 3: Riepilogo ordine e checkout
- [ ] Integrazione checkout via `app.sycren.com` API (payment gateway centralizzato)
- [ ] Chiamata API Cloudflare Registrar: POST `/accounts/:id/registrar/domains`
- [ ] Creazione automatica zona DNS su Cloudflare post-registrazione
- [ ] Aggiunta dominio alla dashboard utente via sycren-db
- [ ] Email di conferma con dettagli tecnici
- [ ] Creazione template DNS automatico "Sycren Default"
- [ ] Validazione: TLD supportato, durata valida, contatti WHOIS validi

### 1.3 Basic DNS Panel (A, CNAME, MX)

- [ ] Tabella record per dominio con colonne: Type, Name, Value, TTL, Proxy, Actions
- [ ] Aggiunta record A (IPv4) — campi: name, IPv4 address, TTL, proxy status
- [ ] Aggiunta record CNAME (alias) — campi: name, target, TTL, proxy status
- [ ] Aggiunta record MX (mail) — campi: name, mail server, priority, TTL
- [ ] Modifica record inline (click sul valore → input editabile)
- [ ] Eliminazione record con conferma "Are you sure?"
- [ ] Proxy toggle (DNS only / Proxied) per record A, AAAA, CNAME
- [ ] TTL predefinito: Auto (Cloudflare ottimale)
- [ ] Integrazione API Cloudflare: CRUD su `/zones/:id/dns_records/:id`
- [ ] Messaggio "DNS changes may take up to 15 minutes to propagate"

### 1.4 Domain Renewals

- [ ] Pulsante "Renew" nella dashboard per dominio
- [ ] Selezione durata rinnovo (1, 2, 3, 5 anni)
- [ ] Checkout rinnovo via sistema pagamento centralizzato
- [ ] Toggle auto-renew per dominio (ON di default)
- [ ] Visualizzazione data scadenza con countdown
- [ ] Stato rinnovo: `Paid`, `Pending`, `Failed`
- [ ] Storico rinnovi: data, importo, metodo

---

## Phase 2: Advanced Features

**Obiettivo:** Raggiungere la parità funzionale con registrar professionali (Namecheap, Cloudflare Dashboard nativa).

### 2.1 All DNS Record Types

- [ ] Record AAAA (IPv6) — campi: name, IPv6 address, TTL, proxy
- [ ] Record TXT — textarea per valore, validazione SPF/DKIM
- [ ] Record NS — per deleghe sottodominio
- [ ] Record SRV — campi: service, proto, name, priority, weight, port, target
- [ ] Validazione avanzata per tipo record (es. IPv6 valido per AAAA, porta numero per SRV)
- [ ] Supporto record multipli per stesso tipo/nome (es. più A round-robin)

### 2.2 Domain Transfers

#### Transfer In (Ingresso)

- [ ] Form "Transfer a domain to Sycren" con campo dominio
- [ ] Guida interattiva "How to get your EPP code" (per registrar comune)
- [ ] Verifica prerequisiti: dominio unlocked, WHOIS admin email accessibile, >60 giorni da registrazione/ultimo transfer
- [ ] Inserimento EPP code
- [ ] Avvio transfer via Cloudflare API
- [ ] Tracking stato: `Pending approval at current registrar`, `Waiting for Cloudflare`, `Completed`, `Failed`
- [ ] Timeline visiva con step correnti
- [ ] Notifiche email a ogni cambio stato
- [ ] Gestione fallimenti: motivo, azioni correttive suggerite

#### Transfer Out (Uscita)

- [ ] Pulsante "Transfer out" nella dashboard dominio
- [ ] Generazione EPP code (click-to-reveal)
- [ ] Toggle domain lock (lock/unlock con conferma)
- [ ] Istruzioni per completare transfer presso nuovo registrar
- [ ] Storico transfer completati

### 2.3 WHOIS Privacy

- [ ] Badge stato WHOIS Privacy nella dashboard: `Protected ✓` / `Not protected`
- [ ] Toggle ON/OFF con conferma
- [ ] Integrazione API Cloudflare Registrar per WHOIS privacy
- [ ] Visualizzazione dati WHOIS pubblici vs protetti
- [ ] Log modifiche WHOIS privacy

### 2.4 DNS Propagation Checker

- [ ] Input: dominio + (opzionale) tipo record specifico
- [ ] Check da 4+ località globali (via API terze parti o Cloudflare)
- [ ] Mappa mondiale con markers colorati per stato propagazione
- [ ] Tabella risultati: nameserver → valore rilevato → match/non match
- [ ] Stima tempo restante per propagazione completa
- [ ] Storico controlli recenti
- [ ] Pulsante "Re-check" con cooldown 30s

### 2.5 URL Redirect & Masking

- [ ] Interfaccia dedicata per configurare redirect
- [ ] Tipi redirect: 301 (Permanent), 302 (Temporary)
- [ ] Configurazione: source URL → target URL
- [ ] URL Masking (iframe) con avvertenza SEO
- [ ] Lista redirect attivi con contatore visite (opzionale)
- [ ] Integrazione Cloudflare Bulk Redirects / Page Rules
- [ ] Test redirect: pulsante "Test redirect" che apre target in nuova tab

### 2.6 Pre-Expiry Notification System

- [ ] CRON scheduler (Vercel CRON / GitHub Actions) per controllo scadenze giornaliero
- [ ] Invio notifiche tramite API `app.sycren.com` (sistema notifiche centralizzato)
- [ ] Template email personalizzate per ogni intervallo (90/30/7 giorni)
- [ ] Log notifiche inviate per dominio (data, tipo, canale)
- [ ] Rinnovo automatico tenta pagamento salvato; fallback notifica
- [ ] Configurazione preferenze notifiche per utente (quali canali, quali soglie)

### 2.7 DNS Templates

- [ ] Libreria template predefiniti:
  - "Sycren Cloud" — A/AAAA al VPS Cloud
  - "Google Workspace" — MX + SPF/DKIM/DMARC TXT
  - "Vercel" — CNAME a cname.vercel-dns.com
  - "GitHub Pages" — A record GitHub Pages
  - "Custom" — creato dall'utente
- [ ] Applicazione template a un dominio con preview modifiche
- [ ] Salvataggio template utente (nome, descrizione, record inclusi)
- [ ] Modifica template esistente

---

## Integrations

### sycren-db

- [ ] Creare modelli Prisma in `sycren-db`:
  - `Domain`: id, userId, name, tld, status, registeredAt, expiresAt, autoRenew, whoisPrivacy
  - `DnsRecord`: id, domainId, type, name, value, ttl, proxied, priority, weight, port, createdAt, updatedAt
  - `Transfer`: id, domainId, type (IN/OUT), status, eppCode, startedAt, completedAt
  - `WhoisLog`: id, domainId, action, timestamp
- [ ] Generare tipi TypeScript da Prisma
- [ ] Estrarre funzioni utility: `getDomainsByUser()`, `getDomainById()`, `createDomain()`, `updateDnsRecord()`
- [ ] Seed script con dati di esempio per sviluppo

### sycren-app (SSO & Notifiche)

- [ ] Integrare NextAuth.js come client SSO di `app.sycren.com`
- [ ] Reindirizzare utenti non autenticati a `app.sycren.com/login`
- [ ] Recuperare profilo utente e permessi da API `app.sycren.com`
- [ ] Inviare notifiche (scadenze, trasferimenti) a API notifiche centralizzata
- [ ] Widget "My Domains" per `app.sycren.com` dashboard (Next.js remote component)
- [ ] Billing: reindirizzare checkout alla pagina di pagamento centralizzata

### sycren-cloud (Domain Pointing)

- [ ] API endpoint in `sycren-cloud`: `POST /api/domains/{id}/point`
- [ ] Pulsante "Point to Cloud" in dashboard dominio → chiama API
- [ ] Crea/aggiorna A/AAAA record con IP del VPS utente
- [ ] Badge di connessione: `Connected to Cloud: VPS-Name`
- [ ] Opzione "Deploy app" che apre la pipeline di deploy di `cloud.sycren.com`
- [ ] SSL automatico via Cloudflare per il dominio puntato

### sycren-security (DDoS Protection)

- [ ] API endpoint in `sycren-security`: `POST /api/domains/{id}/protect`
- [ ] Pulsante "Enable DDoS protection" in dashboard dominio
- [ ] Attiva regole WAF base via API Cloudflare
- [ ] Badge sicurezza: `Protected by Sycren Security ✓`
- [ ] Eventi di sicurezza sul dominio visibili nel pannello Domains

---

## API Routes (Next.js)

- [ ] `GET /api/domains/search?q=example` — ricerca disponibilità via Cloudflare
- [ ] `GET /api/domains` — lista domini utente
- [ ] `GET /api/domains/{id}` — dettaglio dominio
- [ ] `POST /api/domains/register` — registra dominio
- [ ] `POST /api/domains/{id}/renew` — rinnova dominio
- [ ] `PUT /api/domains/{id}/auto-renew` — toggle auto-renew
- [ ] `GET /api/domains/{id}/dns` — lista record DNS
- [ ] `POST /api/domains/{id}/dns` — crea record DNS
- [ ] `PUT /api/domains/{id}/dns/{recordId}` — modifica record
- [ ] `DELETE /api/domains/{id}/dns/{recordId}` — elimina record
- [ ] `POST /api/domains/{id}/dns/templates/apply` — applica template DNS
- [ ] `POST /api/transfers/initiate` — avvia transfer in ingresso
- [ ] `GET /api/transfers` — lista trasferimenti
- [ ] `POST /api/domains/{id}/transfer-out/generate-epp` — genera EPP
- [ ] `PUT /api/domains/{id}/whois-privacy` — toggle privacy
- [ ] `GET /api/tools/propagation?domain=example.com` — propagation check
- [ ] `POST /api/tools/redirects` — crea redirect
- [ ] `GET /api/tools/redirects` — lista redirect
- [ ] `DELETE /api/tools/redirects/{id}` — elimina redirect

---

## Testing & QA

- [ ] Test unitari con Vitest per utility functions e API helpers
- [ ] Test integrazione Cloudflare API (mockate in CI, reali in staging)
- [ ] Test E2E con Playwright per flussi critici:
  - Ricerca dominio → registrazione → checkout
  - CRUD record DNS
  - Rinnovo dominio
  - Trasferimento in ingresso
- [ ] Test responsive: mobile (375px), tablet (768px), desktop (1440px)
- [ ] Test accessibilità: keyboard navigation, screen reader, WCAG AA
- [ ] Test performance: Lighthouse ≥ 90 mobile
- [ ] Test edge case: rate limit Cloudflare, domini premium, TLD non supportati
- [ ] Test notifiche: trigger manuale scadenze per verificare template email

---

## Deploy & Operations

- [ ] Deploy su Vercel con dominio `domains.sycren.com`
- [ ] Configurare variabili d'ambiente in Vercel
- [ ] Abilitare Vercel Analytics per monitoraggio utenti
- [ ] Configurare monitoring uptime (BetterUptime / Checkly)
- [ ] Configurare alert per errori API Cloudflare (rate limit, auth failure)
- [ ] Backup database (via Supabase automatic backups)
- [ ] Documentazione interna su API Cloudflare utilizzate e rate limits
- [ ] Runbook: procedure per fallimenti registrazione, transfer bloccati, WHOIS errori

---

## Verification Criteria

Al completamento di ogni fase, verificare:

### Phase 1 — MVP Exit Criteria

- [ ] Un utente può cercare un dominio `.com` e vedere disponibilità e prezzo
- [ ] Un utente può registrare un dominio con flusso guidato e pagamento
- [ ] Un utente può vedere il dominio nella dashboard dopo la registrazione
- [ ] Un utente può aggiungere, modificare, eliminare record A, CNAME, MX
- [ ] Un utente può rinnovare un dominio con 1 click
- [ ] DNS changes si propagano via Cloudflare (verifica propagation checker esterno)
- [ ] Autenticazione SSO funzionante con `app.sycren.com`
- [ ] Tema Dark/Light coerente
- [ ] Responsivo su mobile e desktop

### Phase 2 — Advanced Features Exit Criteria

- [ ] Tutti i 7 tipi record DNS gestibili (A, AAAA, CNAME, MX, TXT, NS, SRV)
- [ ] Trasferimento in ingresso completato con EPP code
- [ ] Trasferimento in uscita con generazione EPP funzionante
- [ ] WHOIS privacy attivabile/disattivabile
- [ ] DNS propagation checker mostra risultati da 4+ regioni
- [ ] URL redirect (301/302) funzionante
- [ ] Notifiche automatiche a 90/30/7 giorni inviate correttamente
- [ ] Template DNS applicabili con 1 click
- [ ] Integrazione "Point to Cloud" funzionante con `cloud.sycren.com`
- [ ] Integrazione "DDoS protection" funzionante con `security.sycren.com`

---

## Oltre il Progetto — Evoluzione Futura

| Feature | Quando | Note |
|---|---|---|
| API pubblica REST per domini | Fase 2+ | Per clienti che vogliono automazione |
| Multi-account / Team management | Fase 2+ | Utile per agenzie e aziende |
| Domain marketplace (buy/sell) | Fase 3 | Dopo lancio commerciale |
| Premium domain broker | Fase 3 | Per domini già registrati |
| DNSSEC management | Fase 2+ | Sicurezza aggiuntiva zona DNS |
| Email hosting integrato | Fase 3 | Sinergia con Cloud + Domains |
| Bulk domain operations | Fase 2+ | Ricerca, registrazione, modifica DNS in batch |
| Whois history lookup | Dopo lancio | Storico cambiamenti WHOIS |
| Domain appraisal | Fase 3 | Stima valore domini |

---

## Note Finali

- **Età di realizzazione:** 🕐 17-18 anni — modulo a bassa priorità
- **Realisticamente:** sycren-domains sarà probabilmente l'ultimo modulo completato prima del lancio commerciale, o addirittura subito dopo (Fase 2 iniziale)
- **Priorità assoluta per il lancio:** Landing page → sycren-db → Studio → App → Cloud
- **Cloudflare API gratuito** per DNS management — nessun costo operativo oltre al database
- **Reselling:** Il margine sui domini è basso (15% su prezzi già competitivi Cloudflare). Il vero valore è nell'integrazione con Cloud e Security
- Una volta completato, sycren-domains completa l'ecosistema Sycren: i clienti possono ottenere dominio, hosting, sicurezza e sviluppo software da un'unica piattaforma

---

*Sycren Domains — TODO v1.0 · Giugno 2026 · Allineato a Bootstrap Roadmap v1.3*
