# sycren-cloud — Requisiti di Prodotto (PRD)
> PRD v1.0 — Giugno 2026 — Uso interno — Allineato a Bootstrap Roadmap v1.3

---

## 1. Contesto e Obiettivo

Sycren Cloud è la divisione di hosting gestito e infrastruttura cloud della piattaforma Sycren, accessibile su `cloud.sycren.com`. L'obiettivo è offrire hosting performante, VPS provisioning self-service, deploy automatizzato da GitHub e monitoraggio infrastruttura — con un pannello di controllo unificato e una gestione semplificata.

**Strategia bootstrap (Soluzione B):** entrambi i founder hanno 17 anni 🕐 al momento dello sviluppo di Cloud. Il modello operativo iniziale non prevede infrastruttura propria, ma reselling di VPS Hetzner con markup del 40–60%. Il pannello custom astrae il provider sottostante, permettendo in futuro di passare a infrastruttura proprietaria senza impatto sul cliente.

**Principio guida:**
> Costruisci il prodotto ora. Reselling all'inizio. Margine sui servizi, non sull'hardware. La struttura segue i ricavi, non li precede.

**Obiettivo primario:** Offrire hosting gestito e VPS con provisioning automatico, deploy CI/CD e monitoring — tutto da un unico pannello. Generare MRR ricorrente da clienti Studios esistenti e nuovi clienti Cloud.

**Obiettivo secondario:** Astrarre il provider infrastrutturale (Hetzner/DigitalOcean) dietro un pannello custom Sycren, costruendo la base per un futuro cloud proprietario.

**Posizionamento nel portfolio Sycren:**
- Cloud è il quinto modulo nella roadmap di costruzione (dopo Landing → sycren-db → Studio → App)
- Si rivolge a clienti Studios che necessitano di hosting, e a developer esterni che cercano un pannello hosting moderno
- È il primo generatore di MRR ricorrente della piattaforma

**Stato attuale (Giugno 2026):**
- [x] Organizzazione GitHub `sycren` creata
- [x] Repository `sycren-cloud` inizializzato
- [x] Documentazione di fondazione (README.md)
- [ ] Integrazione Hetzner API operativa
- [ ] Pannello di controllo hosting in sviluppo
- [ ] VPS provisioning flow da completare
- [ ] Deploy da GitHub da implementare

---

## 2. Utenti Target

| Segmento | Caratteristiche | Esigenze |
|---|---|---|
| Developer solista / freelance | Necessita di VPS economico per deploy progetti. Vuole automation ma senza complessità DevOps. | Deploy rapido da GitHub, SSH key management, monitoring base, prezzo contenuto. |
| Sysadmin PMI | Gestisce infrastruttura per piccola-media azienda. Cerca pannello centralizzato per server, backup, domini. | Backup automatici, restore one-click, log accesso, alert configurabili, SSL management. |
| Product Manager SaaS | Vuole deployare e monitorare l'app senza occuparsi di infrastruttura. Preferisce piano business con SLA. | Deploy automation, uptime monitoring, auto-scaling, supporto prioritario. |
| Clienti Studios esistenti | Hanno già un sito/app sviluppata da Sycren Studios. Necessitano di hosting gestito senza dover cambiare provider. | Deploy CI/CD integrato, dominio puntato, SSL, backup, assistenza unificata. |
| Startup early-stage | Budget limitato, necessità di VPS/S3 storage per MVP. Valutano rapporto qualità-prezzo e semplicità. | VPS economico, deploy rapido, storage oggetti, pannello intuitivo. |

**Lingua della piattaforma: Inglese.** Il mercato è internazionale. Localizzazioni secondarie in roadmap futura.

---

## 3. Requisiti Funzionali — Dettaglio Feature

### 3.1 Pannello di Controllo Hosting

Il cuore di sycren-cloud. Un pannello centralizzato per la gestione di hosting, file, database, domini e SSL.

**Requisiti:**

