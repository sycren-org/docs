# sycren-security — Project Requirements
> PRD v1.0 — Giugno 2026 — Uso interno — Allineato a Bootstrap Roadmap v1.3

---

## 1. Contesto e Obiettivo

Sycren Security è il layer di sicurezza gestita per le infrastrutture digitali dei clienti Sycren. Protegge domini, server e applicazioni attraverso Web Application Firewall (WAF), anti-DDoS, monitoraggio continuo 24/7 e analisi delle vulnerabilità.

**Posizionamento:** Add-on premium per clienti Cloud esistenti. Non è un prodotto standalone — viene acquistato da chi ha già infrastruttura su Sycren Cloud e vuole proteggerla. Modello operativo iniziale: reselling Cloudflare Pro con pannello custom.

**Strategia bootstrap (Soluzione B):** Sycren Security viene costruito durante la Fase 0 (16-17 anni) ma non viene reso disponibile ai clienti fino al lancio commerciale al 18° compleanno. Il principio guida:

> **Costruisci tutto ora. Vendi al momento giusto (18 anni 🎂). La struttura segue i ricavi, non li precede.**

**Obiettivo primario:** Fornire una piattaforma di sicurezza unificata che permetta ai clienti Cloud di attivare e configurare protezione WAF, anti-DDoS, monitoraggio e alerting in pochi click, senza dover gestire infrastrutture complesse.

**Obiettivo secondario:** Generare MRR ricorrente (€1.200/mese a regime) come add-on per i clienti Cloud esistenti, aumentando il lock-in della piattaforma Sycren e il valore medio per cliente.

