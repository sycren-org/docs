# sycren-cloud — TODO Esecutivo
> PRD v1.0 · Giugno 2026 · 🕐 17 anni · Allineato a Bootstrap Roadmap v1.3

---

## Contesto (dalla Roadmap)

- Entrambi i founder hanno **16-17 anni 🕐**, nessuna partita IVA — Soluzione B: costruire tutto ora, vendere al **18° compleanno 🎂**
- sycren-cloud è il **quinto modulo** nella roadmap di costruzione. Ordine: Landing → sycren-db → Studio → App → **Cloud** → Domains → Security
- Cloud viene costruito a **17 anni** (mese 3-5 dalla partenza) e genera il primo MRR ricorrente della piattaforma
- Modello operativo: **reseller Hetzner** con markup 40-600% — nessuna infrastruttura propria in Fase 0
- Obiettivo pre-lancio: pipeline di **20-30 contatti** qualificati e prodotto Cloud Lite funzionante
- Al momento del lancio commerciale (18 anni), Cloud deve essere pronto per vendere

**Posizione nella timeline:**

```
Età 16 ── LND ── DB ── STU ──► 16-17 ── APP ──► 17 ── CLD ──► 17 ── PRE ──► 18 🎂 LANCIO
                                                                                   
                         cloud.sycren.com lite ── 20-30 leads ── prerequisiti legali
```

**Dipendenza:** `sycren-db` (schema Prisma) e `sycren-app` (SSO) devono esistere prima che cloud possa integrarsi pienamente.

---

## Setup Iniziale

- [ ] Creare progetto Next.js 14+ con App Router e TypeScript strict
- [ ] Configurare Tailwind CSS 3.4+ con design token Sycren (da sycren-design-system)
- [ ] Installare dipendenze: Recharts, React Hook Form 7, Zod 3, Lucide React, tRPC
- [ ] Configurare next/font
- [ ] Creare `.env.example` con tutte le variabili (Hetzner API token, DB, ecc.)
- [ ] Impostare struttura cartelle: `src/app/(routes)`, `src/components/`, `src/lib/`, `src/server/`
- [ ] Configurare Vercel: collegare repo `sycren/sycren-cloud`, impostare ENV
- [ ] Configurare Cloudflare DNS: `cloud.sycren.com` → CNAME a Vercel
- [ ] Integrare Prisma con `sycren-db` (schema condiviso, modelli Server, Backup, Deployment, MonitorData)
- [ ] Impostare GitHub Actions: lint + typecheck su ogni PR, deploy su push a main
- [ ] Configurare ambiente di staging: `cloud-staging.sycren.com` (branch staging)

---

## Phase 1 — Cloud Lite MVP 🕐 17 anni (Mese 3-5)

> **Obiettivo:** VPS provisioning funzionante con Hetzner API, deploy da GitHub, monitoring base. Abbastanza per demo a potenziali clienti e iniziare a raccogliere interesse.

### 1.1 Hetzner API Integration

- [ ] Studiare documentazione Hetzner Cloud API (REST v1)
- [ ] Creare client API TypeScript: create server, get server, list servers, delete server, reboot, shutdown
- [ ] Implementare rate limiting (Hetzner: 100 req/10s)
- [ ] Gestire errori: rate limit, insufficient resources, invalid region, token scaduto
- [ ] Scrivere test mockati per ogni endpoint API
- [ ] Aggiungere logging strutturato per ogni chiamata API (pino/winston)

**Endpoint API Hetzner da integrare:**
| Azione | Endpoint | Priorità |
|---|---|---|
| Crea server | POST /v1/servers | P0 |
| Lista server | GET /v1/servers | P0 |
| Dettaglio server | GET /v1/servers/{id} | P0 |
| Elimina server | DELETE /v1/servers/{id} | P0 |
| Reboot server | POST /v1/servers/{id}/actions/reboot | P0 |
| Lista datacenter | GET /v1/datacenters | P0 |
| Lista server types | GET /v1/server_types | P0 |
| Lista OS images | GET /v1/images | P0 |
| Crea snapshot | POST /v1/servers/{id}/actions/create_image | P1 |
| Resize server | POST /v1/servers/{id}/actions/change_type | P2 |