- **File Manager:** Interfaccia web per upload, download, modifica, rinomina, eliminazione file. Supporto drag & drop. Editor di testo inline per file di configurazione.
- **Database Manager:** Creazione, gestione e backup di database MySQL/PostgreSQL. phpMyAdmin o interfaccia custom integrata. Visualizzazione tabelle, esecuzione query rapide.
- **Domain Manager:** Associazione domini a siti/VPS. Puntamento automatico record DNS via integrazione con sycren-domains. Gestione subdomain e alias.
- **One-Click Installers:** WordPress, Next.js template, Node.js starter, static site generators. Possibilità di creare e salvare template custom.
- **Resource Usage Dashboard:** CPU, RAM, disk, bandwidth in tempo reale. Storico 24h/7gg/30gg.
- **PHP/Node.js Version Manager:** Cambio versione runtime per ogni sito/VPS. Configurazione variabili d'ambiente.

### 3.2 VPS Provisioning

Sistema self-service per la creazione e gestione di VPS. Il cliente seleziona risorse e il VPS viene provisionato automaticamente via Hetzner API.

**Requisiti:**

- **Resource Selection:** Interfaccia per selezionare CPU (vCore), RAM (GB), disk (GB), region (datacenter). Prezzo calcolato e mostrato in tempo reale.
- **OS/Image Selection:** Ubuntu, Debian, Alpine, CentOS, Fedora. Possibilità di immagini personalizzate (con Docker pre-installato, stack LEMP, ecc.).
- **SSH Key Management:** Upload e salvataggio chiavi SSH per accesso ai VPS. Generazione guidata di nuova coppia di chiavi. Associazione chiavi al momento del provisioning.
- **Automated Provisioning:** Chiamata API Hetzner per creazione server. Installazione OS, configurazione SSH, setup firewall base. Tempo target: < 5 minuti dal click al server pronto.
- **VPS Dashboard:** Lista VPS attivi con stato, IP, risorse, uptime. Azioni rapide: restart, shutdown, rebuild, delete. Accesso console web.
- **Snapshots & Images:** Creazione snapshot manuale. Rebuild da snapshot. Salvataggio immagini custom.

### 3.3 Automated Deploy da GitHub

Sistema CI/CD integrato per deploy automatico di applicazioni da repository GitHub.

**Requisiti:**

- **GitHub Connection:** OAuth per connettere account GitHub. Selezione repository da deployare.
- **Deploy Configuration:** Branch selezionabile per deploy. Build command, output directory, environment variables. Auto-deploy on push (via webhook).
- **One-Click Deploy:** Deploy manuale con un click da qualsiasi branch. Rollback all'ultimo deploy stabile.
- **Deploy Logs:** Output build in tempo reale. Storico deploy con timestamp, commit, autore, esito.
- **Environment Management:** Variabili d'ambiente configurate dal pannello. Secrets criptati. Multi-ambiente (staging/production).
- **Framework Detection:** Rilevamento automatico di framework (Next.js, Node.js, static HTML, Python) e configurazione build predefinita.
- **Integration sycren-studio:** Collegamento diretto: progetti Studios → deploy su cloud.sycren.com con un click.

### 3.4 Monitoring

Sistema di monitoraggio uptime e performance per VPS e applicazioni.

**Requisiti:**

- **Uptime Monitoring:** Ping/HTTP check ogni 60 secondi. Storico uptime giornaliero/settimanale/mensile. Status page pubblica opzionale.
- **Performance Graphs:** CPU, RAM, disk I/O, network bandwidth in tempo reale. Grafici interattivi (intervallo selezionabile: 1h, 6h, 24h, 7gg, 30gg). Dati aggregati per risparmiare storage.
- **Configurable Alerts:** Soglie configurabili per CPU/RAM/disk/uptime. Canali di notifica: email, Telegram, webhook. Alert escalation se non risolto dopo X minuti.
- **Access Logs:** Log di accesso HTTP (richieste, status code, IP). Log di sistema (syslog, auth.log). Ricerca e filtro per data/IP/status. Export in JSON/CSV.
- **Diagnostics:** Traceroute, ping, port check dal pannello. Health check endpoint configurati dall'utente.

### 3.5 Backups

Sistema di backup automatico con retention configurabile e restore one-click.

**Requisiti:**

- **Automatic Backups:** Schedule configurabile (ogni 6h/12h/24h/settimanale). Backup incrementali per risparmiare spazio. Retention configurabile: 7gg, 30gg, 90gg.
- **Manual Backups:** Backup on-demand con un click. Snapshot prima di aggiornamenti critici.
- **One-Click Restore:** Selezione backup da lista → restore con un click. Conferma prima della sovrascrittura. Rollback in caso di errore.
- **Off-Site Storage:** Backup caricati su storage esterno (S3-compatibile, Hetzner Storage Box). Crittografia backup (AES-256).
- **Backup Status Dashboard:** Stato ultimo backup per ogni VPS. Dimensione backup, prossimo backup programmato. Alert se backup fallito.

