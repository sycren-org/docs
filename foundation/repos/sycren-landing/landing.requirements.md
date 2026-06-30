# sycren-landing — Requirements (PRD)

> PRD v2.1 — Giugno 2026 — Fase 0 (WAITLIST) — Allineato a Bootstrap Roadmap v1.3
> **Età dei founder: 🕐 16 anni — nessuna vendita attiva, solo raccolta contatti pre-lancio**

---

## 1. Contesto e Obiettivo

Sycren è una piattaforma unificata per sviluppo software, cloud hosting, gestione domini e cybersecurity. La landing page (`sycren.com`) è il primo punto di contatto pubblico del brand — attiva nella Fase 0, prima di qualsiasi attività commerciale.

**Missione aziendale:** eliminare la frammentazione tecnologica, offrendo alle aziende un unico partner per sviluppo, infrastruttura, domini e sicurezza.

**Strategia bootstrap (Soluzione B):** entrambi i founder hanno 16 anni 🕐 e nessuna partita IVA. Il vincolo anagrafico viene trasformato in vantaggio strategico: tutto il periodo pre-commerciale è dedicato a costruire prodotto, brand e pipeline clienti. La landing page è il primo mattone.

> **Costruisci tutto ora. Vendi al momento giusto (18 anni 🎂). La struttura segue i ricavi, non li precede.**

**Obiettivo primario:** Comunicare il posizionamento di Sycren e raccogliere contatti qualificati (waitlist) prima del lancio commerciale. Ogni contatto raccolto ora è un potenziale primo cliente quando Sycren aprirà le vendite.

**Obiettivo secondario:** Stabilire credibilità tecnica immediata verso un target B2B internazionale — startup, PMI e aziende che cercano un partner tecnico affidabile. Sycren non si posiziona come fornitore di servizi ma come alleato strategico.

**Stato attuale (Giugno 2026):**
- [x] Organizzazione GitHub `sycren` creata con struttura repository completa
- [x] Dominio `sycren.com` registrato — DNS su Cloudflare
- [ ] Landing page in sviluppo — **questo repository**
- [ ] Email con dominio via Cloudflare Email Routing
- [ ] Sistema raccolta contatti waitlist attivo

---

## 2. Target Users

| Segmento | Caratteristiche |
|---|---|
| Startup early-stage | Cercano un partner tecnico per costruire l'MVP. Budget limitato, alta urgenza. |
| PMI in digitalizzazione | Necessitano di un sito professionale o applicazione gestionale. Valutano affidabilità e comunicazione. |
| Developer e team tecnici | Potenziali clienti Cloud e Security. Valutano la qualità tecnica dell'offerta e l'architettura. |
| Clienti UK/US/Middle East | Abituati a comunicazione diretta e professionale. Pricing in EUR. Standard elevati di UX. |

**Lingua della pagina: Inglese.** Il mercato principale è internazionale. Localizzazioni secondarie da considerare in roadmap futura.

---

## 3. Architettura della Pagina

La landing page è una **single-page application (SPA)** in Next.js 14+ (App Router), deployata su VPS (Hetzner) via GitHub Actions. URL: `sycren.com`.

### Sezioni in ordine (9 sezioni)

1. **Header / Navbar** — Logo, navigazione, CTA primario, sticky con scroll blur
2. **Hero** — Headline, sottotitolo, CTA principale, griglia punti animata, terminal block, contatore waitlist
3. **Divisioni** — 4 card operative: Studios, Cloud, Domains, Security
4. **How It Works** — Flusso di lavoro in 4 step (orientato a Studios)
5. **Tech Stack** — Griglia tecnologie con tooltip hover
6. **Team / Founder** — Profili Angelo e Cosimo
7. **Pricing** — 4 pacchetti con prezzi espliciti
8. **Waitlist Form** — Raccolta contatti con validazione
9. **Footer** — Link, social, contatti, nota legale Fase 0

---

## 4. Requisiti Funzionali — Dettaglio Feature

### 4.1 Header / Navbar

- Logo Sycren a sinistra (SVG inline, non immagine esterna)
- Link di navigazione alle sezioni: `Services`, `How it works`, `Stack`, `Pricing`, `Team`
- CTA primario a destra: `Join the waitlist` (Fase 0) / `Request a quote` (Fase 2)
- Comportamento scroll: sfondo trasparente sopra l'hero, sfondo solido con backdrop-blur dopo 80px di scroll
- Sticky: `position: sticky` con `top: 0`, rimane visibile durante tutta la navigazione
- Mobile (≤ 768px): hamburger icon → drawer/menu laterale con tutti i link e il CTA
- Tema toggle: 3 bottoni (Dark ☾ / Light ☀ / Black ●) con persistenza localStorage

### 4.2 Hero

