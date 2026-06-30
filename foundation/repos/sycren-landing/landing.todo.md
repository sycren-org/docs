# sycren-landing — TODO

> PRD v2.1 · Giugno 2026 · Fase 0 (WAITLIST) · Allineato a Bootstrap Roadmap v1.3
> **Età dei founder: 🕐 16 anni — priorità #1 dell'intera piattaforma**
> Stima: Settimana 1–2 per avere `sycren.com` live con landing e waitlist

---

## Contesto

- Entrambi i founder hanno **16 anni 🕐**, nessuna partita IVA — Soluzione B: costruire tutto ora, vendere al **18° compleanno 🎂**
- GitHub org `sycren` e dominio `sycren.com` già attivi, DNS su Cloudflare
- La landing page è **priorità #1** — ogni altra dipendenza (sycren-db, Studio, App) viene dopo
- Ogni contatto raccolto via waitlist ora è un potenziale primo cliente al lancio
- Obiettivo: comunicare posizionamento, stabilire credibilità tecnica, raccogliere lead qualificati
- Nessuna vendita attiva — solo raccolta contatti pre-lancio

**Principio guida:**
> Costruisci tutto ora. Vendi al momento giusto (18 anni). La struttura segue i ricavi, non li precede.

---

## Setup iniziale

- [x] Creare organizzazione GitHub `sycren` — struttura repository completa
- [x] Registrare `sycren.com` — DNS su Cloudflare
- [x] **Inizializzare progetto Next.js 14** con App Router e TypeScript strict
  - `npx create-next-app@latest sycren-landing --typescript --app --src-dir=false --import-alias="@/*"`
  - Abilitare Turbopack per sviluppo veloce
- [x] **Installare dipendenze runtime**
  - `npm install framer-motion react-hook-form @hookform/resolvers zod lucide-react resend clsx`
- [x] **Installare devDependencies**
  - `npm install -D @typescript-eslint/eslint-plugin @typescript-eslint/parser eslint-config-next`
- [x] **Configurare next/font**
  - Geist Mono (font monospace per terminal block e codice)
  - Inter (font principale per corpo testo)
  - Nessun font caricato da CDN esterno — tutto via `next/font`
- [x] **Creare `.env.example`** con tutte le variabili d'ambiente documentate
  ```bash
  NEXT_PUBLIC_LAUNCH_MODE=WAITLIST
  NEXT_PUBLIC_SITE_URL=https://sycren.com
  FOUNDER_EMAIL_1=angelo@sycren.com
  FOUNDER_EMAIL_2=cosimo@sycren.com
  ```
- [x] **Impostare struttura cartelle**
  ```text
  app/  components/  lib/  public/
  ```
  - `app/` — layout, page, globals.css, API routes, sitemap, robots
  - `components/layout/` — Header, Footer, MobileMenu
  - `components/sections/` — Hero, Divisions, HowItWorks, TechStack, Team, Pricing, WaitlistForm
  - `components/ui/` — Button, Badge, Card, DivisionCard, PricingCard, TerminalBlock, AnimatedGrid
  - `lib/` — utils, constants, resend client, zod schemas
- [x] **Configurare VPS (Hetzner)**
  - Server Ubuntu 22.04 con Docker e docker-compose installati
  - Aggiungere ENV variables nel GitHub Secret
  - Configurare dominio custom `sycren.com`
- [x] **Configurare Cloudflare DNS**
  - Puntare A/AAAA records a Hetzner IPs
  - Impostare proxy CDN arancione
  - Abilitare WAF base (Security Level: Medium, Challenge Passage: 5 min)
  - Configurare SSL/TLS: Full (strict)
  - Impostare Page Rules per caching assets statici
- [x] **Configurare Cloudflare Email Routing**
  - Catch-all per `@sycren.com` → email personali founder
  - Routing specifico per `angelo.paciello@sycren.com` e `cosimo.russo@sycren.com`

---

## Design System