### 1.2 Database Schema (sycren-db)

- [ ] Modello `Server`: id, userId, hetznerId, name, status, cpu, ram, disk, region, os, ipv4, ipv6, createdAt, updatedAt
- [ ] Modello `SshKey`: id, userId, name, publicKey, fingerprint, createdAt
- [ ] Modello `Backup`: id, serverId, type (auto/manual), status, size, retentionDays, createdAt
- [ ] Modello `Deployment`: id, serverId, githubRepoId, branch, commitSha, buildLog, status, createdAt
- [ ] Modello `MonitorMetric`: id, serverId, metric (cpu/ram/disk/network), value, timestamp (TTL 30gg)
- [ ] Modello `Alert`: id, serverId, metric, operator, threshold, channel, enabled, lastTriggered
- [ ] Modello `DomainBinding`: id, serverId, domain, sslStatus, sslExpiry, autoRenew
- [ ] Aggiungere relazioni Prisma con User (sycren-app SSO)
- [ ] Scrivere seed data per sviluppo locale

### 1.3 VPS Provisioning UI

- [ ] Pagina `/vps/new` — form selezione risorse:
  - CPU slider (1-16 vCore) con prezzo in tempo reale
  - RAM slider (2-64 GB) con prezzo in tempo reale
  - Disk slider (20-500 GB NVMe) con prezzo in tempo reale
  - Region selector: Nbg1 (Nuremberg), Fsn1 (Falkenstein), Hel1 (Helsinki)
  - OS selector: Ubuntu 24.04, Debian 12, Alpine 3.20, CentOS Stream 9
- [ ] Calcolatore prezzo live (costo Hetzner + markup Sycren)
- [ ] SSH key selector: scegli tra chiavi salvate o carica nuova
- [ ] Summary step: riepilogo configurazione, prezzo mensile, pulsante "Provision"
- [ ] Provisioning progress screen: animazione passo-passo (creazione → OS install → network config → pronto)
- [ ] Pagina `/vps/[id]` — dettaglio VPS: IP, risorse, uptime, azioni rapide (restart, shutdown, rebuild, delete)
- [ ] Pagina `/vps` — lista VPS con stato (running/stopped/offline), filtro e ricerca
- [ ] Conferma prima di delete: modale con input "DELETE" obbligatorio
- [ ] Responsive: funzionante su mobile (375px) per gestione rapida

### 1.4 Basic Monitoring Dashboard

- [ ] Pagina `/vps/[id]/monitoring` — dashboard monitoring:
  - CPU usage: grafico area 24h (Recharts)
  - RAM usage: grafico area 24h
  - Disk usage: grafico a barre per partizioni
  - Network traffic: grafico area in/out (bytes/sec)
- [ ] WebSocket connection per dati real-time (Socket.IO o WebSocket nativo)
- [ ] Hetzner metrics polling: ogni 60s via API (o installare agent su VPS)
- [ ] Uptime indicator: green (>99.9%), yellow (>99%), red (<99%)
- [ ] Ultimo riavvio: timestamp + reason (se disponibile)
- [ ] Auto-refresh toggle: on/off (default: on in produzione)

### 1.5 Deploy da GitHub

- [ ] Implementare GitHub OAuth App: registrare app su GitHub, gestire token
- [ ] Pagina `/deploy/new` — connetti repository:
  - Lista repository dell'utente (dal token OAuth)
  - Select branch per deploy
  - Configura build command e output directory
  - Aggiungi environment variables (con opzione "secret")