**Stato attuale (Giugno 2026):**
- [x] Organizzazione GitHub `sycren` creata — repository `sycren-security` inizializzato
- [x] Posizionamento definito: ultimo modulo della piattaforma (ordine #7)
- [ ] Integrazione Cloudflare API da sviluppare
- [ ] Pannello security UI da costruire
- [ ] Integrazione con sycren-db da completare
- [ ] Integrazione SSO con sycren-app da implementare

**Dipendenza dalla Roadmap:**
| # | Modulo | Età 🕐 | Stato |
|---|---|---|---|
| 1 | Landing Page `sycren.com` | 16 | In sviluppo |
| 2 | Database Foundation `sycren-db` | 16 | Da iniziare |
| 3 | Client Portal `studio.sycren.com` | 16 | Da iniziare |
| 4 | Hub Centrale / SSO `app.sycren.com` | 16-17 | Da iniziare |
| 5 | Cloud Lite `cloud.sycren.com` | 17 | Da iniziare |
| 6 | Domains `domains.sycren.com` | 17-18 | Da iniziare |
| **7** | **Security `security.sycren.com`** | **18+** | **Da iniziare** |

---

## 2. Utenti Target

| Segmento | Caratteristiche | Bisogno di Sicurezza |
|---|---|---|
| Admin / CTO di PMI | Gestiscono infrastruttura IT, non sempre hanno competenze security dedicate | Protezione automatica, report mensili per compliance, alert gestiti |
| Security Team | Professionisti che vogliono configurare WAF, regole custom e analizzare log | Controllo granulare, personalizzazione delle policy, accesso ai log |
| Clienti Cloud esistenti | Già usano `cloud.sycren.com` per hosting VPS | Vogliono attivare protezione con un click senza cambiare provider |
| Founder Sycren (Angelo, Cosimo) | Devono monitorare la sicurezza dell'infrastruttura interna Sycren | Dashboard unificata, alert real-time, log centralizzati |

---

## 3. Requisiti Funzionali — Dettaglio Feature

### 3.1 Security Dashboard

**Descrizione:** Schermata principale unificata che mostra lo stato di sicurezza di tutta l'infrastruttura del cliente.

**Elementi richiesti:**
- Panoramica minacce: conteggio eventi critici, warning, info — ultime 24h / 7gg / 30gg
- Stato per servizio: per ogni server/dominio protetto, badge verde/giallo/rosso con ultimo evento
- Widget real-time: richieste al secondo bloccate dal WAF, traffico anomalo, tentativi di intrusione
- Mappa delle minacce (opzionale): geolocalizzazione IP bloccati
- Linea del tempo eventi: lista scrollabile degli ultimi eventi di sicurezza (timestamp, tipo, severità, azione)
- Filtri: per servizio, tipo evento, severità, data

**Comportamento:**
- Auto-refresh ogni 30 secondi (configurabile)
- Badge animato quando arrivano nuovi eventi critici
- Link diretto a ciascun servizio protetto (collegamento a `cloud.sycren.com` per i server, `domains.sycren.com` per i domini)

### 3.2 Web Application Firewall (WAF)

**Descrizione:** Filtraggio del traffico malevolo verso server e applicazioni, con regole preconfigurate e personalizzabili.

**Elementi richiesti:**

**3.2.1 WAF Base (Cloudflare API)**
- Attivazione/Disattivazione WAF per servizio con toggle
- Selezione profilo di sicurezza: `Low` (falsi positivi minimi), `Medium` (bilanciato), `High` (massima protezione)
- Regole preconfigurate OWASP Top 10: SQL injection, XSS, CSRF, path traversal, remote file inclusion
- Rate limiting: soglia richieste per IP (configurabile: 100/300/1000 req/min), durata ban (5/15/60 minuti)
- Whitelist/Blacklist IP: lista manuale con possibilità di import CSV
- Paesi bloccati: selezione da lista paesi (solo per servizi con traffico geograficamente limitato)
- Statistiche blocco: richieste totali, richieste bloccate, top 5 paesi/bloccati, top 5 IP bloccati

**3.2.2 WAF Avanzato (Regole Custom)**
- Editor regole visuale: condizione + azione (Block / Challenge / JS Challenge / Allow / Log)
- Campi condizione: IP, paese, User-Agent, URI path, query string, header, cookie, body
- Operatori: equals, contains, matches regex, starts with, ends with, IP range CIDR
- Test regola: simulazione su traffico recente prima di attivare
- Versioning regole: cronologia modifiche con rollback one-click
- Regole salvate come template riutilizzabili tra servizi

### 3.3 Anti-DDoS Protection

**Descrizione:** Mitigazione automatica di attacchi volumetrici (Layer 3/4) e applicativi (Layer 7).

**Elementi richiesti:**
- Attivazione protezione DDoS per servizio
- Soglie automatiche: traffico normale rilevato, mitigazione attivata automaticamente al superamento
- Modalità: `Automatic` (Cloudflare decide), `Manual` (utente attiva/manage), `Off`
- Statistiche attacchi: cronologia (data, durata, tipo, volume massimo Gbps/Mpps, azione intrapresa)
- Report post-attacco: sommario incidente, IP coinvolti, vettori d'attacco, tempo di mitigazione
- Configurazione avanzata: override sensibilità per servizio

### 3.4 Continuous Monitoring (24/7)

**Descrizione:** Monitoraggio continuo di uptime, anomalie di traffico e tentativi di intrusione.

**Elementi richiesti:**
- Uptime monitoring: check HTTP/HTTPS ogni 60 secondi, timeout 5 secondi
- Traffic anomaly detection: baseline automatica del traffico normale, alert su deviazioni > 3 sigma
- Intrusion attempt logging: registrazione tentativi di accesso non autorizzato, brute force, port scanning
- Monitoraggio certificati SSL: scadenza, validità, errori di configurazione
- Stato storico: grafico uptime 24h/7gg/30gg, eventi di downtime annotati

### 3.5 Centralized Logs

**Descrizione:** Repository centralizzato di tutti gli eventi di sicurezza: accessi, errori, blocchi WAF, tentativi di intrusione.

**Elementi richiesti:**
- Log viewer: tabella con timestamp, tipo, severità, servizio, IP, messaggio
- Ricerca full-text con supporto query: `ip:192.168.1.1`, `severity:critical`, `type:waf_block`
- Filtri: data range, tipo evento, severità, servizio, IP
- Export: CSV, JSON, PDF (selezione data range)
- Retention: 30 giorni per piano base, 90 giorni per piano premium (configurabile)
- Log streaming: webhook verso SIEM esterno (Splunk, ELK, Datadog) — premium

### 3.6 Real-time Alerts

**Descrizione:** Sistema di notifica immediata per eventi di sicurezza critici.

**Canali supportati:**
- **Email:** notifica a indirizzi configurati, template predefiniti per tipo evento
- **Telegram:** messaggio diretto o gruppo con formattazione (severità, servizio, timestamp, link al dettaglio)
- **Webhook:** POST JSON verso URL configurato (per integrazione con sistemi terzi)

**Configurazione alert:**
| Tipo Evento | Default | Canali Consigliati |
|---|---|---|
| Attacco DDoS in corso | ✅ Attivo | Telegram + Email |
| WAF blocco > 1000 richieste in 5 min | ✅ Attivo | Email |
| Tentativo brute force rilevato | ✅ Attivo | Telegram |
| Certificato SSL in scadenza (30gg) | ✅ Attivo | Email |
| Anomalia traffico rilevata | ⚠️ Disattivo | Webhook |
| Nuovo IP in whitelist aggiunto | ⚠️ Disattivo | Email |

- Soglie configurabili per tipo evento
- Cooldown: minimo 5 minuti tra notifiche consecutive dello stesso tipo
- Schedule: `Always` (default) o `Business hours only`
- Alert template personalizzabile (solo premium)

### 3.7 Vulnerability Assessment

**Descrizione:** Scansione periodica automatica delle superfici di attacco per identificare vulnerabilità note.

**Elementi richiesti:**
- Scan scheduling: settimanale, bisettimanale, mensile (configurabile per servizio)
- Tipi di scansione: port scan (top 1000 porte), CVE check (versioni software), misconfiguration scan (headers HTTP, SSL/TLS)
- Report: elenco vulnerabilità trovate (severità, CVE code, descrizione, remediation raccomandata)
- Remediation tracking: status per vulnerabilità (Open / In Progress / Resolved / Accepted Risk)
- Notifica su nuove vulnerabilità critiche trovate

### 3.8 Monthly Security Reports

**Descrizione:** Report di sicurezza mensile generato automaticamente con executive summary e raccomandazioni.

**Contenuto del report:**
- Executive summary per il management
- Statistiche minacce del mese: eventi totali, bloccati, critici
- Top 5 eventi di sicurezza
- Uptime servizi protetti
- Vulnerabilità trovate e risolte
- Raccomandazioni per il mese successivo
- Allegati tecnici: log aggregati, report vulnerabilità

**Formati:** PDF (download), HTML (anteprima browser), Email (riassunto inline con link al PDF)

### 3.9 SSL/TLS Management

**Descrizione:** Gestione avanzata della configurazione SSL/TLS per servizi protetti.

**Elementi richiesti:**
- Cipher suite configuration: selezione profilo (`Modern` — solo TLS 1.3, `Intermediate` — TLS 1.2 + 1.3, `Old` — backward compatible)
- HSTS: attivazione/disattivazione, max-age configurabile (6/12/24 mesi), includeSubDomains
- Certificate Transparency: monitoraggio log CT, alert su certificati emessi non autorizzati
- Certificate status: validità, emittente, scadenza, SANs
- Certificate auto-renew: Cloudflare Origin CA integrato, alert 30 giorni prima della scadenza

---

## 4. Premium Services (su Richiesta)

Services opzionali non inclusi nei piani base, attivabili singolarmente o come pacchetto premium.

| Servizio | Descrizione | Prezzo Indicativo | Tempo Consegna |
|---|---|---|---|
| **Penetration Testing (White-box)** | Test completo con accesso al codice sorgente e architettura. Report dettagliato con vulnerabilità, exploit proof-of-concept, remediation guideline. | €1.500–3.000 | 5–10 giorni lavorativi |
| **Penetration Testing (Black-box)** | Test senza accesso interno, simulazione attaccante esterno. Scansione, enumerazione, exploitation, post-exploitation. | €2.000–4.000 | 10–15 giorni lavorativi |
| **Incident Response** | Supporto immediato in caso di violazione o attacco in corso. Isolamento, analisi forense, remediation, recovery. | €500/ora (prima ora gratuita) | Attivazione < 2 ore |
| **GDPR Compliance Consulting** | Assessment conformità GDPR: data mapping, DPO assessment, gap analysis, remediation plan, documentation. | €2.000–5.000 | 2–4 settimane |
| **Security Audit** | Audit completo di applicazioni sviluppate da Sycren Studios: code review, dependency scan, config review, OWASP assessment. | €800–2.000 | 3–7 giorni lavorativi |

### Flusso Richiesta Premium
1. Cliente invia richiesta dal portale (`security.sycren.com/request-premium`)
2. Form: tipo servizio, descrizione esigenza, urgenza (Standard / Urgente)
3. Team Sycren Security valida e risponde entro 24 ore con preventivo e tempistiche
4. Accettazione preventivo → scheduling → esecuzione → consegna report
5. Follow-up call di debriefing con raccomandazioni

---

## 5. Stack Tecnologico

| Layer | Tecnologia | Note |
|---|---|---|
| Framework | Next.js 14+ (App Router) | Stessa base di tutta la piattaforma Sycren |
| Language | TypeScript 5+ (strict) | Tipizzazione forte su tutto il codebase |
| Styling | Tailwind CSS 3.4+ | Design system condiviso tramite sycren-design-system |
| Database ORM | Prisma (via sycren-db) | Schema centralizzato, nessun database dedicato |
| WAF / DDoS | Cloudflare API | Reselling Cloudflare Pro con pannello custom |
| Monitoring | Cloudflare Analytics API + liveness check custom | Log e metriche near real-time |
| Alerting | Resend (email), Telegram Bot API, webhook custom | Canali multipli configurabili |
| Reports | PDF generation (jsPDF / Puppeteer) | Report mensili automatici |
| SSL | Cloudflare Origin CA API | Emissione e rinnovo automatico certificati |
| Charts / Viz | Recharts / Chart.js | Widget dashboard e report |
| Authentication | sycren-app SSO | Login centralizzato tramite app.sycren.com |
| Hosting | Vercel (frontend) + Cloudflare (edge) | Frontend su Vercel, protezione edge su Cloudflare |

### Variabili d'Ambiente

```bash
# Cloudflare API
CLOUDFLARE_API_TOKEN=xxxxx
CLOUDFLARE_ACCOUNT_ID=xxxxx
CLOUDFLARE_ZONE_IDS=zone1,zone2

# Alerting
RESEND_API_KEY=re_xxxxx
TELEGRAM_BOT_TOKEN=xxxxx
TELEGRAM_CHAT_ID=xxxxx

# Integrazione Sycren
NEXT_PUBLIC_SYCREEN_APP_URL=https://app.sycren.com
SYCREEN_DB_DATABASE_URL=postgresql://...
SYCREEN_SSO_SECRET=xxxxx

# Monitoring
MONITORING_CHECK_INTERVAL=60
ALERT_COOLDOWN_MINUTES=5
LOG_RETENTION_DAYS=30
```

---

## 6. KPI Targets

| KPI | Target | Metodo di Misura |
|---|---|---|
| Threat detection time | < 60 secondi | Tempo tra evento malevolo e alert inviato |
| DDoS mitigation time | < 3 minuti | Tempo tra rilevamento attacco e mitigazione attiva |
| Monitoring system availability | 99.99% | Uptime del sistema di monitoraggio (escluse manutenzioni programmate) |
| Alert delivery time | < 10 secondi | Tempo tra generazione alert e ricezione su Telegram/Email |
| WAF false positive rate | < 5% | Percentuale richieste legittime bloccate erroneamente |
| Monthly report generation | Entro 48h da fine mese | Report automatico generato e inviato nei primi 2 giorni del mese |
| Vulnerability scan coverage | 100% dei servizi attivi | Ogni servizio protetto deve avere scansione attiva |
| Log ingestion delay | < 5 secondi | Tempo tra evento e visibilità nei log centralizzati |
| SSL certificate auto-renewal | 100% (nessun certificato scaduto) | Verifica automatica giornaliera validità certificati |

---

## 7. Integrazioni con la Piattaforma Sycren

### 7.1 sycren-cloud (server protection)
- Quando un cliente attiva un VPS su `cloud.sycren.com`, può attivare la protezione Security con un click
- Dashboard Security mostra ogni server protetto con stato live
- Alert di sicurezza per un server sono visibili anche nella dashboard Cloud
- Dati di traffico e log vengono condivisi per l'analisi delle anomalie

### 7.2 sycren-domains (domain protection)
- Ogni dominio registrato su `domains.sycren.com` può avere WAF e DDoS attivati
- Gestione DNS integrata: le regole WAF si applicano a livello di zona DNS
- Certificati SSL gestiti automaticamente per ogni dominio

### 7.3 sycren-app (SSO e Hub Centrale)
- Autenticazione tramite SSO di `app.sycren.com` — un account per tutti i servizi
- Widget Security nella dashboard principale di app.sycren.com
- Notifiche unificate: alert di sicurezza compaiono nel feed notifiche di app.sycren.com
- Fatturazione: i piani Security compaiono nella sezione Billing di app.sycren.com

### 7.4 sycren-db (Database Foundation)
- Schema Prisma condiviso: tabelle `SecurityService`, `SecurityAlert`, `SecurityLog`, `SecurityReport`
- Tipi TypeScript generati da Prisma per type safety
- Utility DB condivise per query e migrazioni

### 7.5 sycren-studio (per Premium Services)
- Quando un cliente Studios richiede un penetration test o security audit, il flusso parte da `studio.sycren.com`
- Preventivi e contratti gestiti tramite il sistema di Studio

---

## 8. Criteri di Completamento

### MVP (18+ anni — al lancio commerciale)
- [x] Repository `sycren-security` inizializzato su GitHub
- [ ] Security Dashboard: panoramica minacce, stato servizi, eventi recenti
- [ ] WAF base: attivazione, profili preconfigurati, rate limiting, whitelist/blacklist IP
- [ ] Anti-DDoS: attivazione per servizio, soglie automatiche, statistiche attacchi
- [ ] Monitoring 24/7: uptime check, anomaly detection base, intrusion logging
- [ ] Alert system: email + Telegram + webhook, configurazione per tipo evento
- [ ] Centralized logs: viewer con ricerca, filtri, export CSV
- [ ] Integrazione Cloudflare API funzionante (WAF + DDoS + Analytics)
- [ ] Integrazione sycren-db: schema Prisma, migrazioni, tipi
- [ ] Integrazione sycren-app SSO: login centralizzato
- [ ] Integrazione sycren-cloud: protezione attivabile per VPS
- [ ] Integrazione sycren-domains: protezione attivabile per domini

### Fase 2 — Advanced (post-lancio, mese 3-6)
- [ ] Custom WAF rules editor visuale con test e versioning
- [ ] Controlli DDoS avanzati: modalità manuale, override sensibilità
- [ ] Vulnerability scanner automatico: scheduling, report, remediation tracking
- [ ] Monthly security report: PDF automatico con executive summary
- [ ] SSL/TLS management: cipher suite, HSTS, Certificate Transparency
- [ ] Traffic anomaly detection con baseline automatica

### Fase 3 — Premium (mese 6+)
- [ ] Penetration testing request flow: form, preventivo, scheduling, delivery report
- [ ] Incident response playbook: procedure documentate, escalation, template comunicazione
- [ ] GDPR compliance checklist e assessment tool
- [ ] Security audit tool: code review checklist, dependency scanner integration
- [ ] Log streaming verso SIEM esterni (Splunk, ELK, Datadog)
- [ ] Custom alert template per ogni cliente

### Qualità e Performance
- [ ] Dashboard LCP < 2.0s
- [ ] Log viewer con rendering virtualizzato per > 10.000 righe
- [ ] Dati dashboard refresh senza ricaricare pagina (SWR / polling)
- [ ] Tutti i test di integrazione Cloudflare API passano
- [ ] Alert delivery testato su tutti i canali
- [ ] Report PDF generato correttamente con dati reali
- [ ] Nessun segreto o API token in repository (tutto in ENV)
- [ ] Documentazione API interna per integrazioni cross-repo

---

## 9. Piani di Servizio e Pricing

| Piano | Prezzo | WAF | DDoS | Monitoring | Logs | Report | SSL | Vulnerability | Premium Access |
|---|---|---|---|---|---|---|---|---|---|
| **Security Basic** | €10/mese | ✅ Base | ✅ Auto | ✅ 24/7 | ✅ 7gg retention | ❌ | ✅ Base | ❌ | ❌ |
| **Security Pro** | €25/mese | ✅ Custom Rules | ✅ Manual Override | ✅ 24/7 | ✅ 30gg retention | ✅ Mensile | ✅ Avanzato | ✅ Settimanale | ❌ |
| **Security Enterprise** | €50/mese | ✅ Full Custom | ✅ Full Control | ✅ 24/7 + SLA | ✅ 90gg retention | ✅ Settimanale | ✅ Full | ✅ Giornaliero | ✅ Pen Test scontato |

**Sconti annuali:** 20% su piani Basic e Pro, 25% su Enterprise

---

## 10. Modello Operativo — Reselling Cloudflare

Sycren Security non costruisce infrastruttura propria nella fase iniziale. Il modello è:

1. **Reselling Cloudflare Pro:** il cliente paga Sycren, Sycren configura Cloudflare come proxy per il dominio/server del cliente
2. **Markup:** Cloudflare Pro = $20/mese → Sycren rivende a €25-50/mese (50-150% markup)
3. **Pannello custom:** l'interfaccia utente è su `security.sycren.com`, le chiamate API vanno a Cloudflare
4. **Nessun lock-in tecnico:** se un cliente vuole andare direttamente su Cloudflare, Sycren facilita la transizione

---

## Related Notes

| Document | Description |
|---|---|
| [[security.README]] | sycren-security overview |
| [[security.todo]] | Implementation plan |
| [[db.requirements\|sycren-db requirements]] | Shared schema for User, SecurityRule, ThreatLog |
| [[app.requirements\|sycren-app requirements]] | SSO and notifications integration |
| [[design-system.requirements\|Design System requirements]] | UI components consumed |
| [[cloud.requirements\|sycren-cloud requirements]] | Primary customer — protects cloud servers |
| [[domains.requirements\|sycren-domains requirements]] | DNS-level protection |
| [[infra.requirements\|sycren-infra requirements]] | Docker and deployment config |
| [[foundation/ROADMAP\|Bootstrap Roadmap]] | Build order — module #7 |

---

*Sycren Security — PRD v1.0 — Giugno 2026*
*Allineato a Bootstrap Roadmap v1.3. Documento interno. Non distribuire senza autorizzazione scritta.*
