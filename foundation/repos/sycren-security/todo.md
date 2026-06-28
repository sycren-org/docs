# sycren-security — TODO
> PRD v1.0 · Giugno 2026 · Fase 0 (Pre-lancio) · Allineato a Bootstrap Roadmap v1.3

---

## Contesto (dalla Roadmap)

- Sycren Security è il **modulo #7** della piattaforma, ultimo nell'ordine di costruzione
- Viene sviluppato durante la Fase 0 (16-17 anni) ma **reso disponibile solo al lancio commerciale (18+)** 🕐
- È un **add-on premium** per clienti Cloud esistenti — non esiste come prodotto standalone
- Modello operativo: **reselling Cloudflare Pro** con pannello custom su `security.sycren.com`
- Due founder: **Angelo Paciello** (architettura backend, Cloudflare API, infra) e **Cosimo Russo** (UI/UX, frontend, integrazioni)
- Stima temporale: costruzione parallela durante lo sviluppo di Cloud (17-18 anni), deploy effettivo al 18° compleanno

### Ordine di costruzione della piattaforma

| # | Modulo | Età 🕐 | Responsabile |
|---|---|---|---|
| 1 | Landing Page `sycren.com` | **16** | Angelo (lead), Cosimo (lead) |
| 2 | Database Foundation `sycren-db` | **16** | Angelo (lead) |
| 3 | Client Portal `studio.sycren.com` | **16** | Angelo (lead), Cosimo (lead) |
| 4 | Hub Centrale / SSO `app.sycren.com` | **16-17** | Angelo (lead), Cosimo (supporto) |
| 5 | Cloud Lite `cloud.sycren.com` | **17** | Angelo (lead) |
| 6 | Domains `domains.sycren.com` | **17-18** | — |
| **7** | **Security `security.sycren.com`** | **18+** | — |
| 🎂 | **Lancio Commerciale (P.IVA, Vendite)** | **18** | Entrambi |

---

## Setup Iniziale

- [ ] Inizializzare progetto Next.js 14 con App Router e TypeScript strict
- [ ] Configurare Tailwind CSS 3.4+ con design token dal sycren-design-system
- [ ] Installare dipendenze: Recharts, Lucide React, React Hook Form, Zod, jsPDF/Puppeteer
- [ ] Configurare next/font con Geist Mono e Inter (coerente con landing page)
- [ ] Creare `.env.example` con tutte le variabili d'ambiente (Cloudflare API, Resend, Telegram, DB)
- [ ] Impostare struttura cartelle:
  ```
  src/
    app/          (pagine e route)
    components/   (UI components)
    lib/          (Cloudflare API client, utility)
    hooks/        (custom hooks)
    types/        (TypeScript types)
    utils/        (helper functions)
  ```
- [ ] Configurare Vercel: collegare repo, impostare ENV, dominio `security.sycren.com`
- [ ] Configurare Cloudflare: API token con permessi WAF, DDoS, SSL, Analytics
- [ ] Verificare che il pacchetto `sycren-db` sia installabile come dipendenza

---

## Integrazione sycren-db

Prima di qualsiasi sviluppo UI, il database schema deve essere pronto. Lo schema Prisma centralizzato in `sycren-db` deve includere le tabelle per Security.

- [ ] Aggiungere modello `SecurityService` a `sycren-db`:
  - `id`, `clientId`, `cloudServiceId` (nullable — per VPS protetti), `domainId` (nullable — per domini protetti)
  - `wafEnabled`, `wafProfile`, `wafCustomRules` (JSON)
  - `ddosEnabled`, `ddosMode`, `ddosThreshold`
  - `monitoringEnabled`, `monitoringInterval`
  - `vulnScanEnabled`, `vulnScanInterval`, `vulnScanDay`
  - `sslProfile`, `hstsEnabled`, `hstsMaxAge`
  - `status` (active / paused / error), `createdAt`, `updatedAt`
- [ ] Aggiungere modello `SecurityAlert`:
  - `id`, `serviceId`, `type`, `severity`, `title`, `message`, `metadata` (JSON)
  - `channel` (email / telegram / webhook), `deliveredAt`, `readAt`
  - `createdAt`
