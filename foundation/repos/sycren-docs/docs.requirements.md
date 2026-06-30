# sycren-docs — Project Requirements
> PRD v1.0 — Giugno 2026 — Uso interno — Allineato alla Bootstrap Roadmap v1.3

---

## 1. Contesto e Obiettivo

Sycren opera quattro divisioni (Studios, Cloud, Domains, Security) e necessita di una piattaforma documentale centralizzata, pubblica e ben strutturata. `sycren-docs` è il repository che alimenta `docs.sycren.com` — il punto di riferimento tecnico e informativo per sviluppatori, clienti e team interno.

**Strategia bootstrap (Soluzione B):** entrambi i founder hanno 16 anni 🕐 e nessuna partita IVA. Il periodo pre-commerciale (Fase 0, fino al 18° compleanno 🎂) viene dedicato a costruire ogni pilastro della piattaforma. I documenti vengono scritti prima che i servizi diventino commercialmente attivi, in modo da avere documentazione pronta al lancio.

**Missione della documentazione:** fornire informazioni chiare, tecnicamente accurate e sempre aggiornate su ogni servizio Sycren, riducendo al minimo il need di supporto diretto e accelerando l'onboarding di sviluppatori e clienti.

**Obiettivo primario:** costruire un repository di documentazione pubblico che copra API, guide clienti e processi interni — tutto su `docs.sycren.com`.

**Obiettivo secondario:** standardizzare il formato della documentazione in modo che ogni repository Sycren abbia documenti coerenti, versionati e facili da mantenere.

**Stato attuale (Giugno 2026):**
- [x] Organizzazione GitHub `sycren` creata
- [x] Dominio `sycren.com` registrato — DNS su Cloudflare
- [x] Repository `sycren-docs` inizializzato (pubblico)
- [ ] Framework documentale scelto e configurato
- [ ] Contenuti delle tre categorie scritti
- [ ] `docs.sycren.com` live e indicizzato

---

## 2. Target Users

| Segmento | Chi sono | Cosa cercano | Priorità |
|---|---|---|---|
| **Sviluppatori esterni** | Dev che integrano API Sycren o costruiscono su Sycren Cloud | Documentazione API chiara, esempi funzionanti, reference completa, SDK, webhook | Alta |
| **Clienti diretti** | Startup e PMI che usano Sycren Studios o il portale clienti | Guide utente passo-passo, FAQ billing, workflow project management | Alta |
| **Team interno** | Founder, contributor, futuri membri del team | Onboarding, code review guidelines, deployment procedures, development workflow | Media |
| **Partner / Open source** | Contributor esterni a repository Sycren pubblici | Contributing guide, coding standards, architecture docs | Bassa (Fase 0) |

**Lingua della documentazione:** Inglese. La documentazione API e le guide sviluppatore sono internazionali per natura. Guide clienti possono essere localizzate in futuro (Francese, Arabo, Italiano) ma non nella versione iniziale.

---

## 3. Content Categories

### 3.1 Developer API Documentation
Pensata per sviluppatori che integrano servizi Sycren. Scritta in tono tecnico, precisa, con esempi di codice funzionanti.

| Documento | Contenuto | Priorità |
|---|---|---|
| API Reference — sycren-app | Endpoint hub centrale (auth, utenti, progetti) | P0 |
| API Reference — sycren-studio | Endpoint portale clienti (tasks, files, milestones) | P0 |
| API Reference — sycren-cloud | Endpoint cloud (VPS, deploy, backup, monitoring) | P1 |
| API Reference — sycren-domains | Endpoint domini (search, register, DNS management) | P1 |
| API Reference — sycren-security | Endpoint sicurezza (WAF, DDoS, reports) | P2 |
| Authentication & SSO Guide | JWT, OAuth2, SSO flow, API keys | P0 |
| Webhook Documentation | Eventi, payload, retry policy, verifiche firma | P1 |
| SDK Reference | SDK per TypeScript/Python con esempi | P2 |
| Rate Limiting & Errors | Codici errore, rate limit headers, best practices | P1 |
| Changelog / Migration Guides | Breaking changes, versioni API, upgrade path | P1 |