**Elementi richiesti:**
- `<h1>` principale: `One partner. Every layer of your stack.`
- Sottotitolo: `Sycren builds, hosts, and secures digital products for startups and companies that need a reliable technical partner — not just another vendor.`
- CTA primario: `Join the waitlist →` (Fase 0) / `Request a quote →` (Fase 2)
- CTA secondario testuale: `Explore our services ↓` con smooth scroll alla sezione divisioni
- Elemento visivo distintivo: griglia di punti animata che reagisce al cursore (indigo gradient)
- Terminal block con snippet deploy/HTTP stilizzato (effetto finestra terminale)
- Social proof: contatore dinamico `X companies already waiting` aggiornato via API interna (`/api/waitlist/count`)

**Animazione:**
- Entrata orchestrata: headline → sottotitolo → CTA → elemento visivo, con delay progressivi (Framer Motion)
- Supporto `prefers-reduced-motion`: versione statica, nessuna animazione

### 4.3 Sezione Divisioni

Quattro card in griglia 2×2 su desktop, colonna singola su mobile.

**Ogni card contiene:**
- Nome divisione con badge colorato univoco
- Icona rappresentativa della divisione (Lucide icons)
- Descrizione in massimo 2 frasi
- Status badge: `Available` (Studios) / `Coming soon` (Cloud, Domains, Security — solo in Fase 0)
- CTA secondario: `Learn more →` o freccia

**Badge colori divisione:**

| Divisione | Colore badge (dark theme) | Colore badge (light theme) |
|---|---|---|
| Studios | Indigo `#6366F1` | Indigo `#4F46E5` |
| Cloud | Azzurro `#38BDF8` | Blu `#0284C7` |
| Domains | Verde `#34D399` | Verde `#059669` |
| Security | Ambra `#FBBF24` | Ambra `#D97706` |

**Contenuto card:**

- **Studios:** *Sycren Studios builds digital products for startups and companies that need more than a freelancer. Web apps, landing pages, API integrations, and ongoing maintenance — all managed from a single client portal.*
- **Cloud:** *Managed hosting and VPS provisioning with automated deploy from GitHub, uptime monitoring, and one-click backups. Your infrastructure, managed.*
- **Domains:** *Search, register, and manage domains with an advanced DNS panel. Integrated directly with Sycren Cloud and Security.*
- **Security:** *Web Application Firewall, anti-DDoS protection, continuous monitoring, and monthly security reports for your infrastructure.*

### 4.4 Sezione "How It Works"

| Step | Titolo | Descrizione |
|---|---|---|
| 1 | Request a quote | Fill in the project form — type, budget, deadline. We respond within 24 hours. |
| 2 | Onboarding | We gather requirements, align on scope, and share the project timeline. |
| 3 | Build | You track progress in real time via the client portal. Milestones, files, and communication in one place. |
| 4 | Deliver & maintain | We deliver the project and offer ongoing maintenance retainers for updates and monitoring. |

Sotto i 4 step: `50% upfront · Transparent pricing · No hidden fees`

### 4.5 Sezione Stack Tecnologico

| Categoria | Tecnologie |
|---|---|
| Frontend | React, Next.js, TypeScript, Tailwind CSS |
| Backend | Node.js, Fastify, NestJS, Express |
| Database | PostgreSQL, MongoDB, Redis |
| DevOps | Docker, GitHub Actions, Kubernetes |
| Linguaggi | Python, Go, Rust |
| Piattaforme | Cloudflare, Hetzner |

Tooltip al hover: nome tecnologia + "Used for: `descrizione breve`"

### 4.6 Sezione Team / Founder

Due card affiancate (desktop) / stacked (mobile). **Nessuna menzione dell'età.**