- [ ] Aggiungere modello `SecurityLog`:
  - `id`, `serviceId`, `type` (access / error / waf_block / intrusion / ssl / system)
  - `severity` (info / warning / critical), `sourceIp`, `message`, `metadata` (JSON)
  - `createdAt` (con indice per performance query)
- [ ] Aggiungere modello `SecurityReport`:
  - `id`, `clientId`, `period` (monthly / weekly / on_demand)
  - `periodStart`, `periodEnd`, `generatedAt`
  - `pdfUrl`, `htmlContent`, `summary` (JSON)
- [ ] Aggiungere modello `SecurityVulnerability`:
  - `id`, `serviceId`, `cveCode`, `severity`, `title`, `description`
  - `remediation`, `status` (open / in_progress / resolved / accepted_risk)
  - `foundAt`, `resolvedAt`
- [ ] Eseguire migrazione Prisma e generare tipi TypeScript
- [ ] Aggiornare `sycren-db` index.ts per esportare i nuovi modelli

---

## Integrazione sycren-app SSO

- [ ] Implementare middleware Next.js per verifica sessione (cookies/JWT da sycren-app)
- [ ] Aggiungere callback di login: redirect da `app.sycren.com/auth/callback` a `security.sycren.com`
- [ ] Gestire ruoli: `owner`, `admin`, `developer`, `viewer` (mappati da sycren-app)
- [ ] Logout: invalidare sessione e redirect a `app.sycren.com`
- [ ] Proteggere tutte le route API con verifica autenticazione
- [ ] Aggiungere profilo utente nell'header: avatar, nome, dropdown menu

---

## Phase 1: MVP (~200 ore stimate)

**Obiettivo:** Piattaforma funzionante al lancio commerciale (18+) con WAF base, monitoring, alerting e log.

### 1.1 Security Dashboard

- [ ] Layout principale: sidebar navigazione (Dashboard, WAF, DDoS, Monitoring, Logs, Alerts, Reports, SSL, Settings)
- [ ] Hero metric cards: eventi totali (24h), blocchi WAF, servizi protetti, alert critici
- [ ] Mini grafico: richieste al minuto (bloccate vs permesse) — ultime 24 ore
- [ ] Event timeline: lista scrollabile ultimi 50 eventi con severità colorata
- [ ] Service cards: ogni servizio protetto con badge stato, ultimo evento, link rapido a dettaglio
- [ ] Filtri dashboard: data range, severità, servizio
- [ ] Auto-refresh ogni 30 secondi con indicator di aggiornamento
- [ ] Widget "Quick Actions": Attiva WAF su servizio, Visualizza log, Genera report

### 1.2 Cloudflare WAF Integration

- [ ] Aprire account Cloudflare e ottenere API token con permessi: `zone:read`, `waf:write`, `ddos:write`, `analytics:read`, `ssl:write`
- [ ] Implementare `CloudflareAPIClient` in `src/lib/cloudflare.ts`:
  - Metodi: `getZones()`, `getWafStatus(zoneId)`, `setWafProfile(zoneId, profile)`, `getWafEvents(zoneId, params)`
  - Rate limiting: 1200 req/5min (limite Cloudflare), retry con backoff, queue richieste
- [ ] WAF toggle page: lista servizi protetti, attiva/disattiva con conferma
- [ ] WAF profile selector: Low / Medium / High con descrizione tooltip
- [ ] Rate limiting config: soglia (100/300/1000 req/min), durata ban (5/15/60 min), URI esclusi
- [ ] IP whitelist/blacklist: input manuale + import CSV, visualizzazione lista con rimozione
- [ ] Blocked countries: multi-select da lista paesi, salva per zona
- [ ] WAF statistics widget: richieste totali, bloccate, top IP, top paesi — ultimi 7 giorni
- [ ] Test Cloudflare API connection: pagina `/admin/cloudflare-test` con risultato live

### 1.3 Basic Monitoring

- [ ] Uptime check: endpoint `/api/monitoring/check` che esegue HTTP GET a ogni servizio protetto
- [ ] Scheduler: cron job Vercel (CRON tab) ogni 60 secondi
- [ ] Status history: grafico uptime 24h / 7gg / 30gg per servizio
- [ ] Anomaly detection base: confronto traffico attuale vs media ultime 24h, alert se > 3 deviazioni standard
- [ ] Intrusion attempt viewer: tabella IP, tentativi, timestamp, paese, azione intrapresa
- [ ] SSL certificate status: lista certificati con scadenza, validità, giorni rimanenti
- [ ] Health check endpoint: `/api/health` per monitoraggio interno (usato da sycren-app)