### 3.2 Client Guides
Pensata per clienti non tecnici che usano Sycren Studios e il portale clienti. Tono chiaro, diretto, senza gergo tecnico.

| Documento | Contenuto | Priorità |
|---|---|---|
| Getting Started with Sycren Studios | Primi passi, creazione account, primo progetto | P0 |
| Client Portal User Guide | Panoramica portale, navigazione, funzionalità | P0 |
| Project Management Workflow | Milestones, task, files, comunicazioni | P1 |
| Billing & Invoicing FAQ | Prezzi, fatture, metodi pagamento, rimborsi | P0 |
| How to Request a Quote | Form, informazioni necessarie, tempi risposta | P1 |
| Maintenance Retainers Guide | Cosa include, come attivare, cancellazione | P2 |
| Troubleshooting & Support | Problemi comuni, contatti, tempi risposta | P1 |

### 3.3 Internal Documentation
Ad uso esclusivo del team Sycren. Repository privato o sezione protetta su docs.

| Documento | Contenuto | Priorità |
|---|---|---|
| Onboarding Guide | Setup ambiente, tooling, contatti, risorse | P0 |
| Development Workflow | Branch strategy, PR process, code review | P0 |
| Code Review Guidelines | Standard revisione, checklist, criteri accettazione | P1 |
| Deployment Procedures | Deploy su Vercel/Cloudflare, rollback, monitoring | P0 |
| Architecture Overview | Diagrammi, decisioni tecniche, stack decision log | P1 |
| Security Policies | Gestione secret, accessi, vulnerability reporting | P1 |
| Brand & Design Guidelines | Colori, loghi, typography, tone of voice | P2 |
| Runbook / Incident Response | Procedure emergenza, contatti escalation | P2 |

---

## 4. Architecture

### 4.1 URL e Subdomain

```
docs.sycren.com/
├── (public)
│   ├── api/              → API reference (sycren-app, studio, cloud, domains, security)
│   ├── guides/           → Client guides (getting-started, portal, billing)
│   ├── sdk/              → SDK documentation (typescript, python)
│   └── changelog/        → Versioni e migration guides
├── (internal)
│   └── internal/         → Onboarding, workflow, deployment (protetto)
└── (assets)
    └── _assets/          → Immagini, diagrammi, esempi codice
```

### 4.2 Framework Documentationale

- **Framework:** Next.js 14+ (App Router) + MDX
- **Tema:** Docs theme pre-built (es. Nextra, Mintlify, o Docusaurus con Next.js wrapper)
- **Stile:** Coerente con brand Sycren (dark/light, indigo accent)
- **Search:** Algolia DocSearch o Fuse.js client-side per ricerca full-text
- **Navigazione:** Sidebar gerarchica per categoria, breadcrumb, link correlati

### 4.3 Versioning

- La documentazione API è versionata per major API version: `v1`, `v2`, ecc.
- Struttura URL: `docs.sycren.com/api/v1/`, `docs.sycren.com/api/v2/`
- Versione corrente mostrata di default, dropdown per switch versione
- Changelog documenta breaking changes tra versioni

### 4.4 Search

- Barra di ricerca globale nell'header
- Risultati con categorizzazione (API / Guides / Internal)
- Supporto comandi da tastiera (`Cmd+K`)
- Highlight del termine cercato nei risultati

### 4.5 Integrazione CI/CD

- Repo GitHub → Build automatica su push su `main`
- Deploy su Vercel su `docs.sycren.com`
- Preview deploy per ogni PR (subdomain temporaneo)
- Link check automatico (nessun broken link)
- Validazione formattazione MDX

---

## 5. Technical Stack

| Layer | Tecnologia | Note |
|---|---|---|
| Framework | Next.js 14+ (App Router) | SSG per docs, ISR per pagine dinamiche |
| Content | MDX | Frontmatter YAML, componenti React custom |
| Language | TypeScript 5+ (strict) | — |
| Styling | Tailwind CSS 3.4+ | Design token Sycren, dark/light theme |
| Search | Algolia DocSearch | Gratuito per open source / docs pubblici |
| Versioning | nextra versioning / custom | Basato su cartelle `v1/`, `v2/` |
| Deploy | Vercel | Connesso a GitHub, preview deploy |
| Domain | Cloudflare | CDN, SSL, WAF |
| Code highlighting | Shiki / Prism | Nei blocchi codice MDX |