- [ ] Webhook receiver: POST `/api/deploy/webhook` — trigger su push al branch configurato
- [ ] Build pipeline: clone repo → install deps → build → deploy su VPS
- [ ] Deploy log: output live in terminal emulator nel browser (xterm.js o equivalente)
- [ ] Pagina `/deploy/[id]` — dettaglio deploy: commit, autore, timestamp, durata, build log, esito
- [ ] Rollback button: torna all'ultimo deploy stabile
- [ ] Dashboard deploy: `/deploy` — lista deploy recenti con stato (running/success/failed)
- [ ] Framework auto-detection: detect Next.js vs Node.js vs static e pre-compila configurazione

### 1.6 User Dashboard

- [ ] Pagina `/dashboard` — overview risorse:
  - Widget riepilogo VPS attivi / in provisioning / spenti
  - Widget risorse totali (CPU, RAM, disk) vs utilizzate
  - Widget ultimi deploy (5 recenti)
  - Widget alert attivi (se presenti)
  - Widget fatturazione: costo mensile attuale, soglie
- [ ] Quick actions: "New VPS", "Deploy to...", "Create backup", "Run diagnostic"
- [ ] Stato servizi: indicatori verdi per tutti i sistemi (API Hetzner, DB, Vercel)

---

## Phase 2 — Produzione 🕐 17-18 anni (Mese 5-8)

> **Obiettivo:** Feature complete per lancio commerciale. Pannello hosting completo, backup, alert, SSL, billing.

### 2.1 Full Hosting Control Panel

- [ ] File Manager:
  - Lista file/directory con icona tipo
  - Upload drag & drop + multi-file
  - Editor testuale inline (CodeMirror/Monaco)
  - Crea, rinomina, elimina, duplica
  - Permessi UNIX (chmod) dal pannello
- [ ] Database Manager:
  - Crea/elimina database MySQL/PostgreSQL
  - phpMyAdmin o interfaccia custom
  - Esegui query con syntax highlighting
  - Import/export SQL
  - Backup database one-click
- [ ] Domain Manager:
  - Associa dominio a VPS
  - Crea subdomain (dev., staging., admin.)
  - Reindirizzamento URL
  - Integrazione sycren-domains per DNS automation
- [ ] One-Click Installers:
  - WordPress (con scelta tema/plugin base)
  - Next.js template (blog, SaaS landing, dashboard)
  - Node.js starter (Express, Fastify, NestJS)
  - Static site (Hugo, Astro, plain HTML)
  - Docker compose template
- [ ] Resource Usage Dashboard:
  - CPU, RAM, disk, bandwidth real-time
  - Storico 24h / 7gg / 30gg / custom
  - Export dati CSV

### 2.2 Backup System

- [ ] Backup schedule configuration: ogni 6h/12h/24h/settimanale, giorni retention
- [ ] Backup engine: hetzner snapshot + rsync per file
- [ ] Backup list: per VPS, con data, dimensione, tipo
- [ ] Restore: selezione backup → restore one-click → conferma → progresso
- [ ] Off-site backup: upload a Hetzner Storage Box o S3
- [ ] Backup encryption: AES-256 con chiave per utente
- [ ] Backup alerts: notifica su fallimento backup
- [ ] Backup storage quota: mostra utilizzo vs limite piano

### 2.3 Advanced Monitoring & Graphs

- [ ] Grafici interattivi: zoom, intervallo custom, overlay metriche multiple
- [ ] Threshold lines: linee tratteggiate per soglie configurate
- [ ] Export grafici: PNG, CSV
- [ ] Status page pubblica: `status.sycren.com` con uptime storico
- [ ] HTTP/S endpoint monitoring: check esterno ogni 60s, response time, status code
- [ ] SSL certificate expiry monitoring: alert 30/14/7 giorni prima
- [ ] Disk space prediction: trend analysis per stimare quando il disco sarà pieno

### 2.4 Alert System

- [ ] Alert rules: per metrica (CPU > 90%, RAM > 85%, disk > 80%, uptime < 99%)
- [ ] Canali: email (Resend), Telegram bot, webhook (Slack/Discord generico)
- [ ] Alert escalation: se non risolto in 15 min → notifica secondo canale
- [ ] Alert history: log di tutti gli alert con timestamp, metrica, valore, risoluzione
- [ ] Alert silence: snooze per 1h/6h/24h
- [ ] Alert dashboard: pagina `/alerts` con tabella filtrata e gestione