### 1.4 Alert Configuration

- [ ] Alert settings page: tabella regole attive con toggle
- [ ] Aggiunta alert rule: tipo evento, soglia, canale, cooldown, schedule
- [ ] Email alert: integrazione Resend, template HTML predefiniti per tipo evento
  - Template: DDoS attack detected, WAF spike, Brute force, SSL expiring, Vulnerability found
  - Oggetto: `[Sycren Security] {severity} — {title} — {service_name}`
- [ ] Telegram alert: bot setup, invio messaggio con formattazione Markdown
  - Messaggio: `🔴 CRITICAL | DDoS Attack on server-01\nVolume: 500 Mbps\nAction: Auto-mitigation active\nLink: https://security.sycren.com/events/123`
- [ ] Webhook alert: POST JSON a URL configurato con payload standardizzato
  - Payload: `{ event, severity, title, message, service, timestamp, link }`
- [ ] Test alert button: invia alert di test su tutti i canali configurati
- [ ] Alert history: log di tutti gli alert inviati con stato consegna

### 1.5 Centralized Logs

- [ ] Log viewer page: tabella virtualizzata (>10.000 righe) con colonne timestamp, tipo, severità, servizio, IP, messaggio
- [ ] Ricerca full-text con query parser:
  - `ip:192.168.1.1`, `severity:critical`, `type:waf_block`, `service:server-01`
  - Combinazioni: `severity:critical type:waf_block` (AND)
  - Filtri data range con date picker
- [ ] Severity badge: 🔴 Critical, 🟡 Warning, 🔵 Info — con colore e icona
- [ ] Log detail modal: click su riga apre dettaglio completo con metadata JSON formattato
- [ ] Export: CSV (tutti i campi), JSON (raw), PDF (selezione data range, max 1000 righe)
- [ ] Auto-refresh toggle: quando attivo, nuovi log appaiono in tempo reale (polling ogni 10 secondi)
- [ ] Log retention indicator: mostra percentuale storage utilizzato, giorni rimanenti

---

## Phase 2: Advanced Features (~300 ore stimate)

**Obiettivo:** Aggiungere funzionalità avanzate per clienti Pro/Enterprise, differenziare il prodotto dal reselling puro.

### 2.1 Custom WAF Rules Editor

- [ ] Rules list page: tabella regole custom (nome, condizione, azione, stato, priorità)
- [ ] Visual rule editor:
  - Campo: IP, Paese, User-Agent, URI, Query String, Header, Cookie, Body
  - Operatore: equals, contains, regex, starts_with, ends_with, ip_range
  - Valore: input text o select (per campi predefiniti)
  - Azione: Block / Challenge / JS Challenge / Allow / Log
  - Aggiungi condizione: AND/OR tra condizioni multiple
  - Drag & drop per ordinamento priorità
- [ ] Rule tester: input richiesta HTTP di test, mostra quale regola matcha e azione intrapresa
- [ ] Rule versioning: cronologia modifiche (data, autore, diff), rollback one-click
- [ ] Rule templates: salva regola come template, applica a più servizi
- [ ] Pre-built rule templates: OWASP Top 10, WordPress protection, API protection, Login brute force

### 2.2 DDoS Mitigation Controls

- [ ] DDoS dashboard: grafico traffico (Gbps, Mpps), timeline attacchi, servizi sotto attacco
- [ ] Mitigation mode: Automatic / Manual / Off per servizio
- [ ] Manual override: pulsante "Mitigate Now" per attivare protezione manualmente
- [ ] Threshold customization: sensitivity slider (Low / Medium / High) per servizio
- [ ] Attack history: lista attacchi con data, durata, tipo, volume max, azione, note
- [ ] Post-attack report: sommario automatico con IP coinvolti, vettori, grafico timeline
- [ ] DDoS alert rule: notifica solo se attacco supera X Mbps o Y Mpps

### 2.3 Vulnerability Scanner Integration

