# sycren-docs — TODO
> PRD v1.0 · Giugno 2026 · Fase 0 (Waitlist) · Allineato a Bootstrap Roadmap v1.3

---

## Contesto (dalla Roadmap)

- Entrambi i founder hanno **16 anni 🕐**, nessuna partita IVA — Soluzione B: costruire tutto ora, vendere al **18° compleanno 🎂**
- La documentazione deve essere pronta PRIMA del lancio commerciale — ogni servizio Sycren deve avere docs live prima di aprire le vendite
- Ordine di costruzione: Landing → sycren-db → Studio → App → sycren-docs → Cloud → Domains → Security
- sycren-docs viene dopo i servizi principali per poter documentare API reali, non ipotetiche
- Stima temporale: Settimana 4–6 di sviluppo complessivo (dopo Hub/App) per avere `docs.sycren.com` live con contenuti minimi
- Tutti i documenti sono scritti in Inglese. Localizzazioni future in roadmap separata

---

## Setup Iniziale

- [ ] Scegliere framework documentale (Nextra / Mintlify / Docusaurus / custom Next.js + MDX)
- [ ] Inizializzare progetto Next.js 14 con App Router e TypeScript strict (o template framework scelto)
- [ ] Configurare Tailwind CSS 3.4+ con design token Sycren (dark/light theme)
- [ ] Configurare MDX con frontmatter YAML e componenti React custom
- [ ] Creare struttura cartelle: `content/api/`, `content/guides/`, `content/internal/`, `content/changelog/`
- [ ] Creare layout base: Sidebar + Breadcrumb + ToC + Footer
- [ ] Implementare dark/light theme toggle (coerente con landing page)
- [ ] Configurare search (Algolia DocSearch o Fuse.js)
- [ ] Configurare versione API: cartella `content/api/v1/`, struttura per `v2/`
- [ ] Configurare next/font (Geist Mono per codice, Inter per testo)
- [ ] Creare `.env.example` con tutte le variabili d'ambiente
- [ ] Creare landing page docs (`docs.sycren.com` → redirect o pagina index)
- [ ] Configurare Vercel: collegare repo, impostare ENV, preview deploy
- [ ] Configurare Cloudflare: DNS per `docs.sycren.com`, CDN, SSL, WAF
- [ ] Configurare Google Search Console per `docs.sycren.com`

---

## Developer API Documentation

### sycren-app API Reference
- [ ] Documentare endpoint Authentication (register, login, logout, refresh token)
- [ ] Documentare endpoint Users (CRUD, profile, settings)
- [ ] Documentare endpoint Projects (create, list, update, delete, status)
- [ ] Esempi di richiesta/risposta in curl e TypeScript per ogni endpoint
- [ ] Tabella codici errore per ogni gruppo endpoint

### sycren-studio API Reference
- [ ] Documentare endpoint Clients (CRUD, search, notes)
- [ ] Documentare endpoint Tasks (create, assign, status transition, comments)
- [ ] Documentare endpoint Files (upload, download, list, delete)
- [ ] Documentare endpoint Milestones (create, timeline, completion)
- [ ] Documentare endpoint Invoices (list, status, download PDF)
- [ ] Esempi richiesta/risposta completi

### sycren-cloud API Reference
- [ ] Documentare endpoint VPS (provision, status, restart, resize, terminate)
- [ ] Documentare endpoint Deploy (trigger deploy, status, logs, rollback)
- [ ] Documentare endpoint Backups (create, list, restore, schedule)
- [ ] Documentare endpoint Monitoring (uptime, metrics, alerts)
- [ ] Documentare endpoint Domains (link VPS, DNS records)

### sycren-domains API Reference
- [ ] Documentare endpoint Search (domain availability, suggestions, TLD list)
- [ ] Documentare endpoint Register (purchase, privacy, nameservers)
- [ ] Documentare endpoint DNS (record CRUD, zone management)
- [ ] Documentare endpoint Transfer (incoming, outgoing, status)

### sycren-security API Reference
- [ ] Documentare endpoint WAF (rules, whitelist, blacklist, logs)
- [ ] Documentare endpoint DDoS (protection status, mitigation history)
- [ ] Documentare endpoint Monitoring (continuous scan, vulnerabilities)
- [ ] Documentare endpoint Reports (generate, schedule, export PDF)

### Guide Trasversali
- [ ] Authentication & SSO Guide (JWT flow, OAuth2, SSO setup, API keys scoping)
- [ ] Webhook Documentation (event list, payload schema, retry policy, HMAC verification)
- [ ] Rate Limiting & Error Handling (HTTP headers, error codes, exponential backoff)
- [ ] SDK Reference — TypeScript (install, init, examples per ogni servizio)
- [ ] SDK Reference — Python (install, init, examples per ogni servizio)
- [ ] Changelog v1.0 (initial release, endpoint list, known limitations)
- [ ] Migration Guide template (struttura per future versioni)

