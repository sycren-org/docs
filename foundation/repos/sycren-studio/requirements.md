# Sycren Studios — Client Portal: Project Requirements
> PRD v1.0 — Giugno 2026 — Uso interno — Allineato alla Bootstrap Roadmap v1.3

---

## 1. Contesto e Obiettivo

Sycren Studios è la **divisione agency** di Sycren — il braccio operativo che si occupa di sviluppo software custom, consulenza tecnica e progettazione digitale. Mentre la landing page (`sycren.com`) è il primo punto di contatto pubblico del brand, `studio.sycren.com` è il **cuore commerciale** dell'intera piattaforma: il portale attraverso cui i clienti interagiscono con Sycren per tutto il ciclo di vita del progetto.

**Posizionamento strategico:** Studios è la prima divisione a generare ricavi per Sycren. A differenza di Cloud, Domains e Security (modelli a ricorrenza / reselling), Studios opera su progetti one-shot e retainer mensili. È il traino iniziale dell'intero ecosistema.

**Obiettivo primario della piattaforma:** Fornire un portale clienti completo che gestisca l'intero flusso — dalla richiesta di preventivo alla consegna finale, passando per onboarding, comunicazione, condivisione file e monitoraggio avanzamento. Il cliente deve poter vedere lo stato del progetto in tempo reale, senza dover scrivere email o chiamare.

**Obiettivo secondario:** Costruire un vantaggio competitivo rispetto a freelance e agenzie tradizionali. Sycren Studios si differenzia per trasparenza totale (prezzi pubblici su `sycren.com`), comunicazione strutturata (chat dedicata, notifiche email) e un'esperienza utente paragonabile a quella di un SaaS moderno.

**Strategia bootstrap (Soluzione B):** Entrambi i founder hanno 16 anni 🕐 e nessuna partita IVA. Il principio guida:

> **Costruisci tutto ora. Vendi al momento giusto (18 anni 🎂). La struttura segue i ricavi, non li precede.**