- [ ] **Definire CSS custom properties** in `globals.css` per i 3 temi
  - **Dark (default):** `--bg: #0A0A0F`, `--accent: #6366F1`, `--text: #F0F0FF`, `--card: #12121A`, `--border: #1E1E2A`
  - **Light:** `--bg: #FAFAFA`, `--accent: #4F46E5`, `--text: #0F0F1A`, `--card: #FFFFFF`, `--border: #E5E5EB`
  - **Black:** `--bg: #000000`, `--accent: #FFFFFF`, `--text: #FFFFFF`, `--card: #000000`, `--border: #1A1A1A`
- [ ] **Configurare Tailwind** per usare le CSS custom properties
  - `tailwind.config.ts`: estendere `colors` con `bg`, `accent`, `text`, `card`, `border`
  - Aggiungere varianti dark personalizzate per il tema Black
- [ ] **Implementare tema toggle** nell'Header
  - 3 bottoni icona: Moon (Dark) / Sun (Light) / Circle (Black)
  - Stato attivo evidenziato
  - Accessibile: aria-label, focus ring
- [ ] **Persistere scelta tema in localStorage**
  - Chiave: `sycren-theme`
  - Leggere al mount del layout, applicare classe al `<html>`
- [ ] **Rispettare `prefers-color-scheme`** alla prima visita
  - Se nessun tema in localStorage, usare `prefers-color-scheme` (dark → Dark, light → Light)
- [ ] **Verificare WCAG AA contrasto** per tutti e 3 i temi
  - Testare ogni combinazione bg/testo
  - Testare link e stati hover/focus
  - Documentare i rapporti di contrasto

---

## UI Components

- [ ] **Button** — Due varianti (`primary` / `secondary`)
  - Supporto per `asChild` (render come `<a>` o `<button>`)
  - Stati: default, hover, active, disabled, loading (con spinner)
  - Props: variant, size (sm/md/lg), isLoading, disabled, className
- [ ] **Badge** — Pill colorati per divisione
  - Colori: indigo (Studios), blue (Cloud), green (Domains), amber (Security)
  - Variante "Coming soon" con stile warning
  - Compact, testo inline
- [ ] **Card** — Contenitore base
  - `bg-card` (variabile tema), `border`, `rounded-xl`
  - Hover effect: leggero translate-y e shadow increase
  - Padding configurabile
- [ ] **DivisionCard** — Card specializzata per sezione Divisioni
  - Badge colorato, icona Lucide, nome, descrizione, status badge
  - Status: `Available` (verde) / `Coming soon` (ambra)
  - Hover: glow sul bordo del colore della divisione
- [ ] **PricingCard** — Card per pacchetto prezzo
  - Nome pacchetto, prezzo (evidenziato), lista inclusi, CTA
  - Variante highlight per "Most popular": bordo accent, glow, badge "Most popular" in alto
  - Variante "Recurring" con badge dedicato
- [ ] **TerminalBlock** — Blocco stile terminale per Hero
  - Title bar con 3 cerchi RGB (rosso, giallo, verde)
  - Snippet codice con sintassi colorata
  - Effetto glow/scanline opzionale
  - Testo monospace (Geist Mono)

---

## Layout

- [ ] **Header/Navbar**
  - Logo Sycren a sinistra (SVG inline)
  - Nav links: Services, How it works, Stack, Pricing, Team
  - CTA primario: `Join the waitlist`
  - Tema toggle (3 bottoni)
  - Sticky: `position: sticky; top: 0; z-index: 50`
  - Scroll behavior: `bg-transparent` sopra hero, `bg-card/80 backdrop-blur-md` dopo 80px
  - Transizione fluida tra stati scroll
- [ ] **Mobile Menu** (≤ 768px)
  - Hamburger icon (Lucide `Menu`)
  - Drawer laterale con overlay scuro
  - Tutti i link di navigazione + CTA
  - Animazione di apertura/chiusura con Framer Motion
  - `aria-expanded`, `aria-controls` per accessibilità
