# sycren-domains — Domain Registration & DNS Management: Project Requirements
> PRD v1.0 — Giugno 2026 — Uso interno — Allineato alla Bootstrap Roadmap v1.3

---

## 1. Contesto e Obiettivo

Sycren Domains è la divisione di gestione domini e DNS della piattaforma Sycren, accessibile su `domains.sycren.com`. Costituisce il modulo #6 nell'ordine di costruzione della piattaforma (dopo Landing → sycren-db → Studio → App → Cloud), con priorità bassa nella fase di lancio ma alto valore strategico nel lungo termine.

**Missione della divisione:** Offrire agli utenti Sycren un registrar di domini integrato con un pannello DNS professionale, eliminando la necessità di utilizzare provider esterni separati (Namecheap, GoDaddy, OVH, ecc.) per la gestione dei domini.

**Strategia tecnica:** Wrapper UI su Cloudflare API. Sycren Domains non implementa un registrar diretto — utilizza Cloudflare Registrar come backend (accreditato ICANN) e astrae la complessità API dietro un'interfaccia utente moderna e integrata con il resto della piattaforma Sycren.

**Obiettivo primario:** Permettere agli utenti di cercare, registrare, rinnovare e trasferire domini, e gestire configurazioni DNS complete, tutto da un'unica piattaforma integrata con Sycren Cloud e Sycren Security.

**Obiettivo secondario:** Creare un lock-in positivo per l'ecosistema Sycren — un cliente che ha domini su Sycren sarà naturalmente portato a usare Sycren Cloud per l'hosting e Sycren Security per la protezione DNS.

**Stato attuale (Giugno 2026):**
- [x] Organizzazione GitHub `sycren` creata — repository `sycren-domains` esistente
- [x] Dominio `sycren.com` registrato su Cloudflare
- [ ] Nessun codice sviluppato per `domains.sycren.com`
- [ ] In attesa del completamento di `sycren-db`, `studio.sycren.com`, `app.sycren.com`, `cloud.sycren.com`

**Posizionamento nella Roadmap:**

| # | Modulo | Età 🕐 | Priorità |
|---|---|---|---|
| 1 | Landing Page `sycren.com` | 16 | Alta |
| 2 | Database Foundation `sycren-db` | 16 | Alta |
| 3 | Client Portal `studio.sycren.com` | 16 | Alta |
| 4 | Hub Centrale / SSO `app.sycren.com` | 16-17 | Alta |
| 5 | Cloud Lite `cloud.sycren.com` | 17 | Media |
| **6** | **Domains `domains.sycren.com`** | **17-18** | **Bassa** |
| 7 | Security `security.sycren.com` | 18+ | Bassa |
| 🎂 | Lancio Commerciale | 18 | — |

---

## 2. Utenti Target

| Segmento | Caratteristiche | Bisogno Principale |
|---|---|---|
| Clienti Sycren Cloud esistenti | Hanno VPS/hosting su Sycren Cloud, vogliono gestire domini e DNS insieme | Integrazione unificata dominio → hosting |
| Clienti Sycren Studios | Hanno un sito web sviluppato da Sycren, necessitano di dominio associato | Registrazione DNS facile senza provider terzi |
| Sviluppatori e team tecnici | Gestiscono multipli domini e record DNS complessi | Pannello DNS avanzato, API, automazione |
| PMI e startup | Vogliono un unico partner per dominio + hosting + sicurezza | Semplicità, integrazione, supporto unificato |
| Rivenditori / agenzie | Gestiscono domini per conto di clienti finali | Multi-tenancy, fatturazione unificata |