- [ ] Scanner engine: integrazione API esterna (NVD/CVE database lookup) o tool open source (Nuclei)
- [ ] Scan scheduling: setup wizard — servizio, frequenza (weekly/biweekly/monthly), tipo (port / CVE / config)
- [ ] Scan results: tabella vulnerabilità (CVE code, severity, title, affected component, CVSS score)
- [ ] Vulnerability detail: descrizione CVE, link a NVD/NIST, remediation steps, affected versions
- [ ] Remediation tracking: status (Open / In Progress / Resolved / Accepted Risk), assignee, commenti
- [ ] Notification: alert su nuove vulnerabilità critiche/critical trovate
- [ ] Remediation SLA: Critical < 48h, High < 7gg, Medium < 30gg, Low < 90gg
- [ ] Vulnerability report PDF: executive summary per management + technical appendix

### 2.4 Monthly Report Generation

- [ ] Report engine: raccoglie dati da WAF stats, DDoS events, monitoring, vulnerability scans
- [ ] Report template: executive summary, threat statistics (grafici), top events, uptime, vulnerabilities, recommendations
- [ ] Auto-generation: cron giornaliero che controlla se è primo del mese, genera report per tutti i clienti
- [ ] PDF generation: jsPDF + html2canvas o Puppeteer per PDF professionale
- [ ] Report preview: anteprima HTML nel browser prima del download
- [ ] Email delivery: invia PDF come allegato, riassunto inline nel corpo email
- [ ] Report archive: storico report consultabile, download passati
- [ ] On-demand report: pulsante "Genera Report Ora" nella pagina Reports

### 2.5 SSL/TLS Management Panel

- [ ] SSL overview: lista domini/servizi con stato certificato, emittente, scadenza, SANs
- [ ] SSL profile selector: Modern (TLS 1.3 only) / Intermediate (TLS 1.2 + 1.3) / Old (backward compatible)
  - Tooltip: mostra dettaglio cipher suite per profilo
- [ ] HSTS configuration: toggle on/off, max-age (6/12/24 mesi), includeSubDomains, preload
- [ ] Certificate Transparency: stato log CT, alert su certificati non autorizzati emessi per il dominio
  - Check giornaliero dei log CT pubblici (crtsh)
- [ ] SSL check: scan automatico configurazione SSL (Qualys SSL Labs grade), report mensile
- [ ] Auto-renew: integrazione Cloudflare Origin CA per emissione rinnovo automatico
- [ ] SSL expiration tracker: countdown giorni, alert a 90/30/7 giorni dalla scadenza

---

## Phase 3: Premium Services (~200 ore stimate)

**Obiettivo:** Servizi a valore aggiunto che giustificano il posizionamento premium e generano revenue one-shot.

### 3.1 Penetration Testing Request Flow

- [ ] Request form: tipo test (white-box / black-box), descrizione perimetro, urgenza, deadline desiderata
- [ ] Integrated pricing calculator: stima automatica basata su tipo e complessità
- [ ] Quote generation: PDF preventivo automatico, inviato al cliente via email
- [ ] Acceptance flow: cliente accetta preventivo → scheduling → stato "In Progress" con timeline
- [ ] Delivery: upload report PDF, notifica al cliente, debrief call scheduling
- [ ] Archive: storico penetration test per cliente, report consultabili

### 3.2 Incident Response Playbook

- [ ] Playbook documentato: procedure per tipo incidente (DDoS, data breach, ransomware, account compromise)
  - Fasi: Detection → Triage → Containment → Eradication → Recovery → Post-mortem
  - Template comunicazione: interno, cliente, autorità (DPO/Garante)
- [ ] Incident log: timeline eventi durante la risposta, note del team, azioni intraprese
- [ ] Emergency contact: pulsante "Emergency" nella dashboard che notifica immediatamente il team Sycren
- [ ] Post-incident report: template per report finale con raccomandazioni

### 3.3 GDPR Compliance Checklist

- [ ] Interactive checklist: categorie (Data Mapping, Consent Management, DPO, Data Breach, Rights of Subjects, Documentation)
  - Ogni item: titolo, descrizione, status (Not Started / In Progress / Completed / N/A), note, evidenza (file upload)
- [ ] Compliance score: percentuale completamento per categoria, overall score
- [ ] Export: report compliance PDF per autorità o audit interno
- [ ] Data breach notification: template e flow di notifica al Garante (entro 72h)
- [ ] ROPA (Record of Processing Activities): form per tenere traccia delle attività di trattamento