**Card Angelo Paciello:**
- Ruolo: `Founder & CEO`
- Stack: React, Next.js, TypeScript, Node.js, Fastify, PostgreSQL, MongoDB, Redis, Docker, Python
- GitHub: [P4ciuf](https://github.com/P4ciuf)
- Portfolio: [p4ciuf.netlify.app](https://p4ciuf.netlify.app)
- Avatar: placeholder con iniziali

**Card Cosimo Russo:**
- Ruolo: `Co-Founder & Managing Director`
- Stack: HTML5, CSS3, JavaScript, TypeScript, Node.js, Express, MongoDB, SEO
- GitHub: [Ninja1389](https://github.com/Ninja1389)
- Portfolio: [ninja1389.netlify.app](https://ninja1389.netlify.app)
- Avatar: placeholder con iniziali

### 4.7 Sezione Pricing

Prezzi espliciti, nessun "contact us for pricing" per i pacchetti base.

| Pacchetto | Prezzo | Incluso | Badge |
|---|---|---|---|
| Landing Page Pro | From €500 | Next.js, SEO on-page, deploy, domain setup | — |
| Web App MVP | From €2,500 | Auth, dashboard, DB, deploy, 30d support | `Most popular` |
| API Integration | From €1,200 | System bridge, automated tests, technical docs | — |
| Monthly Retainer | €300/mo | Updates, support, uptime monitoring | `Recurring` |

**Comportamento:**
- Card `Web App MVP` visivamente evidenziata (bordo accent, glow effect)
- Sotto ogni card: CTA `Get a quote →` che porta al form waitlist (ancora nella stessa pagina)
- Nota legale sotto la griglia: `All prices in EUR · VAT not included · 50% upfront on project start`

### 4.8 Waitlist Form

**Campi del form:**
- `Email` — obbligatorio, validazione formato email
- `Name` — opzionale
- `Project type` — dropdown: Web Application, Landing Page, API Integration, Cloud Hosting, Security, Other
- `Message` — textarea opzionale, max 500 caratteri
- Honeypot field nascosto `website` per anti-spam (non visibile all'utente, riempito solo da bot)

**Comportamento submit:**
- Validazione client-side con React Hook Form + Zod
- POST verso API Route Next.js `/api/waitlist`
- Chiamata al SDK Resend per invio email
- Email di conferma automatica all'utente
- Email di notifica ai founder (`angelo@sycren.com`, `cosimo@sycren.com`)
- Feedback visivo inline: spinner durante submit → messaggio di successo (verde) o errore (rosso)
- Disabilita il bottone durante il caricamento per evitare doppi invii

### 4.9 Footer

- Logo Sycren (SVG)
- Navigazione secondaria: link alle sezioni della pagina
- Link social con icone: GitHub (`github.com/sycren`), X/Twitter, LinkedIn, Discord
- Email di contatto: `hello@sycren.com`
- Nota Fase 0 (solo in WAITLIST mode): `Currently in pre-launch — join the waitlist above.`
- Copyright: `© 2026 Sycren. All rights reserved.`
- Lingua: Inglese

### 4.10 Stato Pagina — WAITLIST vs LIVE

La pagina ha due stati operativi gestiti esclusivamente via variabile d'ambiente `NEXT_PUBLIC_LAUNCH_MODE`:

| Variabile | Descrizione | Differenze visive |
|---|---|---|
| `WAITLIST` (default) | Raccolta contatti pre-lancio (🕐 16-17 anni) | CTA = "Join the waitlist", badge "Coming soon" attivi su Cloud/Domains/Security, nota footer presente, nessuna vendita attiva |
| `LIVE` | Vendita attiva (🎂 18+ anni) | CTA = "Request a quote", badge "Coming soon" rimossi (tutte le divisioni "Available"), nota footer rimossa |

**Regola:** Nessuna modifica al codice richiesta tra i due stati — solo aggiornamento della ENV e redeploy via GitHub Actions.

---

## 5. Stack Tecnologico

| Layer | Tecnologia | Versione |
|---|---|---|
| Framework | Next.js (App Router) | 14+ |
| Linguaggio | TypeScript (strict mode) | 5+ |
| Styling | Tailwind CSS | 3.4+ |
| Animazioni | Framer Motion | 11+ |
| Form | React Hook Form | 7+ |
| Validazione | Zod | 3+ |
| Icone | Lucide React | — |
| Email | Resend SDK | — |
| Font | Geist Mono + Inter (via `next/font`) | — |

### Variabili d'Ambiente

```bash
RESEND_API_KEY=re_xxxxxxxxxxxxx
NEXT_PUBLIC_LAUNCH_MODE=WAITLIST          # WAITLIST (16-17) | LIVE (18+)
NEXT_PUBLIC_SITE_URL=https://sycren.com
FOUNDER_EMAIL_1=angelo@sycren.com
FOUNDER_EMAIL_2=cosimo@sycren.com
```

---

## 6. Requisiti di Design

### Tema — Tre Varianti

| Proprietà | Dark (default) | Light | Black |
|---|---|---|---|
| Sfondo | `#0A0A0F` | `#FAFAFA` | `#000000` |
| Accent | `#6366F1` | `#4F46E5` | `#FFFFFF` |
| Testo primario | `#F0F0FF` | `#0F0F1A` | `#FFFFFF` |
| Card bg | `#12121A` | `#FFFFFF` | `#000000` |
| Bordo | `#1E1E2A` | `#E5E5EB` | `#1A1A1A` |

### Design Token

Niente colori hex hardcoded nel JSX. Tutti i colori sono definiti come CSS custom properties su `:root`, `.dark`, `.light`, `.black` e referenziati via classi Tailwind configurate in `tailwind.config.ts`.

### Accessibilità

- Contrasto WCAG AA verificato per tutti e tre i temi (rapporto ≥ 4.5:1 per testo normale)
- Supporto `prefers-reduced-motion`: animazioni disattivate
- Label su tutti i campi form
- ARIA labels su elementi interattivi
- Focus visible ring su link e bottoni
- Navigazione da tastiera completa

---

## 7. Performance

| Metrica | Target |
|---|---|
| Lighthouse Performance (mobile) | ≥ 90 |
| Lighthouse Accessibility (mobile) | ≥ 90 |
| LCP (Largest Contentful Paint) | < 2.0s |
| CLS (Cumulative Layout Shift) | < 0.05 |
| Bundle JS (gzipped) | < 150KB |
| First Input Delay (FID) | < 50ms |
| Time to Interactive (TTI) | < 3.0s |

### Strategie di ottimizzazione

- Lazy loading per immagini e componenti fuori schermo
- Dynamic imports (`next/dynamic`) per componenti pesanti (es. animazioni hero)
- `next/font` per caricamento font ottimizzato (nessun CDN esterno)
- Ottimizzazione immagini via `next/image`
- Bundle splitting automatico di Next.js
- Minificazione e compressione (Next.js built-in)

---

## 8. Criteri di Completamento

- [ ] Tutte e 9 le sezioni implementate e funzionanti
- [ ] Form waitlist funzionante con invio email via Resend
- [ ] Honeypot anti-spam attivo e verificato
- [ ] Tema Dark / Light / Black con toggle e localStorage
- [ ] Comportamento Fase 0 (WAITLIST) verificato: CTA, badge, footer notice
- [ ] Navbar sticky con backdrop-blur e smooth scroll alle sezioni
- [ ] Menu mobile funzionante su 375px viewport
- [ ] Lighthouse Performance ≥ 90 su mobile
- [ ] Lighthouse Accessibility ≥ 90 su mobile
- [ ] Deploy attivo su `sycren.com` con SSL (VPS + Cloudflare)
- [ ] Nessun segreto o API key committed nel repository
- [ ] `.env.example` con tutte le variabili documentate
- [ ] `prefers-reduced-motion` supportato
- [ ] Bundle JS < 150KB gzipped
- [ ] LCP < 2.0s, CLS < 0.05

---

## 9. Dipendenze del Progetto

### Dipendenze principali (`dependencies`)

```json
{
  "next": "^14.2.0",
  "react": "^18.3.0",
  "react-dom": "^18.3.0",
  "framer-motion": "^11.0.0",
  "react-hook-form": "^7.50.0",
  "@hookform/resolvers": "^3.3.0",
  "zod": "^3.22.0",
  "lucide-react": "^0.350.0",
  "resend": "^3.0.0",
  "clsx": "^2.1.0"
}
```

### Dipendenze di sviluppo (`devDependencies`)

```json
{
  "typescript": "^5.4.0",
  "@types/node": "^20.11.0",
  "@types/react": "^18.2.0",
  "@types/react-dom": "^18.2.0",
  "tailwindcss": "^3.4.0",
  "postcss": "^8.4.0",
  "autoprefixer": "^10.4.0",
  "eslint": "^8.56.0",
  "eslint-config-next": "^14.2.0",
  "@typescript-eslint/eslint-plugin": "^7.0.0",
  "@typescript-eslint/parser": "^7.0.0"
}
```

---

## 10. Struttura Cartelle

```text
sycren-landing/
├── app/
│   ├── layout.tsx
│   ├── page.tsx
│   ├── globals.css
│   ├── sitemap.ts
│   ├── robots.ts
│   └── api/
│       └── waitlist/
│           ├── route.ts
│           └── count/
│               └── route.ts
├── components/
│   ├── layout/
│   │   ├── Header.tsx
│   │   ├── Footer.tsx
│   │   └── MobileMenu.tsx
│   ├── sections/
│   │   ├── Hero.tsx
│   │   ├── Divisions.tsx
│   │   ├── HowItWorks.tsx
│   │   ├── TechStack.tsx
│   │   ├── Team.tsx
│   │   ├── Pricing.tsx
│   │   └── WaitlistForm.tsx
│   └── ui/
│       ├── Button.tsx
│       ├── Badge.tsx
│       ├── Card.tsx
│       ├── DivisionCard.tsx
│       ├── PricingCard.tsx
│       ├── TerminalBlock.tsx
│       └── AnimatedGrid.tsx
├── lib/
│   ├── utils.ts
│   ├── constants.ts
│   ├── resend.ts
│   └── schemas.ts
├── public/
│   └── favicon.ico
├── .env.example
├── .gitignore
├── next.config.ts
├── tailwind.config.ts
├── tsconfig.json
├── postcss.config.js
├── package.json
└── README.md
```

---

*Sycren — Landing Page PRD v2.1 · Giugno 2026 · Fase 0 (WAITLIST) · sycren-landing repository*
*Documento interno. Non distribuire senza autorizzazione scritta.*