### Variabili d'Ambiente

```bash
NEXT_PUBLIC_SITE_URL=https://docs.sycren.com
NEXT_PUBLIC_ALGOLIA_APP_ID=xxxxxxxxxx
NEXT_PUBLIC_ALGOLIA_SEARCH_API_KEY=xxxxxxxxxx
ALGOLIA_ADMIN_API_KEY=xxxxxxxxxx
NEXT_PUBLIC_GA_ID=G-XXXXXXXXXX
```

---

## 6. SEO Requirements

| Elemento | Requisito |
|---|---|
| URL structure | Gerarchica, leggibile, senza query params |
| Meta tags | title, description, og:title, og:description, og:image per ogni pagina |
| Canonical | Ogni pagina ha link canonico |
| Structured data | BreadcrumbList, SearchAction (site search schema) |
| Sitemap | `sitemap.xml` generato automaticamente, inviato a Google Search Console |
| Robots | `robots.txt` con allow per tutto /public, disallow per /internal |
| Performance | Lighthouse ≥ 90 mobile, LCP < 2s, CLS < 0.05 |
| Core Web Vitals | Monitorati via Vercel Analytics |
| Indicizzazione | Google Search Console configurata per `docs.sycren.com` |
| Broken links | CI check per ogni PR |

---

## 7. Design Requirements

### 7.1 Tema

Coerente con il brand Sycren già definito per la landing page:

**Dark (Default)** — `#0A0A0F` bg, `#6366F1` accent, `#F0F0FF` text
**Light** — `#FAFAFA` bg, `#4F46E5` accent, `#0F0F1A` text

### 7.2 Componenti Docs UI

- Sidebar navigazione (categoria espandibile, link attivo evidenziato)
- Breadcrumb navigazione
- Tabella dei contenuti (ToC) laterale per pagine lunghe
- Blocchi codice con syntax highlighting e bottone copia
- Callout (info, warning, danger, tip) in MDX
- Paginazione "Previous / Next" in fondo pagina
- Search modal con scorciatoia da tastiera
- Toggle dark/light theme

---

## 8. Completion Criteria

- [ ] `docs.sycren.com` live e accessibile pubblicamente via HTTPS
- [ ] API reference per almeno sycren-app, sycren-studio, sycren-cloud, sycren-domains, sycren-security
- [ ] Authentication & SSO Guide completa
- [ ] Getting Started + Portal Guide + Billing FAQ per clienti
- [ ] Internal onboarding + dev workflow + deployment procedures
- [ ] Search funzionante (Algolia o Fuse.js) con indicizzazione completa
- [ ] Versioning funzionante (almeno v1 attiva, struttura per v2 pronta)
- [ ] Dark / Light theme funzionante, persistente
- [ ] Sidebar navigazione con tutte le categorie
- [ ] Breadcrumb e ToC su ogni pagina
- [ ] Lighthouse ≥ 90 mobile
- [ ] Sitemap + robots.txt + Google Search Console configurati
- [ ] Nessun broken link (CI check passa)
- [ ] Preview deploy funzionante per ogni PR
- [ ] README del repo completo con istruzioni dev

---

## Related Notes

| Document | Description |
|---|---|
| [[docs.README]] | sycren-docs overview |
| [[docs.todo]] | Implementation plan |
| [[app.requirements\|sycren-app requirements]] | API docs to document |
| [[studio.requirements\|sycren-studio requirements]] | Client guides to document |
| [[cloud.requirements\|sycren-cloud requirements]] | Integration guides to document |
| [[domains.requirements\|sycren-domains requirements]] | DNS guides to document |
| [[security.requirements\|sycren-security requirements]] | Security guides to document |
| [[landing.requirements\|sycren-landing requirements]] | Public docs integration |
| [[foundation/ROADMAP\|Bootstrap Roadmap]] | Build order and timeline |

---

*Sycren — Docs Requirements PRD v1.0 — Giugno 2026*
*Documento interno. Non distribuire senza autorizzazione scritta.*