---

## Client Guides

- [ ] Getting Started with Sycren Studios (creazione account, primo progetto, dashboard)
- [ ] Client Portal User Guide (panoramica, navigazione, sezione progetti, comunicazioni)
- [ ] How to Request a Quote (form, campi, tempi di risposta, follow-up)
- [ ] Project Management Workflow (creazione milestone, task, upload file, tracking)
- [ ] Billing & Invoicing FAQ (prezzi, metodi pagamento, fatture, rimborsi, VAT)
- [ ] Maintenance Retainers Guide (cosa include, come attivare, disdetta)
- [ ] Troubleshooting & Support (problemi comuni, contatti supporto, SLA)

---

## Internal Documentation

- [ ] Onboarding Guide for New Team Members (tooling, contatti, risorse, setup ambiente)
- [ ] Development Workflow (git branch strategy, PR lifecycle, code review, release)
- [ ] Code Review Guidelines (checklist, standard, criteri approvazione)
- [ ] Deployment Procedures (deploy manuale su Vercel, rollback, monitoraggio)
- [ ] Architecture Overview (stack decision log, diagrammi, rationale tecnico)
- [ ] Security Policies (secret management, access control, vulnerability disclosure)
- [ ] Runbook / Incident Response (procedure emergenza, escalation, contatti)
- [ ] Brand & Design Guidelines (colori, loghi, typography, tone of voice)

---

## UI / Componenti Docs

- [ ] Sidebar (categorie espandibili, link attivo, scroll, mobile drawer)
- [ ] Breadcrumb (Home > API > V1 > Authentication)
- [ ] Table of Contents (ToC laterale, highlight sezione attiva, smooth scroll)
- [ ] Code Block (syntax highlighting, linguaggio label, copia button, line numbers)
- [ ] Callout componenti (Info, Warning, Danger, Tip) in MDX
- [ ] Pagination (Previous / Next page in fondo)
- [ ] Search modal (overlay, risultati categorizzati, keyboard shortcut)
- [ ] Version dropdown (switch tra v1, v2, ecc., badge "latest")
- [ ] Dark/Light theme toggle (icona sole/luna, persistenza localStorage)
- [ ] Mobile responsive (sidebar collassabile, font scaling)

---

## Integrazioni

- [ ] Algolia DocSearch: indicizzare tutti i contenuti pubblici
- [ ] CI/CD: GitHub Actions per lint MDX + link check su ogni PR
- [ ] Prebuild hook: generare sitemap.xml e robots.txt automaticamente
- [ ] Vercel Analytics: monitorare traffico, pagine popolari, 404
- [ ] Google Search Console: inviare sitemap, monitorare indicizzazione
- [ ] 404 page personalizzata (suggerimenti, search bar, link utili)

---

## Verifica e QA

- [ ] Tutti gli endpoint API documentati hanno esempi di richiesta e risposta
- [ ] Ogni pagina ha title e meta description univoci
- [ ] Nessun broken link in tutto il sito (CI check ok)
- [ ] Search indicizza tutti i contenuti pubblici
- [ ] Dark/Light theme funzionante su tutte le pagine
- [ ] Version switch funzionante (almeno v1)
- [ ] Mobile: sidebar funzionante, ToC collassabile, testo leggibile
- [ ] Lighthouse ≥ 90 mobile
- [ ] Schema.org markup presente sulla index page
- [ ] sitemap.xml valido, inviato a Google
- [ ] docs.sycren.com risponde HTTPS con certificato valido
- [ ] Preview deploy funzionante su PR

---

## Oltre Docs — Cosa Segue (con Età)

| # | Modulo | Età 🕐 | Responsabile |
|---|---|---|---|
| 1 | Landing Page `sycren.com` | **16** | Angelo (lead), Cosimo (lead) |
| 2 | Database Foundation `sycren-db` | **16** | Angelo (lead) |
| 3 | Client Portal `studio.sycren.com` | **16** | Angelo (lead), Cosimo (lead) |
| 4 | Hub Centrale / SSO `app.sycren.com` | **16-17** | Angelo (lead), Cosimo (supporto) |
| 5 | Docs Platform `docs.sycren.com` | **16-17** | Angelo (lead), Cosimo (supporto) |
| 6 | Cloud Lite `cloud.sycren.com` | **17** | Angelo (lead) |
| 7 | Domains `domains.sycren.com` | **17-18** | — |
| 8 | Security `security.sycren.com` | **18+** | — |
| 🎂 | **Lancio Commerciale (P.IVA, Vendite)** | **18** | Entrambi |

---

*Sycren Docs — TODO v1.0 · Giugno 2026 · Allineato a Bootstrap Roadmap v1.3*