**Stato attuale (Giugno 2026):**
- [x] Organizzazione GitHub `sycren` creata — repository `sycren-studio` esistente
- [x] `sycren.com` registrato e DNS su Cloudflare
- [ ] `sycren-db` schema Prisma in sviluppo (prerequisito per sycren-studio)
- [ ] `studio.sycren.com` da sviluppare (next-in-line dopo landing e sycren-db)
- [ ] Landing page in sviluppo (priorità #1)
- [ ] Raccolta contatti waitlist attiva su `sycren.com`

**Dipendenza da sycren-db:** `sycren-studio` dipende dallo schema Prisma centralizzato in `sycren-db` per: utenti, progetti, fatture, messaggi, file, milestone, contratti. sycren-db deve essere completato prima di iniziare lo sviluppo di sycren-studio.

---

## 2. Utenti Target

| Segmento | Caratteristiche | Bisogno Principale |
|---|---|---|
| **Startup early-stage** | Budget limitato (€500–5.000), alta urgenza, founder tecnici o non tecnici | MVP funzionante in 2–6 settimane, prezzo trasparente, comunicazione diretta |
| **PMI in digitalizzazione** | Necessitano di sito professionale o applicazione gestionale. Valutano affidabilità e supporto continuativo | Partner tecnico di fiducia, manutenzione post-consegna, reportistica avanzamento |
| **Agenzie e studi professionali** | Cercano un partner tecnico per progetti dei propri clienti (white-label potenziale) | Qualità del codice, rispetto tempistiche, comunicazione professionale |
| **Professionisti e creator** | Landing page, portfolio, e-commerce leggero. Budget ridotto ma richieste rapide | Velocità di esecuzione, prezzi fissi, nessuna complicazione burocratica |
| **Aziende con sistema legacy** | Necessitano di audit, refactoring o integrazione API tra sistemi esistenti | Consulenza tecnica, documentazione, architettura moderna |

**Mercato geografico:** Internazionale (Europa, UK, USA, Middle East). Comunicazione in inglese come default. Pricing in EUR.

---

## 3. Requisiti Funzionali — Dettaglio Feature

La piattaforma si articola in 6 aree funzionali principali, ciascuna con requisiti specifici.

---

### 3.1 Richiesta Preventivo (Quote Request)

Il punto di ingresso principale per un nuovo potenziale cliente.

**Requisiti:**
- Form strutturato con i seguenti campi:
  - Nome e cognome / Ragione sociale (obbligatorio)
  - Email (obbligatorio, validazione formato)
  - Azienda (opzionale)
  - Tipologia progetto (dropdown): Landing Page, Web Application, Mobile App, API Integration, E-commerce, Consulting, Maintenance, Other
  - Budget indicativo (range selezionabile: < €500, €500–1.500, €1.500–3.000, €3.000–5.000, €5.000–10.000, €10.000+)
  - Scadenza desiderata (date picker o range "entro 1 mese / 1-3 mesi / 3-6 mesi / flessibile")
  - Descrizione progetto (textarea, obbligatoria, min 20 caratteri, max 2000)
  - Allegati (file upload, max 5 file, 10MB ciascuno, formati: PDF, DOC, DOCX, JPG, PNG, ZIP)
- Honeypot field nascosto per anti-spam
- Validazione lato client con React Hook Form + Zod
- POST verso API Route `/api/quotes`
- Email di conferma al richiedente con numero pratica
- Email di notifica ai founder (`angelo@sycren.com`, `cosimo@sycren.com`)
- Feedback visivo inline: spinner durante submit → messaggio di successo o errore
- Rate limiting: max 3 richieste per IP nelle ultime 24 ore

**Stati del preventivo:**
| Stato | Descrizione |
|---|---|
| `PENDING` | Ricevuto, in attesa di revisione |
| `REVIEWING` | In analisi da parte del team |
| `QUOTED` | Preventivo generato e inviato |
| `ACCEPTED` | Accettato dal cliente (con e-signature) |
| `DECLINED` | Rifiutato dal cliente |
| `EXPIRED` | Scaduto (30 giorni senza risposta) |

---

### 3.2 Onboarding Cliente (Client Onboarding)

Dopo l'accettazione del preventivo, il cliente viene guidato attraverso un flusso di onboarding strutturato.

**Requisiti:**
- Wizard guidato a step con indicatore di progresso
- Step 1: **Profilo Aziendale** — Ragione sociale, partita IVA (opzionale), indirizzo, telefono, settore
- Step 2: **Requisiti Dettagliati** — Espansione della descrizione progetto, User Stories (se applicabile), wireframe/design reference (allegati)
- Step 3: **Timeline e Milestone** — Date proposte per milestone chiave, revisioni e consegna finale. Il sistema propone date baseline, il team e il cliente le accordano
- Step 4: **Contratto e Fatturazione** — Preview contratto digitale, metodi di pagamento, _50% upfront_ confermato
- Step 5: **Checklist Finale** — Riepilogo di tutto prima del via. Bottone "Conferma e parti"
- Ogni passaggio deve essere salvabile come bozza e riprendibile
- Email di benvenuto con link al progetto appena creato
- Notifica al team: "Nuovo progetto avviato: [nome progetto]"

---

### 3.3 Dashboard Progetto (Project Dashboard)

Il cuore dell'esperienza cliente. Ogni progetto ha una dashboard dedicata.

**Requisiti:**
- Header progetto: nome, stato, cliente, date chiave (inizio, consegna prevista, consegna effettiva)
- **Barra di avanzamento** globale: percentuale calcolata su milestone completate / totale milestone
- **Sezione Milestone:**
  - Elenco milestone con: nome, descrizione, data prevista, data completamento, stato (`PENDING`, `IN_PROGRESS`, `COMPLETED`, `DELAYED`)
  - Possibilità per il team di aggiornare lo stato (solo team, non cliente)
  - Cliente vede in sola lettura
- **Sezione Deliverable:**
  - Checklist dei deliverable previsti (dal contratto)
  - Ogni deliverable ha: nome, descrizione, link (quando disponibile), stato (`NOT_STARTED`, `IN_REVIEW`, `APPROVED`, `CHANGES_REQUESTED`)
  - Il cliente può approvare o richiedere modifiche
- **Timeline visiva:** Gantt semplificato o linea del tempo interattiva con milestone e deliverable
- **Changelog:**
  - Feed cronologico di tutte le attività sul progetto: milestone completate, file caricati, messaggi importanti, modifiche al perimetro
  - Visibile sia al cliente che al team
- **Stati progetto:**
  | Stato | Descrizione |
  |---|---|
  | `ONBOARDING` | In fase di raccolta requisiti |
  | `IN_PROGRESS` | In sviluppo attivo |
  | `ON_HOLD` | In attesa di feedback o risorse dal cliente |
  | `REVIEW` | In fase di revisione finale |
  | `COMPLETED` | Concluso, in attesa di feedback finale |
  | `ARCHIVED` | Archiviato, accessibile in sola lettura |

---

### 3.4 Condivisione File (File Sharing)

Area dedicata allo scambio di asset, documenti e risorse tra cliente e team.

**Requisiti:**
- Interfaccia a griglia/lista con cartelle e file
- Struttura di default per ogni progetto:
  ```
  📁 /briefs
  📁 /design (Figma export, mockup)
  📁 /technical (documentazione, architettura)
  📁 /deliverables (build finali, asset pronti)
  📁 /reference (materiale inviato dal cliente)
  ```
- Upload drag & drop con progress bar
- Supporto formati: PDF, DOC, DOCX, XLS, XLSX, PNG, JPG, SVG, FIG (Figma), ZIP, RAR
- Limite: 50MB per file (Fase 0), 200MB per file (Fase 2+)
- Versionamento: upload con stesso nome file → nuova versione. Storico versioni accessibile
- Preview inline per PDF, immagini e documenti di testo
- Download link generabile (con scadenza opzionale)
- Commenti per file (discussione contestuale)
- Notifica email: "Nuovo file caricato in [progetto] — [nome file]"
- Permessi: il cliente vede e carica solo nella propria area; il team ha accesso completo

---

### 3.5 Comunicazione Cliente (Client Communication)

Canale di comunicazione dedicato per ogni progetto.

**Requisiti:**
- **Chat integrata** (real-time via Supabase Realtime o WebSocket):
  - Canale dedicato per progetto (cliente + team assegnato)
  - Messaggi di testo, allegati, emoji
  - Indicatore "sta scrivendo"
  - Timestamp e separatore giornaliero
  - Storico completo ricercabile
- **Discussioni per milestone:**
  - Thread dedicato sotto ogni milestone per discussioni contestuali
  - Notifica al team quando il cliente commenta una milestone
- **Notifiche email:**
  - Nuovo messaggio in chat (riepilogo ogni 15 minuti se non letto)
  - Milestone completata o in ritardo
  - Deliverable pronto per revisione
  - Richiesta di feedback o decisione
- **Preferenze notifica:** Il cliente può scegliere frequenza email (immediata, giornaliera, settimanale, mai)
- **Template risposte rapide** per il team (risposte frequenti predefinite)

---

### 3.6 Storico Progetti (Project History)

Dopo la consegna, il progetto viene archiviato ma rimane accessibile.

**Requisiti:**
- Elenco progetti completati con filtro per stato, data, nome
- Vista dettaglio archivio: tutti i deliverable, documentazione finale, changelog completo
- **Documentazione finale:** PDF riepilogativo generato automaticamente con: descrizione, milestone, architettura, credenziali (se applicabile), istruzioni manutenzione
- **Storico fatture:** elenco fatture emesse per il progetto, stato pagamento, link download PDF
- **Contratti firmati:** copia digitale del contratto con firma, data, condizioni
- Possibilità di riattivare un progetto archiviato → nuovo preventivo per manutenzione/evolutive

---

## 4. Servizi Offerti

Sycren Studios eroga le seguenti categorie di servizi, ciascuna con tipologie di progetto e tempistiche indicative.

### 4.1 Sviluppo Web

| Tipologia | Esempi | Tempo Medio | Prezzo Indicativo |
|---|---|---|---|
| Landing Page | Sito one-page, portfolio, evento | 1–2 settimane | Da €500 |
| Sito Aziendale | Multi-pagina, CMS, blog | 2–4 settimane | Da €1.500 |
| E-commerce | Negozio online, catalogo, pagamenti | 4–8 settimane | Da €3.000 |
| Portale | Area riservata, dashboard, workflow | 6–12 settimane | Da €5.000 |

### 4.2 Sviluppo Applicazioni Web e Mobile

| Tipologia | Stack Tipico | Tempo Medio | Prezzo Indicativo |
|---|---|---|---|
| Web App MVP | Next.js + Supabase + Tailwind | 4–8 settimane | Da €2.500 |
| Dashboard/SaaS | React/Next.js + Node.js + PostgreSQL | 6–12 settimane | Da €5.000 |
| Mobile App (React Native) | React Native + Expo + Supabase | 8–16 settimane | Da €7.000 |
| PWA | Next.js + Service Worker + IndexedDB | 3–6 settimane | Da €3.000 |

### 4.3 Consulenza Tecnica e Audit

| Tipologia | Cosa Include | Durata | Prezzo |
|---|---|---|---|
| Code Review | Analisi codice esistente, vulnerabilità, best practice | 1 settimana | €800 |
| Architettura | Disegno architetturale, stack selection, roadmap tecnica | 1–2 settimane | €1.200 |
| Security Audit | Vulnerability scan, report, remediation plan | 1–2 settimane | €1.500 |
| Performance Audit | Lighthouse, load testing, ottimizzazione | 1 settimana | €600 |

### 4.4 Integrazioni API e Automazioni

| Tipologia | Esempi | Tempo Medio | Prezzo |
|---|---|---|---|
| Integrazione API | Stripe, Shopify, Salesforce, HubSpot, OpenAI | 1–4 settimane | Da €1.200 |
| Automazione Workflow | Zapier alternativo, webhook, pipeline dati | 2–4 settimane | Da €1.500 |
| Sistema di sincronizzazione | CRM ↔ DB, ERP ↔ E-commerce | 4–8 settimane | Da €3.000 |

### 4.5 Manutenzione e Supporto Continuativo

| Tipologia | Cosa Include | Prezzo Mensile |
|---|---|---|
| Basic | Aggiornamenti dipendenze, backup, monitoraggio uptime | €300/mese |
| Standard | Basic + 4h di sviluppo evolutivo, supporto email | €600/mese |
| Premium | Standard + 12h di sviluppo, supporto prioritario, SLA 8h | €1.200/mese |

### 4.6 UI/UX Design

| Tipologia | Cosa Include | Tempo Medio | Prezzo |
|---|---|---|---|
| Wireframe | Struttura pagine, user flow, sitemap | 1 settimana | €400 |
| Mockup UI | Design Figma completo, design system, prototipo interattivo | 2–4 settimane | Da €1.000 |
| Design System | Componenti riutilizzabili, token design, documentazione | 2–3 settimane | Da €1.500 |
| UX Audit | Analisi usabilità, heatmap, report con raccomandazioni | 1 settimana | €600 |

---

## 5. Stack Tecnologico

### 5.1 Frontend

| Layer | Tecnologia | Versione |
|---|---|---|
| Framework | Next.js | 14+ (App Router) |
| Linguaggio | TypeScript | 5+ (strict mode) |
| Styling | Tailwind CSS | 3.4+ |
| State Management | React Context + Zustand (opzionale per stati complessi) | — |
| Form | React Hook Form | 7+ |
| Validazione | Zod | 3+ |
| Icons | Lucide React | — |
| Real-time | Supabase Realtime (per chat e notifiche) | — |

### 5.2 Backend & Database

| Layer | Tecnologia | Note |
|---|---|---|
| API Routes | Next.js API Routes / Next.js Server Actions | — |
| ORM | Prisma (via `sycren-db`) | Schema centralizzato, condiviso con tutte le app |
| Database | Supabase (PostgreSQL) | Gratuito Fase 0 (fino a 500MB) |
| Auth | Supabase Auth | SSO via `sycren-app` in Fase 2, auth diretto in Fase 0 |
| File Storage | Supabase Storage | Per file sharing allegati ai progetti |
| Real-time | Supabase Realtime | WebSocket per chat in tempo reale |

### 5.3 Infrastruttura

| Servizio | Ruolo | Costo Fase 0 |
|---|---|---|
| Vercel | Hosting e deploy | Gratuito |
| Cloudflare | DNS, CDN, WAF | Gratuito |
| Supabase | Database, Auth, Storage, Realtime | Gratuito |
| Resend | Email transazionali | Gratuito (3.000/mese) |
| GitHub | Source control, CI/CD | Gratuito |

### 5.4 Variabili d'Ambiente

```bash
# Database
DATABASE_URL=postgresql://...
DIRECT_URL=postgresql://...

# Auth
NEXT_PUBLIC_SUPABASE_URL=https://xxxxx.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=eyJxxxxx
SUPABASE_SERVICE_ROLE_KEY=eyJxxxxx

# Storage
NEXT_PUBLIC_SUPABASE_STORAGE_BUCKET=studio-files

# Email
RESEND_API_KEY=re_xxxxx

# App
NEXT_PUBLIC_SITE_URL=https://studio.sycren.com
FOUNDER_EMAIL_1=angelo@sycren.com
FOUNDER_EMAIL_2=cosimo@sycren.com

# Launch mode (ereditato da landing)
NEXT_PUBLIC_LAUNCH_MODE=WAITLIST   # WAITLIST (16-17) | LIVE (18+)
```

### 5.5 Schema Dati Principale (via sycren-db)

| Modello | Descrizione | Relazioni |
|---|---|---|
| **User** | Utente della piattaforma | `has_many` Project, Message, QuoteRequest |
| **QuoteRequest** | Richiesta di preventivo | `belongs_to` User, `has_many` File |
| **Project** | Progetto attivo o archiviato | `belongs_to` User, `has_many` Milestone, Deliverable, Message, File |
| **Milestone** | Tappa del progetto | `belongs_to` Project |
| **Deliverable** | Elemento consegnabile | `belongs_to` Milestone (opzionale), `belongs_to` Project |
| **Message** | Messaggio chat | `belongs_to` Project, `belongs_to` User |
| **File** | File caricato | `belongs_to` Project, `polymorphic` per contesto (quote/milestone/deliverable) |
| **Contract** | Contratto firmato | `belongs_to` Project |
| **Invoice** | Fattura | `belongs_to` Project |
| **ChangelogEntry** | Evento di changelog | `belongs_to` Project, `polymorphic` source |

---

## 6. KPI Target

### 6.1 Qualità del Servizio

| Metrica | Target | Come si Misura |
|---|---|---|
| Tempo medio risposta preventivo | < 24 ore lavorative | Dal submit alla prima risposta umana |
| Soddisfazione cliente post-progetto | > 4.5 / 5 | Survey al completamento del progetto |
| Progetti consegnati in tempo (sprint) | > 90% | Data consegna effettiva vs data concordata |
| Tempo medio risposta chat (orario lavorativo) | < 15 minuti | First response time su messaggi cliente |
| NPS (Net Promoter Score) | > 50 | Survey trimestrale ai clienti attivi |
| Tasso di conversione preventivo → progetto | > 40% | Preventivi accettati / preventivi emessi |

### 6.2 Performance e Affidabilità

| Metrica | Target |
|---|---|
| Uptime piattaforma (99° percentile) | > 99.5% |
| Lighthouse Performance (mobile) | ≥ 85 |
| LCP (Largest Contentful Paint) | < 2.5s |
| Tempo medio caricamento dashboard | < 2s |
| Tempo upload file (10MB) | < 5s (connessione media) |

### 6.3 Business (Fase 2, post-lancio commerciale)

| Metrica | Target (Mese 1–6) | Target (Mese 6–12) |
|---|---|---|
| Richieste preventivo / mese | 15–30 | 30–60 |
| Progetti attivi simultanei | 3–5 | 8–12 |
| Fatturato medio per progetto | €2.500 | €3.500 |
| Clienti retainer attivi | 2–4 | 6–10 |
| MRR da retainer | €600–1.200 | €1.800–3.600+ |

---

## 7. Criteri di Completamento

### 7.1 Criteri MVP (Fase 0 — 🕐 16 anni)

- [ ] Form richiesta preventivo funzionante con validazione, file upload, email di conferma e notifica founder
- [ ] Flow onboarding guidato (5 step) con salvataggio bozze
- [ ] Dashboard progetto con barra avanzamento, milestone e deliverable
- [ ] Area file sharing con upload, download, preview e versionamento base
- [ ] Chat in tempo reale per comunicazione cliente ↔ team
- [ ] Admin panel per gestione progetti, milestone e team
- [ ] Autenticazione Supabase funzionante (email + password, magic link)
- [ ] Connessione a `sycren-db` per schema utenti e progetti
- [ ] Deploy su Vercel con dominio `studio.sycren.com` e SSL
- [ ] Responsive design: funzionante su mobile (375px+), tablet e desktop
- [ ] Nessun segreto in repo, `.env.example` completo
- [ ] Lighthouse Performance ≥ 85 (mobile)

### 7.2 Criteri Fase 2 (Business Logic — 🕐 16–17 anni)

- [ ] Generazione automatica preventivi PDF con template professionale
- [ ] Accettazione preventivo con firma digitale (e-signature)
- [ ] Gestione milestone con calendario e notifiche scadenza
- [ ] Changelog automatico per ogni azione sul progetto
- [ ] Notifiche email complete (tutti i trigger: file, messaggio, milestone, deliverable)
- [ ] Stripe integration per pagamenti online
- [ ] Integrazione con `sycren-app` SSO per login unificato
- [ ] Storico progetti completo con archivio e documentazione finale

### 7.3 Criteri Fase 3 (Polish — 🕐 17 anni)

- [ ] Reportistica: statistiche progetto, tempo speso, fatturato per cliente
- [ ] Archiviazione automatica progetti completati dopo 30 giorni
- [ ] Documentazione finale PDF generata automaticamente per ogni progetto
- [ ] Preferenze notifica configurabili dal cliente
- [ ] Mobile responsiveness audit completo (tutte le schermate)
- [ ] Template email personalizzati con branding Sycren
- [ ] Integrazione con Stripe Billing per fatturazione ricorrente (retainer)
- [ ] Dashboard admin con overview: richieste in sospeso, progetti attivi, MRR

---

## Appendice: Flusso Cliente Tipico

```
1️⃣ Cliente arriva su sycren.com → CTA "Request a quote"
        │
2️⃣ Compila form preventivo → Riceve email conferma
        │
3️⃣ Team riceve notifica → Analizza requisiti
        │
4️⃣ Preventivo generato → Inviato al cliente (PDF + link)
        │
5️⃣ Cliente accetta → Firma digitale → 50% upfront via Stripe
        │
6️⃣ Onboarding: wizard 5 step → Requisiti dettagliati raccolti
        │
7️⃣ Progetto parte → Dashboard attiva → Milestone create
        │
8️⃣ Sviluppo → Cliente monitora in tempo reale
        │   ├── Chat per domande rapide
        │   ├── File sharing per asset e consegne
        │   └── Deliverable da revisionare e approvare
        │
9️⃣ Consegna finale → Feedback → Progetto archiviato
        │
🔟 Opzionale: Retainer mensile per manutenzione
```

---

## Appendice: Dipendenze Cross-Repository

| Dipendenza | Repository | Natura | Priorità |
|---|---|---|---|
| Schema DB condiviso | `sycren-db` | Bloccante | Deve essere completato PRIMA di sycren-studio |
| Modelli User e Auth | `sycren-db` → `sycren-studio` | Bloccante | Tabella User richiesta per login |
| SSO unificato | `sycren-app` | Futura (Fase 2) | In Fase 0, auth diretta Supabase. In Fase 2, reindirizzo a `app.sycren.com` |
| Notifiche email | `sycren-app` | Futura (Fase 2) | In Fase 0, notifiche via Resend diretto. In Fase 2, sistema unificato |
| Billing | `sycren-app` | Futura (Fase 2) | In Fase 0, fatturazione manuale / Stripe diretto |

---

*Sycren Studios — Client Portal PRD v1.0 — Giugno 2026*
*Documento interno. Non distribuire senza autorizzazione scritta.*