### 2.5 Access Logs & Diagnostics

- [ ] Centralized log viewer: `/vps/[id]/logs`
  - Log filtering: per livello (info/warn/error), data, keyword, servizio
  - Real-time tail: streaming log via WebSocket
  - Log retention: 7gg free, 30gg Business, 90gg Enterprise
  - Export: JSON, CSV, plain text
- [ ] Diagnostic tools:
  - Ping: misura latenza verso IP/dominio
  - Traceroute: pathway analysis
  - Port scan: verifica porte aperte
  - DNS lookup: risoluzione dominio
  - HTTP health check: response status + body preview
- [ ] Error aggregation: raggruppa errori simili, mostra occorrenze, trend

### 2.6 SSL Management

- [ ] Let's Encrypt auto-issuance: HTTP challenge (default) + DNS challenge (Cloudflare)
- [ ] SSL dashboard: lista domini, stato SSL, emittente, scadenza, fingerprint
- [ ] Wildcard SSL: emissione e rinnovo automatico per *.dominio.com
- [ ] Custom SSL: upload certificato + chiave privata + CA bundle
- [ ] SSL scan: verifica vulnerabilità, cipher suite, protocolli
- [ ] Auto-renew: 7 giorni prima scadenza, alert se fallisce
- [ ] HSTS header: configurabile dal pannello (max-age, includeSubDomains, preload)

---

## Phase 3 — Scale 🕐 18+ anni (Post-lancio)

> **Obiettivo:** scaling automatico, storage oggetti, multi-region, admin panel.

- [ ] Auto-scaling:
  - Regole: CPU > 80% per 5 min → aggiungi vCore; CPU < 20% per 30 min → rimuovi
  - Scaling orizzontale: aggiungi/rimuovi VPS in cluster
  - Scaling verticale: resize VPS (CPU/RAM) su Hetzner API
  - Cooldown period: 10 min tra scaling events
  - Notification: alert su ogni scaling action
- [ ] Storage Oggetti (S3-compatibile):
  - API compatibile AWS S3 (MinIO o Hetzner Storage Box)
  - Bucket: create, list, delete, policy (public/private)
  - Upload/download via web UI
  - CDN integration: Cloudflare cache per bucket pubblici
  - Presigned URLs: generazione temporanea per download sicuri
- [ ] Multi-Region Support:
  - Nuremberg (nbg1), Falkenstein (fsn1), Helsinki (hel1)
  - Replica backup tra regioni (Enterprise plan)
  - Geo-DNS: record DNS diversi per regione
  - Region transfer: migra VPS da una regione all'altra
- [ ] Admin Panel:
  - `/admin` — solo per admin sycren
  - Lista tutti gli utenti con VPS attivi
  - Resource pool usage: CPU/RAM/disk totali consumati
  - Hetzner credit balance: costo mensile vs ricavi
  - Manual server actions: force shutdown, migrate, override plan
  - Audit log: tutte le azioni admin su VPS utenti

---

## Integrazioni Cross-Platform

### sycren-db

- [ ] Connettere Prisma schema a `sycren-db` come pacchetto npm workspace
- [ ] Usare i tipi TypeScript generati da Prisma in tutta l'applicazione
- [ ] Migrazioni coordinate: non creare migrazioni separate in sycren-cloud
- [ ] Seed data comune: utenti di test, server di esempio, metriche fake
- [ ] Query helper condivisi: paginazione, filtri, aggregazioni

### sycren-app SSO

- [ ] Integrare NextAuth.js per SSO con `app.sycren.com`
- [ ] Login redirect: `cloud.sycren.com/login` → `app.sycren.com/login` → callback
- [ ] JWT token validation: verificare token SSO su ogni richiesta API
- [ ] User roles sync: owner/admin/developer/viewer da app a cloud
- [ ] Widget dashboard su app.sycren.com: riepilogo stato cloud
- [ ] Notifiche unificate: alert cloud inviati al notification center di app