- [ ] **Footer**
  - Logo Sycren (stesso SVG dell'Header)
  - Navigazione secondaria: link alle sezioni
  - Link social: GitHub, X/Twitter, LinkedIn, Discord (icone Lucide)
  - Email: `hello@sycren.com` (link mailto)
  - Nota Fase 0: `Currently in pre-launch — join the waitlist above.` (condizionale ENV)
  - Copyright: `© 2026 Sycren. All rights reserved.`

---

## Sections

- [ ] **Hero**
  - `<h1>`: `One partner. Every layer of your stack.`
  - Paragrafo sottotitolo (sopra)
  - CTA primario: `Join the waitlist →`
  - CTA secondario: `Explore our services ↓` (smooth scroll a #divisions)
  - AnimatedGrid: griglia punti con effetto mouse tracking (indigo gradient)
  - TerminalBlock: snippet codice deploy/HTTP
  - Contatore waitlist: `X companies already waiting` (fetch da `/api/waitlist/count`)
  - Orchestrated entrance: headline → subtitle → CTA → visual element (Framer Motion, staggerChildren)
  - `prefers-reduced-motion`: nessuna animazione, tutto visibile staticamente
- [ ] **Divisions**
  - Griglia 2×2 (desktop) / 1 colonna (mobile)
  - 4 DivisionCard (Studios, Cloud, Domains, Security)
  - Cloud/Domains/Security mostrano badge "Coming soon" (solo WAITLIST mode)
  - Studios mostra "Available"
- [ ] **How It Works**
  - 4 step in orizzontale (desktop) / verticale (mobile)
  - Connettori/linee tra gli step
  - Step numerati: 1→2→3→4
  - Sotto: `50% upfront · Transparent pricing · No hidden fees`
- [ ] **Tech Stack**
  - 6 categorie: Frontend, Backend, Database, DevOps, Languages, Platforms
  - 22 tecnologie totali in griglia
  - Tooltip al hover: nome + "Used for: `descrizione`"
  - Icone/logo semplificati per ogni tecnologia (opzionale)
- [ ] **Team**
  - 2 card affiancate (desktop) / stacked (mobile)
  - Angelo Paciello — Founder & CEO
  - Cosimo Russo — Co-Founder & Managing Director
  - Nessuna menzione dell'età
  - Avatar placeholder iniziali
  - Stack tecnologico come tag
  - Link GitHub e portfolio
- [ ] **Pricing**
  - 4 PricingCard
  - "Web App MVP" evidenziata (Most popular)
  - Prezzi espliciti: €500, €2,500, €1,200, €300/mo
  - CTA: `Get a quote →` (ancora alla sezione form)
  - Nota legale sotto: `All prices in EUR · VAT not included · 50% upfront on project start`
- [ ] **Waitlist Form**
  - Campi: Email (required), Name (optional), Project type (dropdown), Message (textarea, max 500)
  - Honeypot: campo hidden `website` (nome non standard, non visibile)
  - Validazione client-side: React Hook Form + Zod schema
  - Submit: POST a `/api/waitlist`, feedback loading/success/error
  - Disabilita bottone durante submit
  - Successo: messaggio "You're on the list! We'll be in touch."
  - Errore: messaggio chiaro con possibilità di riprovare
- [ ] **Schema.org Organization JSON-LD**
  - Iniettato in `<head>` via `layout.tsx` o `page.tsx`
  - name: Sycren, url: https://sycren.com, sameAs: `[GitHub, X, LinkedIn, Discord]`

---

## API

- [ ] **`/api/waitlist`** — POST route
  - Validazione body con Zod schema (email, name?, projectType?, message?, website?)
  - Controllo honeypot: se `website` è popolato → return 200 falsa (silent reject)
  - Invio email di conferma all'utente tramite Resend
    - Template: "Thanks for joining the waitlist, `[Name]`!"
    - Contenuto: conferma iscrizione, cosa aspettarsi, contatti
  - Invio email di notifica ai founder
    - Destinatari: `angelo@sycren.com`, `cosimo@sycren.com`
    - Contenuto: Nome, Email, Tipo progetto, Messaggio, timestamp
  - Gestione errori: try/catch con log, return 500 con messaggio generico
  - Rate limiting base (opzionale, da middleware)
- [ ] **`/api/waitlist/count`** — GET route
  - Legge conteggio totale dal database (o da contatore incrementale)
  - Return JSON: `{ count: number }`
  - Cache: 60s (configurabile via middleware o CDN)
  - Se non ancora collegato a DB, return numero statico incrementale
- [ ] **Resend client** — `lib/resend.ts`
  - Inizializzazione SDK con `RESEND_API_KEY`
  - Funzione helper `sendEmail({ to, subject, html })`
  - Template email in HTML inline (no JSX, massima compatibilità)

---

## SEO & Performance

- [ ] **Title e meta description**
  - Title: `Sycren — One partner. Every layer of your stack.`
  - Description: `Sycren builds, hosts, and secures digital products for startups and companies. Software development, cloud hosting, domain management, and cybersecurity — unified.`
- [ ] **Open Graph tags**
  - `og:title`, `og:description`, `og:url`, `og:type: website`
  - `og:image`: logo/hero screenshot (generato staticamente)
  - `og:locale: en_US`
- [ ] **Twitter Card**
  - `twitter:card: summary_large_image`
  - `twitter:title`, `twitter:description`, `twitter:image`
- [ ] **sitemap.xml** — `app/sitemap.ts`
  - Statico: solo root `/`
  - `lastModified`, `changeFrequency: monthly`, `priority: 1.0`
- [ ] **robots.txt** — `app/robots.ts`
  - Allow all crawlers
  - Sitemap: `https://sycren.com/sitemap.xml`
- [ ] **Lazy loading** immagini e componenti fuori schermo
  - `next/image` con `loading="lazy"` per immagini sotto la fold
  - `IntersectionObserver` per componenti animati (animazioni solo quando visibili)
- [ ] **Dynamic imports** per componenti pesanti
  - `next/dynamic` per AnimatedGrid, TerminalBlock, WaitlistForm
  - `ssr: false` per componenti con solo client-side logic (es. contatore animato)
- [ ] **Lighthouse optimization**
  - Eliminare render-blocking resources
  - Ottimizzare Cumulative Layout Shift (dimensioni esplicite per immagini e media)
  - Minimizzare JavaScript non utilizzato
  - Server Components dove possibile (layout, sezioni statiche)
  - `next/script` con strategia `afterInteractive` per analytics
- [ ] **Bundle size**
  - Monitorare con `@next/bundle-analyzer` (devDependency)
  - Target JS gzipped < 150KB
  - Evitare import interi di librerie (es. `lucide-react` import singoli, non `*`)

---

## VPS Deploy & CI/CD

- [x] **Configurare VPS** — Ubuntu 22.04, Docker, docker-compose
- [x] **Impostare GitHub Actions** — SSH deploy su push a `main`
- [x] **Configurare GitHub Secrets**
  - `SSH_HOST`, `SSH_USERNAME`, `SSH_PASSWORD`, `SSH_PORT`
  - `GH_PAT` (per autenticazione git clone privato)
  - `RESEND_API_KEY`
  - `FOUNDER_EMAIL_1`, `FOUNDER_EMAIL_2`
- [ ] **Configurare dominio custom** `sycren.com` (Cloudflare → VPS)
- [x] **SSL/TLS** tramite Cloudflare Full Strict
- [ ] **Abilitare deploy automatico** per ogni push su `main`

---

## Verification & QA

- [ ] **Tutte le 9 sezioni** implementate e funzionanti
  - Navigazione tra sezioni via navbar links
  - Smooth scroll funzionante
  - Contenuto corretto in ogni sezione
- [ ] **Waitlist form**
  - Invio email di conferma all'utente entro 60 secondi
  - Invio notifica ai founder entro 60 secondi
  - Honeypot: bot che riempiono `website` vengono silenziosamente ignorati
  - Validazione: email non valida → errore inline
  - Campo vuoto → errore "Email is required"
- [ ] **Tema toggle**
  - Dark ☾ / Light ☀ / Black ● funzionanti
  - Persistenza in localStorage (chiudi e riapri il browser → tema preserved)
  - `prefers-color-scheme` rispettato al primo accesso senza localStorage
  - Contrasto WCAG AA verificato per tutti e 3 i temi
- [ ] **Navbar**
  - Sticky: rimane visibile durante scroll
  - Scroll blur: trasparente sopra hero, sfondo con blur dopo 80px
  - Transizione fluida
- [ ] **Mobile responsive**
  - Menu hamburger funzionante a 375px viewport
  - Tutte le sezioni responsive (nessun overflow orizzontale)
  - Touch target ≥ 44px per elementi interattivi
- [ ] **Performance**
  - Lighthouse Performance ≥ 90 su mobile (emulato Moto G4, 3G Slow)
  - Lighthouse Accessibility ≥ 90 su mobile
  - LCP < 2.0s (verificato con WebPageTest o Lighthouse)
  - CLS < 0.05
  - Bundle JS < 150KB gzipped (`next build` + analisi bundle)
- [ ] **Deploy**
  - `sycren.com` attivo e raggiungibile
  - SSL valido (certificato Cloudflare)
  - Analytics attivo (es. Plausible o Cloudflare Web Analytics)
  - Nessun errore 500/404 in produzione
  - Redirect HTTP → HTTPS funzionante
- [ ] **Sicurezza**
  - Nessuna API key o secret nel repository (controllare `.gitignore`, history)
  - `.env` in `.gitignore`
  - No console.log in produzione
  - Honeypot anti-spam attivo
  - Rate limiting su endpoint API (opzionale)

---

## Post-Deploy

- [ ] **Testare su dispositivi reali** (iPhone, Android, tablet, desktop)
- [ ] **Inviare link a beta tester** (amici, developer community)
- [ ] **Raccogliere feedback** su UX e contenuti
- [ ] **Pubblicare su LinkedIn** profilo aziendale Sycren
- [ ] **Pubblicare su X/Twitter** con menzione a community dev
- [ ] **Aggiungere a Discord Sycren** canale #announcements
- [ ] **Iniziare raccolta contatti** — primo lead ricevuto via waitlist
- [ ] **Monitorare analytics** prime 48 ore: visite, click, conversioni form
- [ ] **Ottimizzare basandosi sui dati** (se bounce rate alto, rivedere hero/CTA)

---

## Cosa Segue (dopo la landing)

Completata la landing, l'ordine di sviluppo della piattaforma Sycren:

| # | Modulo | Età 🕐 | Responsabile |
|---|---|---|---|
| 1 | ✅ Landing Page `sycren.com` | **16** | Angelo (lead), Cosimo (lead) |
| 2 | Database Foundation `sycren-db` | **16** | Angelo (lead) |
| 3 | Client Portal `studio.sycren.com` | **16** | Angelo (lead), Cosimo (lead) |
| 4 | Hub Centrale / SSO `app.sycren.com` | **16-17** | Angelo (lead), Cosimo (supporto) |
| 5 | Cloud Lite `cloud.sycren.com` | **17** | Angelo (lead) |
| 6 | Domains `domains.sycren.com` | **17-18** | — |
| 7 | Security `security.sycren.com` | **18+** | — |
| 🎂 | Lancio Commerciale (P.IVA, Vendite) | **18** | Entrambi |

---

## Note Finali

- Il progetto è in **Fase 0 (WAITLIST)** — nessuna vendita attiva, solo raccolta contatti
- Al compimento dei 18 anni 🎂, basterà cambiare `NEXT_PUBLIC_LAUNCH_MODE` da `WAITLIST` a `LIVE` e fare un redeploy via GitHub Actions per attivare la modalità commerciale
- Tutto il codice deve essere pulito, tipizzato (TypeScript strict), e accessibile (WCAG AA)
- Performance è un requisito, non un optional — Landing page deve caricare in < 2s su mobile 3G
- Ogni commit su `main` triggera deploy automatico via GitHub Actions — verificare sempre il corretto funzionamento prima di mergiare

---

*Sycren Landing — TODO v2.1 · Giugno 2026 · Fase 0 (WAITLIST) · Allineato a Bootstrap Roadmap v1.3*