### 3.6 SSL Management

Gestione certificati SSL per domini e VPS.

**Requisiti:**

- **Automatic SSL (Let's Encrypt):** Emissione e rinnovo automatico certificati Let's Encrypt. Wildcard SSL per subdomain. Integrazione con Cloudflare API per DNS challenge.
- **Custom SSL Upload:** Upload certificato e chiave privata per SSL enterprise/esteso.
- **SSL Dashboard:** Lista domini con stato certificato, data scadenza, emittente. Alert email/Telegram 30gg/14gg/7gg prima della scadenza.
- **SSL Monitoring:** Scansione settimanale certificati per vulnerabilità (Heartbleed, POODLE, ecc.). Report conformità TLS 1.3.

### 3.7 Logs

Sistema centralizzato di logging per VPS e applicazioni.

**Requisiti:**

- **Centralized Log Viewer:** Interfaccia unica per log di tutti i VPS. Ricerca full-text, filtri per livello (info, warn, error), data, servizio.
- **Log Retention:** Retention configurabile: 7gg, 30gg, 90gg, 1 anno. Archivio compresso dopo retention.
- **Real-Time Tail:** Streaming log in tempo reale via WebSocket. Filtro live per keyword.
- **Log Export:** Export in JSON, CSV, plain text. Integrazione webhook per forwarding a servizi esterni (Datadog, Grafana, self-hosted).
- **Error Aggregation:** Raggruppamento errori simili. Conteggio occorrenze. Alert su soglia di errori.

### 3.8 Storage Oggetti (S3-compatibile)

Servizio di object storage per file statici, media, backup.

**Requisiti:**

- **S3-Compatible API:** API compatibile con AWS S3. SDK per upload/download da applicazioni. Bucket pubblici/privati.
- **Web UI:** Upload/download dal pannello. Gestione permessi bucket. Generazione URL presigned per download temporanei.
- **CDN Integration:** Integrazione con Cloudflare CDN per distribuzione globale file statici. Cache invalidation policy.
- **Pricing:** Pay-per-GB storage + bandwidth. Piano gratuito (5GB) per clienti Business.

---

## 4. Piani di Servizio

| Piano | Prezzo | VPS | Storage | Backup | Deploy | Monitoring | Supporto |
|---|---|---|---|---|---|---|---|
| **Starter** | €9/mese | 1 vCore, 2GB RAM, 20GB NVMe | 5GB S3 | Settimanale (7gg retention) | — | Uptime base | Email |
| **Business** | €29/mese | 2 vCore, 4GB RAM, 40GB NVMe | 20GB S3 | Giornaliero (30gg ret.) | GitHub CI/CD | Completo + Alert | Email + Telegram |
| **Enterprise** | €79/mese | 4 vCore, 8GB RAM, 80GB NVMe | 100GB S3 | Ogni 6h (90gg ret.) | CI/CD + Staging | Completo + Status page | Prioritario 24/7 |
| **Custom** | Da €199/mese | Configurabile | Configurabile | Custom | Custom | Custom | Dedicato |

**Modello operativo iniziale:**
- VPS Hetzner CX22 (€4/mese) → rivenduto come Starter a €9/mese (markup 125%)
- VPS Hetzner CX32 (€7/mese) → rivenduto come Business a €29/mese (markup 314%)
- VPS Hetzner CX42 (€14/mese) → rivenduto come Enterprise a €79/mese (markup 464%)
- Il markup più alto sui piani superiori include il valore aggiunto del software Sycren (pannello, deploy CI/CD, monitoring, backup)

**Sconti annuali:**
- Pagamento annuale: 2 mesi gratis (effettivo ~€7.5/mese Starter)
- Sconto clienti Studios esistenti: 15% sul primo anno

---

## 5. Modello Operativo Iniziale

### 5.1 Provider Infrastrutturale

Nella fase iniziale, sycren-cloud opera esclusivamente come **reseller Hetzner**. Nessuna infrastruttura fisica propria.

| Provider | VPS Base | Costo/mese | Rivenduto come | Prezzo |
|---|---|---|---|---|
| Hetzner CX22 | 2 vCore, 4GB RAM, 40GB | €3.99 | Starter | €9/mese |
| Hetzner CX32 | 4 vCore, 8GB RAM, 80GB | €6.99 | Business | €29/mese |
| Hetzner CX42 | 8 vCore, 16GB RAM, 160GB | €13.99 | Enterprise | €79/mese |
| Hetzner CX52 | 16 vCore, 32GB RAM, 320GB | €26.99 | Custom | Da €199/mese |

### 5.2 Markup Strategy

Il markup del 40-600% è giustificato da:
1. **Software layer:** pannello di controllo custom, deploy automation, monitoring, backup, SSL management
2. **Integrazioni:** SSO con sycren-app, billing unificato, dominio automatico con sycren-domains
3. **Supporto:** gestione ticket, alerting, troubleshooting incluso
4. **Brand:** hosting con marchio Sycren, fattura unica

### 5.3 Stack Iniziale

| Componente | Tecnologia |
|---|---|
| Frontend | Next.js 14+ (App Router) |
| Language | TypeScript 5+ (strict) |
| Styling | Tailwind CSS 3.4+ |
| Database ORM | Prisma (via sycren-db) |
| Provider API | Hetzner Cloud API (REST) |
| Container | Docker |
| Monitoring | Custom (chart.js/Recharts, WebSocket) |
| Backup Storage | Hetzner Storage Box (S3) |
| Queue | Redis (Bull/BullMQ) |

### 5.4 Variabili d'Ambiente

```bash
HETZNER_API_TOKEN=xxxxxxxxxxxxx
HETZNER_SSH_KEY_ID=123456
DEFAULT_REGION=nbg1
BACKUP_STORAGE_ENDPOINT=https://storagebox.hetzner.cloud
BACKUP_STORAGE_ACCESS_KEY=xxx
BACKUP_STORAGE_SECRET_KEY=xxx
NEXT_PUBLIC_CLOUD_URL=https://cloud.sycren.com
DATABASE_URL=postgresql://...
```

---

## 6. Stack Tecnologico

| Layer | Tecnologia | Versione |
|---|---|---|
| Framework | Next.js (App Router) | 14+ |
| Language | TypeScript | 5+ (strict) |
| Styling | Tailwind CSS | 3.4+ |
| Database ORM | Prisma (via sycren-db) | 5+ |
| Provider API | Hetzner Cloud API | REST v1 |
| API Client | tRPC | 10+ |
| Forms | React Hook Form | 7+ |
| Validation | Zod | 3+ |
| Charts | Recharts | 2+ |
| Icons | Lucide React | Latest |
| Container | Docker | 24+ |
| CI/CD | GitHub Actions | — |
| Queue | Redis + BullMQ | — |
| Monitoring | Custom WebSocket | — |
| Backup | Hetzner Storage Box / S3 | — |
| Deploy | GitHub Webhooks | — |

---

## 7. KPI Target

| Metrica | Target | Misurazione |
|---|---|---|
| Uptime garantito (SLA) | 99.9% | Monitoraggio esterno ogni 60s |
| Recovery Time Objective (RTO) | < 1 ora | Tempo da alert a ripristino |
| Recovery Point Objective (RPO) | < 24 ore | Perdita massima dati accettabile |
| Provisioning nuovo VPS | < 5 minuti | Da click utente a server attivo |
| Deploy da GitHub | < 3 minuti | Da push a deploy completato |
| Backup completion rate | > 99.5% | Backup riusciti / backup programmati |
| SSL auto-rinnovo success | > 99% | Certificati rinnovati senza intervento |
| Pannello uptime | 99.95% | Disponibilità interfaccia web |
| Alert response time | < 5 minuti | Da soglia superata a notifica inviata |

---

## 8. Integrazioni Cross-Platform

### 8.1 sycren-app SSO (app.sycren.com)

- Single Sign-On: login unico per tutta la piattaforma Sycren
- Billing unificato: fattura unica per tutti i servizi cloud
- Dashboard widget: riepilogo stato VPS, risorse, alert, backup
- Notifiche unificate: alert infrastruttura visibili nel feed notifiche app.sycren.com
- Gestione ruoli: owner, admin, developer, viewer (rispetto a VPS e servizi)

### 8.2 sycren-db (database foundation)

- Schema Prisma condiviso: tabelle utenti, server, backups, deployments, monitoring_data
- Tipi TypeScript generati da Prisma
- Utility DB condivise per query comuni
- Migrazioni coordinate con il pacchetto centralizzato

### 8.3 sycren-domains (domains.sycren.com)

- Puntamento DNS automatico: quando un cliente acquista hosting, il dominio viene configurato automaticamente
- SSL via DNS challenge: integrazione Cloudflare API per emissione certificati
- Subdomain management: creazione subdomain dal pannello cloud
- Domain health check: verifica configurazione DNS del dominio associato al VPS

### 8.4 sycren-security (security.sycren.com)

- WAF per hosting: protezione firewall applicativo per siti su cloud
- DDoS mitigation: attivazione protezione su VPS
- Security monitoring: alert per tentativi di intrusione, scan porte, traffico anomalo
- SSL Security Scan: verifica vulnerabilità certificati e cipher suite
- Log integration: forwarding log di sicurezza a security.sycren.com

### 8.5 sycren-studio (studio.sycren.com)

- Deploy diretto: progetto Studios → deploy su cloud.sycren.com con un click
- Hosting incluso: piani Studios possono includere hosting Starter
- Client portal: il cliente vede stato hosting nel proprio portale Studios

---

## 9. Criteri di Completamento

### Fase 1 — Cloud Lite MVP

- [ ] Integrazione API Hetzner: creazione, cancellazione, reboot VPS
- [ ] VPS provisioning UI: selezione risorse, SSH key, OS, provisioning flow completo
- [ ] VPS attivo in < 5 min dal click
- [ ] Basic monitoring dashboard: uptime, CPU, RAM, disk
- [ ] GitHub OAuth + deploy automation (one-click da repository)
- [ ] SSH key management (upload, generate, associate)
- [ ] pannello hosting base: lista VPS, stato, azioni rapide
- [ ] Integrazione sycren-db funzionante

### Fase 2 — Produzione

- [ ] File manager completo (upload, edit, delete, drag & drop)
- [ ] Database manager (MySQL/PostgreSQL, phpMyAdmin o equivalente)
- [ ] Backup automatici + restore one-click
- [ ] Grafici performance interattivi (CPU, RAM, disk, network)
- [ ] Sistema alert (email, Telegram) con soglie configurabili
- [ ] Access logs + diagnostica applicativa
- [ ] SSL management (Let's Encrypt auto + custom upload)
- [ ] Piani di servizio con billing (Starter, Business, Enterprise)

### Fase 3 — Scale

- [ ] Auto-scaling orizzontale/verticale
- [ ] Storage oggetti S3-compatibile
- [ ] Multi-region support (Nuremberg, Helsinki, Falkenstein)
- [ ] Integrazione completa sycren-app SSO + billing
- [ ] Admin panel per resource management
- [ ] Status page pubblica
- [ ] API pubblica per clienti (gestione VPS programmatica)

### Qualità e Sicurezza

- [ ] Tutte le API Hetzner chiamate con retry e rate limiting
- [ ] Secrets e token in variabili d'ambiente, mai in codice
- [ ] Rate limiting sulle route API pubbliche
- [ ] Validazione input con Zod su tutte le route
- [ ] Logging strutturato (pino/winston)
- [ ] Tests: unit (Jest/Vitest), integration (Supertest)
- [ ] Tipo strictly su Prisma queries
- [ ] Nessun segreto in repo

---

## 10. Limiti e Vincoli

- **Età founder:** 🕐 17 anni — nessuna partita IVA al momento dello sviluppo. Cloud viene costruito in Fase 0, venduto in Fase 2 (18+)
- **Budget:** $0 Capex — solo strumenti gratuiti (Vercel, Supabase, Resend, Cloudflare)
- **Spesa ricorrente minima:** €4/mese per VPS Hetzner di test
- **Provider singolo:** inizialmente solo Hetzner; DigitalOcean come fallback futuro
- **No Kubernetes in Fase 1:** deployment su VPS singolo con Docker Compose

---

*Sycren Cloud — PRD v1.0 — Giugno 2026*
*Documento interno. Non distribuire senza autorizzazione scritta.*