### sycren-app Billing

- [ ] Sync piani di servizio: Starter/Business/Enterprise come prodotti Stripe
- [ ] Webhook Stripe: create/cancel subscription, payment success/failure
- [ ] Resource provisioning on payment: VPS creato solo dopo pagamento confermato
- [ ] Proration: upgrade/downgrade piano a metà mese
- [ ] Invoice integration: fattura unificata con altri servizi Sycren
- [ ] Credit system: crediti prepagati per risorse under/over plan

### sycren-domains

- [ ] Automazione DNS: quando un cliente acquista hosting, CNAME/ A record configurato automaticamente
- [ ] SSL via DNS challenge: integrazione Cloudflare API per emissione wildcard
- [ ] Subdomain panel: crea `dev.tuo.com`, `staging.tuo.com` dal pannello cloud
- [ ] Domain health checker: verifica che il DNS punti al VPS giusto
- [ ] Domain expiry alert: notifica integrata nel pannello cloud

### sycren-security

- [ ] WAF rules: protezione base per siti hosted
- [ ] DDoS protection: attivazione Cloudflare under attack mode
- [ ] Security scan: scan periodico VPS per vulnerabilità
- [ ] Fail2ban integration: log analysis e ban automatico IP malevoli
- [ ] Security report: report mensile con tentativi di intrusione bloccati
- [ ] Log forwarding: log di sicurezza inviati a security.sycren.com

---

## UI / Design System

- [ ] Pagina `/login` — login con redirect SSO + "Powered by Sycren" branding
- [ ] Sidebar navigation: VPS list, Deploy, Monitoring, Backups, SSL, Settings
- [ ] Dashboard widgets: metriche in card con trend up/down indicator
- [ ] VPS status indicator: verde (running), giallo (provisioning), rosso (offline/error), grigio (deleted)
- [ ] Tema scuro/chairo: rispetta preferenza sistema, toggle manuale
- [ ] Responsive design: sidebar collassabile su mobile, card a larghezza piena
- [ ] Empty states: illustrazioni per "No VPS yet", "No deployments", "No backups"
- [ ] Loading skeletons: shimmer per ogni card e tabella
- [ ] Error states: componente errore con retry button e messaggio leggibile
- [ ] Toast notifications: successo/errore per azioni rapide

---

## API Routes

| Route | Metodo | Descrizione | Auth |
|---|---|---|---|
| `/api/vps` | GET | Lista VPS utente | SSO required |
| `/api/vps` | POST | Crea nuovo VPS | SSO required |
| `/api/vps/[id]` | GET | Dettaglio VPS | SSO required |
| `/api/vps/[id]` | DELETE | Elimina VPS | SSO required |
| `/api/vps/[id]/actions` | POST | Reboot, shutdown, rebuild | SSO required |
| `/api/vps/[id]/metrics` | GET | Metriche monitoring | SSO required |
| `/api/vps/[id]/backups` | GET | Lista backup | SSO required |
| `/api/vps/[id]/backups` | POST | Crea backup manuale | SSO required |
| `/api/vps/[id]/backups/[bid]/restore` | POST | Restore da backup | SSO required |
| `/api/deploy` | POST | Crea nuovo deploy | SSO + OAuth |
| `/api/deploy/[id]` | GET | Dettaglio deploy | SSO required |
| `/api/deploy/webhook` | POST | Webhook GitHub push | GitHub secret |
| `/api/ssh-keys` | GET | Lista SSH keys | SSO required |
| `/api/ssh-keys` | POST | Aggiungi SSH key | SSO required |
| `/api/ssh-keys/[id]` | DELETE | Elimina SSH key | SSO required |
| `/api/alerts` | GET | Lista alert rules | SSO required |
| `/api/alerts` | POST | Crea alert rule | SSO required |
| `/api/alerts/[id]` | PUT | Modifica alert rule | SSO required |
| `/api/alerts/[id]` | DELETE | Elimina alert rule | SSO required |
| `/api/ssl` | GET | Lista certificati SSL | SSO required |
| `/api/ssl` | POST | Richiedi nuovo certificato | SSO required |
| `/api/ssl/[id]/renew` | POST | Forza rinnovo SSL | SSO required |
| `/api/billing/usage` | GET | Usage corrente e costo | SSO required |