**Lingua della piattaforma: Inglese** (coerente con il resto dell'ecosistema Sycren). Localizzazioni future da considerare in roadmap.

---

## 3. Requisiti Funzionali — Dettaglio Feature

### 3.1 Domain Search

Il motore di ricerca domini è il punto di ingresso principale della piattaforma.

- Barra di ricerca prominente nella homepage di `domains.sycren.com`
- Verifica disponibilità in tempo reale via Cloudflare API
- Supporto TLD estesi: `.com`, `.it`, `.eu`, `.io`, `.dev`, `.app`, `.net`, `.org`, `.co`, `.uk`, `.de`, `.fr`, `.tech`, `.online`, `.store`, `.cloud`, `.me`, `.info`, `.biz`
- Visualizzazione prezzo per TLD (prezzo Cloudflare + markup Sycren configurabile)
- Suggerimenti alternativi se il dominio richiesto non è disponibile:
  - "Did you mean...?" correzione ortografica
  - TLD alternativi suggeriti (es. `.io` se `.com` occupato)
  - Varianti con prefissi/suffissi comuni (es. `get-`, `the`, `-app`)
- Risultati con badge: `Available ✓` / `Registered ✗` / `Premium` / `Reserved`
- Filtri per TLD, fascia di prezzo, disponibilità
- Storico ricerche recenti per utente autenticato (salvato su sycren-db)

### 3.2 Domain Registration

Flusso di registrazione guidato con checkout integrato.

- Selezione durata registrazione: 1, 2, 3, 5, 10 anni
- Configurazione WHOIS: inserimento contatti registrante, amministrativo, tecnico
- Opzione WHOIS privacy (inclusa o a pagamento, basato su policy Cloudflare)
- DNS suggerito automatico: punti a Cloudflare NS di default
- Riepilogo ordine con breakdown costi: dominio + privacy + markup
- Checkout tramite `app.sycren.com` (sistema di pagamento centralizzato)
- Creazione automatica zona DNS su Cloudflare post-registrazione
- Email di conferma registrazione con dettagli tecnici (nameserver, pannello login)
- Aggiunta automatica dominio alla lista domini dell'utente in `app.sycren.com`
- Tempo di attivazione target: < 5 minuti dalla conferma pagamento

### 3.3 DNS Panel — Record Management

Pannello DNS completo per la gestione dei record di zona.

#### Record Supportati

| Tipo | Descrizione | Esempio d'Uso |
|---|---|---|
| **A** | IPv4 address record | `@ → 185.199.108.153` |
| **AAAA** | IPv6 address record | `@ → 2001:db8::1` |
| **CNAME** | Canonical name (alias) | `www → sycren.com` |
| **MX** | Mail exchange | `@ → mail.sycren.com (priority 10)` |
| **TXT** | Text record | `@ → v=spf1 include:_spf.google.com ~all` |
| **NS** | Nameserver record | `subdomain → ns1.cloudflare.com` |
| **SRV** | Service record | `_sip._tcp → target:5060 priority 10 weight 5` |

#### Funzionalità del Pannello

- Tabella record con ordinamento per tipo, nome, TTL
- Aggiunta record con form dinamico (campi cambiano in base al tipo selezionato)
- Modifica inline dei record (click-to-edit su valore)
- Eliminazione con conferma (undo entro 5 secondi su Cloudflare API)
- Filtri per tipo record (mostra solo A / solo MX, ecc.)
- Ricerca testuale tra tutti i record
- Esportazione zona DNS in formato BIND
- Template DNS predefiniti:
  - "Sycren Cloud" — punta A/AAAA al VPS Sycren Cloud
  - "Google Workspace" — MX Google, TXT SPF/DKIM/DMARC
  - "Vercel Deploy" — CNAME a cname.vercel-dns.com
  - "GitHub Pages" — A a GitHub Pages IP, CNAME www
  - "Custom" — template salvato dall'utente
- TTL personalizzabile per record: Auto, 120, 300, 600, 900, 1800, 3600, 7200, 18000, 43200, 86400
- Proxy status (Cloudflare proxy): DNS only / Proxied (arancione/nuvola)
- Supporto record multipli per stesso nome/tipo (es. più record A per round-robin)

### 3.4 Domain Transfers

Gestione trasferimenti domini in entrata e in uscita.

#### Trasferimento in Ingresso (Transfer In)

- Form di avvio trasferimento con inserimento nome dominio
- Richiesta codice EPP (Auth Code) al registrar uscente — guida interattiva su come ottenerlo
- Verifica stato dominio: periodo redemption, stato locked, etc.
- Inserimento codice EPP per autorizzazione trasferimento
- Pagamento per estensione di 1 anno (obbligatorio ICANN)
- Monitoraggio stato trasferimento: `Pending`, `Waiting approval`, `Completed`, `Failed`
- Timeline visiva del trasferimento con step chiari
- Email notifica a ogni cambio di stato
- Rollback automatico in caso di fallimento (rimborso)

#### Trasferimento in Uscita (Transfer Out)

- Generazione codice EPP dalla dashboard
- Sblocco dominio per trasferimento (toggle domain lock)
- Verifica WHOIS per approvazione trasferimento
- Notifica email al registrante quando il trasferimento viene avviato dal nuovo registrar
- Storico trasferimenti completati con date e registrar

### 3.5 WHOIS Privacy Protection

- Attivabile/disattivabile per dominio dalla dashboard
- Sostituisce dati registrante con informazioni proxy Cloudflare
- Compatibile GDPR (dati registrante EU automaticamente protetti)
- Badge visibile nella lista domini: `WHOIS Privacy: ON ✓` / `OFF`
- Nessun costo aggiuntivo (se incluso nel pricing Sycren Domains)
- Applicabile anche a domini trasferiti su Cloudflare

### 3.6 Domain Renewals

- Rinnovo manuale dalla dashboard (1, 2, 3, 5, 10 anni)
- Rinnovo automatico attivo di default (configurabile per dominio)
- Prezzo di rinnovo garantito (senza aumenti improvvisi)

#### Sistema di Notifiche Pre-Scadenza

| Scadenza | Tipo Notifica | Canale | Messaggio |
|---|---|---|---|
| 90 giorni | Primo avviso | Email + App | "Your domain expires in 90 days" |
| 30 giorni | Avviso urgente | Email + App + Push | "Your domain expires in 30 days — renew now" |
| 7 giorni | Ultimo avviso | Email + App + Push + SMS (opz.) | "Final notice: your domain expires in 7 days" |
| 0 giorni | Scaduto — Grace period | Email urgente | "Your domain has expired — 30-day grace period active" |
| -30 giorni | Redemption period | Email critica | "Your domain is in redemption — recovery fee applies" |

- Le notifiche sono gestite centralmente da `app.sycren.com` (sistema notifiche unificato)
- Stato dominio visibile nella lista: `Active`, `Expiring soon`, `Expired (grace)`, `Redemption`, `Pending delete`
- Rinnovo automatico tenta il pagamento salvato; se fallisce, notifica e riprova dopo 48h
- Storico rinnovi: data, importo, metodo pagamento per ogni transazione

### 3.7 DNS Propagation Checker

- Tool integrato che verifica la propagazione DNS globale per un dominio/record
- Check da multiple località geografiche (USA, Europa, Asia, Oceania)
- Visualizzazione per tipo record: quali DNS server rispondono con quale valore
- Mappa mondiale con punti verdi (propagato) / rossi (non propagato) / gialli (parziale)
- Tempo stimato per completamento propagazione
- Storico controlli per dominio
- API interna per controllo automatico post-modifica DNS
- Target tecnico: propagazione completata < 15 minuti su Cloudflare edge

### 3.8 URL Redirect & URL Masking

- Redirect 301 (permanente) / 302 (temporaneo) da un dominio/sottodominio a un URL di destinazione
- Configurazione tramite interfaccia dedicata (non richiede modifica manuale DNS)
- Opzioni di redirect:
  - `domain.com → www.domain.com` (canonical redirect predefinito)
  - `old-domain.com → new-domain.com` (migrazione dominio)
  - `sub.domain.com → external-url.com/path` (redirect selettivo)
- URL Masking: mostra contenuto di un URL esterno mantenendo il dominio originale nel browser (iframe)
- Avvertenza SEO per masking: `⚠ May impact search rankings. Consider 301 redirect instead.`
- Redirect rules gestite via Cloudflare Bulk Redirects / Page Rules
- Storico redirect con contatore visite (opzionale, via analytics)

---

## 4. Integrazioni Cross-Platform

### 4.1 sycren-cloud (`cloud.sycren.com`)

| Integrazione | Descrizione |
|---|---|
| **Domain pointing** | Pulsante "Point to Cloud" nella dashboard domini → configura automaticamente A/AAAA record verso IP VPS Sycren Cloud |
| **One-click deploy** | Dopo aver puntato il dominio, opzione "Deploy app here" che apre la pipeline di deploy cloud |
| **SSL auto** | Certificato SSL automatico via Cloudflare per domini puntati a Cloud |
| **Stato integrazione** | Notifica in dashboard: "This domain is linked to your Cloud VPS" con link a cloud.sycren.com |

### 4.2 sycren-security (`security.sycren.com`)

| Integrazione | Descrizione |
|---|---|
| **DDoS activation** | Pulsante "Enable DDoS protection" nella dashboard domini → attiva protezione via Sycren Security |
| **WAF rules** | Applicazione automatica regole WAF di base per domini registrati su Sycren |
| **Security status** | Badge nella lista domini: `Protected ✓` / `Unprotected ⚠` |
| **Alert integration** | Eventi di sicurezza sul dominio (DDoS, brute force) notificati via app.sycren.com |

### 4.3 sycren-app (`app.sycren.com`)

| Integrazione | Descrizione |
|---|---|
| **SSO** | Accesso a `domains.sycren.com` tramite login unificato `app.sycren.com` |
| **Dashboard widget** | Widget "My Domains" nella dashboard principale: numero domini, stato, scadenze imminenti |
| **Notifiche** | Tutte le notifiche (scadenze, trasferimenti, modifiche DNS) passano tramite il sistema notifiche centralizzato |
| **Billing** | Acquisti domini fatturati attraverso il sistema di pagamento centralizzato di `app.sycren.com` |
| **API** | Le API interne di Domains sono accessibili tramite API gateway unificato |

### 4.4 sycren-db

- Schema Prisma centralizzato in `sycren-db` esteso con modelli per: `Domain`, `DnsRecord`, `Transfer`, `Whois`
- Migrazioni database gestite centralmente
- Tipi TypeScript generati da Prisma per type safety end-to-end
- Connessione database condivisa (Supabase Fase 0, PostgreSQL in produzione)

---

## 5. Stack Tecnologico

| Layer | Tecnologia | Note |
|---|---|---|
| Framework | Next.js 14+ (App Router) | Server Components, API Routes |
| Language | TypeScript 5+ (strict) | Type safety su tutto il codebase |
| Styling | Tailwind CSS 3.4+ | Design token condivisi da `sycren-design-system` |
| UI Components | shadcn/ui + Lucide React | Componenti riutilizzabili, icone consistenti |
| ORM | Prisma ORM | Via `sycren-db`, schema centralizzato |
| Database | PostgreSQL (Supabase / Neon) | Provider gratuito in Fase 0 |
| DNS Backend | Cloudflare API | `@cloudflare/cloudflare-api` SDK |
| Auth | NextAuth.js / Lucia Auth | SSO integrato con `app.sycren.com` |
| Form & Validazione | React Hook Form + Zod | Validazione forms condivisa |
| Notifiche | Sistema centralizzato `app.sycren.com` | Webhook / API per notifiche cross-platform |
| Search | Fuse.js (lato client) | Per ricerca domini e filtri locali |

### Variabili d'Ambiente

```bash
# Cloudflare API
CLOUDFLARE_API_TOKEN=xxxxxxxx
CLOUDFLARE_ACCOUNT_ID=xxxxxxxx
CLOUDFLARE_ZONE_ID=xxxxxxxx

# Database (via sycren-db)
DATABASE_URL=postgresql://...

# Integrazioni
NEXT_PUBLIC_APP_URL=https://app.sycren.com
NEXT_PUBLIC_CLOUD_URL=https://cloud.sycren.com
NEXT_PUBLIC_SECURITY_URL=https://security.sycren.com

# App SSO
NEXTAUTH_URL=https://domains.sycren.com
NEXTAUTH_SECRET=xxxxx

# Pricing
DOMAIN_MARKUP_PERCENTAGE=15
```

---

## 6. Requisiti di Design

### Tema

Coerente con il design system Sycren (temi Dark / Light). Colore badge divisione Domains: **Verde** (`#34D399` dark, `#059669` light).

### Pagine

| Pagina | Route | Descrizione |
|---|---|---|
| Home / Search | `/` | Barra ricerca dominio principale, TLD popolari, statistiche |
| Search Results | `/search?q=example` | Lista risultati disponibilità con prezzi e azioni |
| Registration | `/register/{domain}` | Form registrazione multi-step |
| Dashboard | `/dashboard` | Lista domini dell'utente con stato, scadenze, azioni rapide |
| Domain Detail | `/domains/{id}` | Dettaglio dominio: info WHOIS, status, rinnovo, trasferimento |
| DNS Panel | `/domains/{id}/dns` | Tabella record DNS con CRUD |
| DNS Templates | `/domains/{id}/dns/templates` | Gestione template DNS predefiniti |
| Transfers | `/transfers` | Lista trasferimenti in corso e storico |
| New Transfer | `/transfers/new` | Form avvio trasferimento in ingresso |
| Propagation | `/tools/propagation` | DNS propagation checker |
| Redirects | `/tools/redirects` | URL redirect management |
| Settings | `/settings` | Preferenze notifiche, default TLD, auto-renew |

---

## 7. KPI Targets

| Metrica | Target | Metodo di Misura |
|---|---|---|
| **Propagazione DNS** | < 15 minuti su Cloudflare edge | Monitoraggio interno / Cloudflare API |
| **Propagazione DNS globale** | < 48 ore | Propagation checker multi-region |
| **Disponibilità pannello DNS** | 99.95% | Uptime monitoring (BetterUptime / Checkly) |
| **Notifiche pre-scadenza** | Automatica a 90 / 30 / 7 giorni | Verifica schedulazione CRON + log |
| **Tempo registrazione dominio** | < 5 minuti dal pagamento | End-to-end timing |
| **Tempo risposta API DNS** | < 200ms (p95) | APM tool |
| **Soddisfazione utente** | > 4.0 / 5 | NPS survey in-app (Fase 2) |
| **Tasso rinnovo automatico** | > 80% | Metriche billing |

---

## 8. Criteri di Completamento

- [ ] Domain search funzionante con verifica disponibilità via Cloudflare API
- [ ] Flusso registrazione dominio completo con checkout
- [ ] Pannello DNS con gestione record A, AAAA, CNAME, MX, TXT, NS, SRV
- [ ] Templates DNS predefiniti (almeno 3)
- [ ] Trasferimento domini in ingresso con codice EPP
- [ ] Trasferimento domini in uscita con generazione EPP
- [ ] WHOIS privacy attivabile/disattivabile
- [ ] Rinnovo manuale e automatico
- [ ] Sistema notifiche pre-scadenza a 90/30/7 giorni
- [ ] DNS propagation checker multi-region
- [ ] URL redirect (301/302) e URL masking
- [ ] Integrazione SSO con `app.sycren.com`
- [ ] Widget dashboard "My Domains" su `app.sycren.com`
- [ ] Integrazione "Point to Cloud" con `cloud.sycren.com`
- [ ] Integrazione "DDoS protection" con `security.sycren.com`
- [ ] Tema Dark / Light coerente con design system Sycren
- [ ] Responsive design (mobile: 375px+)
- [ ] Deploy su `domains.sycren.com` con SSL
- [ ] Nessun segreto in repo

---

## 9. Vincoli e Note

- **Età di costruzione:** 🕐 17-18 anni. Non è una priorità immediata — tutti gli sforzi vanno prima su sycren-db, sycren-studio, sycren-app, e cloud.sycren.com
- **Budget:** $0 (Cloudflare è gratuito per DNS; i costi di registrazione domini sono coperti dal cliente con markup Sycren)
- **Cloudflare API rate limits:** Gestire con caching e retry logic
- **Dipendenze esterne:** Cloudflare Registrar potrebbe non essere disponibile per tutti i TLD — prevedere fallback per TLD non supportati
- **DPA (Data Processing Agreement):** Cloudflare è compliant GDPR, ma verificare requisiti per dati WHOIS

---

## Related Notes

| Document | Description |
|---|---|
| [[domains.README]] | sycren-domains overview |
| [[domains.todo]] | Implementation plan |
| [[db.requirements\|sycren-db requirements]] | Shared schema for User, Domain, DnsRecord |
| [[app.requirements\|sycren-app requirements]] | SSO and notifications integration |
| [[design-system.requirements\|Design System requirements]] | UI components consumed |
| [[cloud.requirements\|sycren-cloud requirements]] | Domain-to-cloud integration |
| [[security.requirements\|sycren-security requirements]] | DNS-level DDoS protection |
| [[infra.requirements\|sycren-infra requirements]] | Docker and deployment config |
| [[foundation/ROADMAP\|Bootstrap Roadmap]] | Build order — module #6 |

---

*Sycren Domains — PRD v1.0 — Giugno 2026*
*Allineato a Bootstrap Roadmap v1.3 e Documento di Fondazione v1.0*
*Documento interno. Non distribuire senza autorizzazione scritta.*