### 3.4 Security Audit Tool

- [ ] Code review checklist: OWASP ASVS (Application Security Verification Standard) items
- [ ] Dependency scanner: analisi package.json, requirements.txt, Cargo.toml per CVE note
- [ ] Configuration review: checklist per server config, database config, cloud provider config
- [ ] Audit report: PDF con findings, severity, remediation timeline
- [ ] Integration con sycren-studio: quando un cliente Studios richiede audit, il report è disponibile in `studio.sycren.com`

---

## Integrazione Cross-Platform

### sycren-cloud (server protection)
- [ ] Aggiungere pulsante `Enable Security` nella dashboard server di `cloud.sycren.com`
- [ ] Ricevere webhook da sycren-cloud: nuovo VPS creato, VPS rimosso
- [ ] Mostrare stato sicurezza (Protected / Unprotected) nella lista server Cloud
- [ ] Condividere metriche di traffico tra Cloud Monitoring e Security Analytics

### sycren-domains (domain protection)
- [ ] Aggiungere toggle `Security Protection` nella dashboard dominio di `domains.sycren.com`
- [ ] Ricevere webhook da sycren-domains: nuovo dominio registrato, dominio scaduto
- [ ] Applicare WAF a livello di zona DNS quando protezione attivata
- [ ] SSL certificates gestiti automaticamente per domini protetti

### sycren-app (hub centrale)
- [ ] Widget Security nella dashboard principale di `app.sycren.com`: eventi critici, servizi protetti, alert attivi
- [ ] SSO: login redirect, callback, gestione sessione
- [ ] Notifiche unificate: alert di sicurezza compaiono nel feed notifiche di app.sycren.com
- [ ] Billing: piani Security visibili nella sezione Billing
- [ ] Settings: configurazione alert preferita accessibile da app.sycren.com

### sycren-db (database foundation)
- [ ] Verificare che `sycren-db` sia importabile come dipendenza npm (o git submodule)
- [ ] Eseguire `prisma migrate` per applicare nuovi modelli Security
- [ ] Testare che i tipi generati siano corretti e utilizzabili

---

## Oltre il Lancio — Cosa Segue (con Età)

| # | Obiettivo | Età 🕐 | Dettaglio |
|---|---|---|---|
| 1 | Chiusura 2-4 clienti Studios | **18** | Primi clienti, fatture emesse, feedback su piattaforma |
| 2 | Attivazione primi abbonamenti Security | **18** | Clienti Cloud che attivano Security Basic/Pro |
| 3 | Security MRR > €500 | **18** | ~10-15 clienti Basic + 2-3 Pro |
| 4 | Apertura SRL | **18** (mese 3-6) | Con MRR stabilmente > €3.000 |
| 5 | Primo pen test venduto | **18-19** | Cliente Enterprise che richiede pen testing |
| 6 | Security MRR > €1.200 | **19** | Obiettivo roadmap Fase 3 |
| 7 | Team: primo Security Engineer | **19** | Non più solo reselling, capacità interne |

---

## Verification Criteria

Prima di rendere `security.sycren.com` disponibile ai clienti (al lancio commerciale, 18+):

- [ ] Cloudflare API integration testata: WAF attiva/disattiva, regole applicate, statistiche lette
- [ ] Alert inviati e ricevuti correttamente su tutti e 3 i canali (email, Telegram, webhook)
- [ ] Log viewer carica >10.000 righe senza degrado performance
- [ ] Dashboard mostra dati reali da almeno un servizio di test
- [ ] SSO funzionante: login da `app.sycren.com` → redirect → sessione attiva
- [ ] Integrazione sycren-cloud: protezione attivabile per VPS di test
- [ ] Integrazione sycren-domains: protezione attivabile per dominio di test
- [ ] Report mensile generato con dati corretti, PDF scaricabile
- [ ] Nessun segreto o API token in repository
- [ ] Deploy su Vercel con dominio `security.sycren.com` e SSL attivo
- [ ] Lighthouse Performance ≥ 85 mobile (dashboard pesante per i grafici)
- [ ] Responsive: layout funzionante da 375px a 1920px

---

*Sycren Security — TODO v1.0 · Giugno 2026*
*Allineato a Bootstrap Roadmap v1.3. Documento interno. Non distribuire senza autorizzazione scritta.*