---

## Testing

- [ ] Unit test per ogni API route (Vitest)
- [ ] Unit test per Hetzner API client (mock axios/fetch)
- [ ] Integration test: provisioning flow completo (mock Hetzner API)
- [ ] Integration test: deploy flow (mock GitHub API + build)
- [ ] Test error handling: rate limit, API down, invalid config
- [ ] Test race conditions: due deploy simultanei, backup durante provisioning
- [ ] Security test: SQL injection, XSS, CSRF su form e input
- [ ] Load test: 10 richieste provisioning simultanee
- [ ] E2E test (Playwright/Cypress):
  - Flow: login → nuovo VPS → provisioning → dashboard → backup → delete
  - Flow: login → connetti GitHub → deploy → view log → rollback

---

## Verifica / Completion Criteria

### Phase 1 — Cloud Lite MVP

- [ ] Creazione VPS via Hetzner API funzionante da UI
- [ ] VPS attivo in < 5 minuti dal click "Provision"
- [ ] SSH key management: upload, generate, associate funzionante
- [ ] Monitoring dashboard: CPU, RAM, disk mostrano dati reali
- [ ] Deploy da GitHub: repository connesso, build eseguita, deploy completato
- [ ] Dashboard utente: VPS list, stato, azioni rapide funzionanti
- [ ] Integrazione sycren-db: Server, SshKey, Deploy modelli popolati
- [ ] Responsive: tutte le pagine principali funzionano su mobile (375px)

### Phase 2 — Produzione

- [ ] File manager: upload, edit, delete, rename funzionanti
- [ ] Database manager: creazione DB, esecuzione query, import/export
- [ ] Backup automatici: schedule funzionante, retention rispettata
- [ ] Restore one-click: backup selezionato → VPS ripristinato
- [ ] Grafici performance: zoom, intervallo, overlay metriche
- [ ] Alert: notifica email/Telegram su soglia superata
- [ ] SSL Let's Encrypt: emissione automatica e rinnovo
- [ ] Log viewer: tail real-time, filtri, export

### Phase 3 — Scale

- [ ] Auto-scaling: regole configurate e funzionanti
- [ ] Storage S3: bucket create, upload/download via API
- [ ] Multi-region: VPS creati in nbg1, fsn1, hel1
- [ ] Admin panel: utenti, risorse, audit log

### Generali

- [ ] Lighthouse Performance ≥ 85 (mobile)
- [ ] LCP < 2.5s su dashboard principale
- [ ] Nessun segreto in repository
- [ ] Variabili d'ambiente documentate in `.env.example`
- [ ] README.md aggiornato con setup instructions
- [ ] LICENSE file presente

---

## Oltre Cloud — Cosa Segue (con Età)

| # | Modulo | Età 🕐 | Dipende da |
|---|---|---|---|
| 1 | Landing Page `sycren.com` | **16** | — |
| 2 | Database Foundation `sycren-db` | **16** | — |
| 3 | Client Portal `studio.sycren.com` | **16** | sycren-db |
| 4 | Hub Centrale / SSO `app.sycren.com` | **16-17** | sycren-db, studio |
| **5** | **Cloud Lite `cloud.sycren.com`** | **17** | sycren-db, app |
| 6 | Domains `domains.sycren.com` | **17-18** | sycren-db, app |
| 7 | Security `security.sycren.com` | **18+** | sycren-db, app, cloud |
| 🎂 | **Lancio Commerciale (P.IVA, Vendite)** | **18** | Tutti i moduli |

---

*Sycren Cloud — TODO v1.0 · Giugno 2026 · Allineato a Bootstrap Roadmap v1.3*
