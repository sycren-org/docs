# sycren-design-system — TODO
> PRD v1.0 · Giugno 2026 · Allineato a Bootstrap Roadmap v1.3

---

## Contesto (dalla Roadmap)

- Il design system è un **progetto abilitante** per tutti i moduli Sycren: `sycren-landing`, `sycren-app`, `sycren-studio`, `sycren-cloud`, `sycren-domains`, `sycren-security`
- Sviluppato **in parallelo** alla landing page — i componenti P0 servono subito per la landing
- Repository su `github.com/sycren/sycren-design-system` (privato)
- Pubblicato come pacchetto npm privato su **GitHub Packages** (`@sycren/design-system`)
- Ogni applicazione installa il pacchetto come dipendenza — **nessun code sharing via git submodule o copia**
- Stima temporale: componenti P0 pronti entro Settimana 1–2 (parallelo alla landing), package completo entro Mese 2–3

**Principio guida:** Costruisci una volta, usa ovunque. Se un pattern UI appare in due repository diversi, deve vivere qui.

---

## Phase 1: Foundation (P0 — Età 16)

### 1.1 Repository Setup

- [ ] Inizializzare `sycren-design-system` con TypeScript strict mode
- [ ] Configurare `tsconfig.json` con path alias (`@/` → `./src/`)
- [ ] Inizializzare `package.json` con name `@sycren/design-system`, versione privata
- [ ] Installare dipendenze: React 18, Tailwind CSS 3.4+, Lucide React 0.400+
- [ ] Installare dev dependencies: TypeScript 5+, Storybook 8, Vitest, Testing Library
- [ ] Configurare `tsup` per build ESM + CJS + types
- [ ] Creare `.npmrc` per GitHub Packages authentication
- [ ] Configurare ESLint + Prettier con regole progetto
- [ ] Impostare `postcss.config.js` per Tailwind
- [ ] Creare struttura cartelle (`src/tokens`, `src/components/`, `src/hooks/`, `src/utils/`)

### 1.2 Design Tokens

- [ ] Definire `src/tokens/colors.css` — Dark theme (default)
- [ ] Aggiungere `[data-theme="light"]` tokens in colors.css
- [ ] Aggiungere `[data-theme="black"]` tokens in colors.css
- [ ] Definire `src/tokens/typography.css` — font-family, font-size scale, line-height, font-weight
- [ ] Definire `src/tokens/spacing.css` — spacing scale 0–24
- [ ] Definire `src/tokens/radius.css` — radius scale (none, sm, md, lg, xl, 2xl, full)
- [ ] Definire `src/tokens/shadows.css` — shadow scale per tema Dark
- [ ] Aggiungere shadow variant per tema Light
- [ ] Aggiungere shadow variant per tema Black
- [ ] Definire `src/tokens/transitions.css` — fast, base, slow, spring
- [ ] Creare `src/tokens/index.css` che importa tutti i token CSS
- [ ] Verificare contrasto WCAG AA per Dark/Light/Black su tutte le combinazioni colore

### 1.3 Tailwind Configuration

- [ ] Creare `tailwind.config.ts` esteso con custom colors da CSS variables
- [ ] Mappare bg-primary, bg-secondary, bg-tertiary, bg-card, bg-elevated
- [ ] Mappare text-primary, text-secondary, text-tertiary, text-inverse
- [ ] Mappare accent, accent-hover, accent-soft, accent-border
- [ ] Mappare division colori (studios, cloud, domains, security)
- [ ] Mappare fontFamily sans (Inter) e mono (Geist Mono)
- [ ] Mappare radius, shadows, transitions come utilities estese
- [ ] Configurare `darkMode: ['selector', '[data-theme="dark"]']`
- [ ] Verificare che Tailwind classi funzionino con i CSS custom properties

### 1.4 Storybook Setup

- [ ] Inizializzare Storybook con `npx storybook@latest init`
- [ ] Configurare `.storybook/main.ts` — stories path, addon essentials
- [ ] Configurare `.storybook/preview.ts` — import tokens CSS, decorator theme provider
- [ ] Installare addon-a11y per accessibility panel
- [ ] Installare addon-interactions per testing interattivo
- [ ] Installare addon-themes per theme switching in Storybook UI
- [ ] Creare decorator globale ThemeDecorator che supporta Dark/Light/Black
- [ ] Deployare Storybook su GitHub Pages o Vercel
- [ ] Verificare build Storybook in CI

### 1.5 Utility Functions

- [ ] Creare `src/utils/cn.ts` con `clsx` + `tailwind-merge` per class merging
- [ ] Creare `src/hooks/useTheme.ts` — set/get theme, localStorage persistence, prefers-color-scheme fallback
- [ ] Creare `src/hooks/useToast.ts` — toast queue management

### 1.6 Component: Button

- [ ] Creare `src/components/Button/Button.tsx`
- [ ] Varianti: `primary`, `secondary`, `ghost`, `danger`, `link`
- [ ] Size: `sm` (32px), `md` (40px), `lg` (48px)
- [ ] Stato loading con spinner SVG inline
- [ ] Stato disabled con stile attenuato e `aria-disabled`
- [ ] Stato active con stile pressed
- [ ] Focus ring visibile (2px accent, 2px offset)
- [ ] Icon support (sinistra/destra/dash icona singola)
- [ ] Supporto `asChild` (polymorphic via Radix Slot)
- [ ] Aggiungere default export e named export
- [ ] Scrivere `Button.stories.ts` con tutti gli stati e varianti
- [ ] Scrivere `Button.test.tsx` (render, variants, click, disabled, loading)

### 1.7 Component: Badge

- [ ] Creare `src/components/Badge/Badge.tsx`
- [ ] Varianti: `studios`, `cloud`, `domains`, `security`, `neutral`, `success`, `warning`, `error`
- [ ] Variante outline con bordo colorato, fondo trasparente
- [ ] Size: `sm`, `md`
- [ ] Icona opzionale a sinistra
- [ ] Versione closeable (badge removibile con X button)
- [ ] Scrivere `Badge.stories.ts`
- [ ] Scrivere `Badge.test.tsx`

### 1.8 Component: Card

- [ ] Creare `src/components/Card/Card.tsx`
- [ ] Varianti: `default`, `elevated`, `interactive`, `highlighted`
- [ ] Slot: `<Card.Header>`, `<Card.Content>`, `<Card.Footer>` (compound components)
- [ ] Interactive: hover state con border/glow transition
- [ ] Disabled state per interactive (cursor not-allowed, opacità ridotta)
- [ ] Scrivere `Card.stories.ts`
- [ ] Scrivere `Card.test.tsx`

---

## Phase 2: Core Components (P1 — Età 16–17)

### 2.1 Component: Input

- [ ] Creare `src/components/Input/Input.tsx`
- [ ] Single-line (type text, email, password, url, tel, number)
- [ ] Textarea multi-riga (con `rows` prop e resize controllato)
- [ ] Size: `sm`, `md`, `lg`
- [ ] Stati: default, hover, focus, error, disabled, readonly
- [ ] Loading state con skeleton shimmer al posto del valore
- [ ] Label associata con `htmlFor`
- [ ] Helper text sotto (grigio)
- [ ] Error message sotto (rosso) con `aria-invalid`
- [ ] Icone sinistra/destra
- [ ] `aria-describedby` collegato a helper/error
- [ ] Scrivere `Input.stories.ts`
- [ ] Scrivere `Input.test.tsx`

### 2.2 Component: Select / Dropdown

- [ ] Creare `src/components/Select/Select.tsx`
- [ ] Native `<select>` version con stile coerente agli altri input
- [ ] Custom dropdown con option list stile menu
- [ ] Size: `sm`, `md`, `lg`
- [ ] Stati: default, hover, focus, error, disabled, open
- [ ] Opzioni con icona opzionale
- [ ] Ricerca interna (custom only)
- [ ] `aria-expanded`, `aria-activedescendant`, `role="listbox"`, `aria-selected`
- [ ] Navigazione frecce Up/Down, Enter per selezionare, Escape per chiudere
- [ ] Scrivere `Select.stories.ts`
- [ ] Scrivere `Select.test.tsx`

### 2.3 Component: Modal / Dialog

- [ ] Creare `src/components/Modal/Modal.tsx`
- [ ] Varianti: `default` (centrato), `fullscreen` (mobile)
- [ ] Size: `sm` (400px), `md` (560px), `lg` (720px), `xl` (960px)
- [ ] Overlay con click per chiudere (configurabile)
- [ ] Header con titolo e close button
- [ ] Content scrollabile
- [ ] Footer con azioni (Button primary + ghost)
- [ ] Focus trap (Tab cycle dentro il modal)
- [ ] Chiusura con Escape
- [ `aria-modal="true"`, `role="dialog"`, `aria-labelledby` collegato al titolo
- [ ] Blocco scroll body
- [ ] Animazione entrata (fade + scale) e uscita
- [ ] Supporto `prefers-reduced-motion`
- [ ] Scrivere `Modal.stories.ts`
- [ ] Scrivere `Modal.test.tsx`

### 2.4 Component: Toast / Notification

- [ ] Creare `src/components/Toast/Toast.tsx`
- [ ] Creare `src/components/Toast/ToastContainer.tsx` (gestione posizione + coda)
- [ ] Varianti: `success`, `error`, `warning`, `info`
- [ ] Posizioni: `top-right`, `top-left`, `bottom-right`, `bottom-left`, `top-center`
- [ ] Durata configurabile (default: 5s info/success, persistente error)
- [ ] Coda massima 5 toast visibili
- [ ] Progress bar countdown opzionale
- [ ] Animazione slide entrata, fade uscita
- [ ] `role="alert"`, `aria-live="polite"/"assertive"`
- [ ] Dismiss manuale via close button o Escape
- [ ] Scrivere `Toast.stories.ts`
- [ ] Scrivere `Toast.test.tsx`

### 2.5 Component: Navigation

- [ ] Creare `src/components/Navigation/Sidebar.tsx`
- [ ] Creare `src/components/Navigation/TopNav.tsx`
- [ ] Creare `src/components/Navigation/MobileNav.tsx` (drawer laterale)
- [ ] Creare `src/components/Navigation/Breadcrumbs.tsx`
- [ ] Item: icona + label + badge notifiche opzionale
- [ ] Submenu accordion (sidebar)
- [ ] Stati item: `default`, `hover`, `active` (`aria-current="page"`), `disabled`
- [ ] Keyboard navigation: Arrow Up/Down, Enter, Home, End
- [ ] Role navigation, aria-label sulla nav
- [ ] Scrivere `Navigation.stories.ts`
- [ ] Scrivere `Navigation.test.tsx`

### 2.6 Component: Avatar

- [ ] Creare `src/components/Avatar/Avatar.tsx`
- [ ] Varianti: `image`, `initials`, `placeholder`
- [ ] Size: `xs` (24px), `sm` (32px), `md` (40px), `lg` (56px), `xl` (80px)
- [ ] Forma: `circle` (default), `square` (con radius)
- [ ] Fallback automatico: image → initials → placeholder icon
- [ ] Scrivere `Avatar.stories.ts`
- [ ] Scrivere `Avatar.test.tsx`

### 2.7 Component: Skeleton

- [ ] Creare `src/components/Skeleton/Skeleton.tsx`
- [ ] Varianti: `text`, `circle`, `rect`, `card` (composito)
- [ ] Animazione pulse shimmer con gradient direction configurabile
- [ ] Width/height configurabili (o predefiniti per variante)
- [ ] Scrivere `Skeleton.stories.ts`
- [ ] Scrivere `Skeleton.test.tsx`

### 2.8 Component: Tooltip

- [ ] Creare `src/components/Tooltip/Tooltip.tsx`
- [ ] Posizioni: `top`, `right`, `bottom`, `left`
- [ ] Trigger: `hover` (default), `focus`, `click`
- [ ] Delay configurabile: show 300ms, hide 100ms
- [ ] Rich content support (HTML/ReactNode, non solo testo)
- [ ] Freccia indicatrice
- [ ] `role="tooltip"`, `aria-describedby` collegato al trigger
- [ ] Animazione fade + slide
- [ ] Scrivere `Tooltip.stories.ts`
- [ ] Scrivere `Tooltip.test.tsx`

---

## Phase 3: Polish (P2 — Età 17)

### 3.1 Accessibility

- [ ] Verificare keyboard navigation su tutti i componenti interattivi
- [ ] Testare focus ring visibile su ogni elemento focusable
- [ ] Verificare contrasto WCAG AA (4.5:1 testo, 3:1 UI) per tutti e 3 i temi
- [ ] Scrivere test a11y con `@testing-library/jest-dom` (toHaveAccessibleName, toHaveAccessibleDescription)
- [ ] Testare screen reader (NVDA/VoiceOver) su componenti principali
- [ ] Verificare `prefers-reduced-motion` su tutti i componenti animati
- [ ] Aggiungere `prefers-contrast: more` support opzionale

### 3.2 Testing

- [ ] Scrivere unit test per ogni componente (render, varianti, stati, eventi)
- [ ] Scrivere test per useTheme hook
- [ ] Scrivere test per useToast hook
- [ ] Scrivere test per util function cn()
- [ ] Raggiungere ≥ 80% code coverage
- [ ] Configurare Vitest con jsdom environment
- [ ] Aggiungere test in CI (GitHub Actions)

### 3.3 Build & Publish

- [ ] Configurare `tsup` per build ESM (`src/index.ts` → `dist/esm/`)
- [ ] Configurare `tsup` per build CJS (`src/index.ts` → `dist/cjs/`)
- [ ] Generare file `.d.ts` per TypeScript
- [ ] Generare CSS bundle separato (tokens + component styles)
- [ ] Aggiungere `sideEffects: false` in package.json per tree-shaking
- [ ] Verificare che l'import per-componente funzioni (`import { Button } from '@sycren/design-system'`)
- [ ] Pubblicare su GitHub Packages con `npm publish`
- [ ] Testare installazione in `sycren-landing`
- [ ] Verificare funzionamento tema Dark/Light/Black in landing

### 3.4 CI/CD

- [ ] Creare `.github/workflows/test.yml` — lint + typecheck + test su PR
- [ ] Creare `.github/workflows/publish.yml` — build + publish su merge in main
- [ ] Aggiungere step di deploy Storybook su Vercel/GitHub Pages
- [ ] Configurare GitHub Actions secrets per GITHUB_TOKEN e NPM_TOKEN

---

## Integration Tasks

- [ ] Integrare `@sycren/design-system` in **`sycren-landing`** (P0 — Età 16)
- [ ] Integrare `@sycren/design-system` in **`sycren-studio`** (P1 — Età 16–17)
- [ ] Integrare `@sycren/design-system` in **`sycren-app`** (P1 — Età 16–17)
- [ ] Integrare `@sycren/design-system` in **`sycren-cloud`** (P2 — Età 17)
- [ ] Integrare `@sycren/design-system` in **`sycren-domains`** (P2 — Età 17–18)
- [ ] Integrare `@sycren/design-system` in **`sycren-security`** (P3 — Età 18+)

---

## Verification Criteria

- [ ] Button: 5 varianti × 3 size × 4 stati = 60 combinazioni funzionanti
- [ ] Badge: 8 varianti × 2 variant (solid/outline) × 2 size = 32 combinazioni
- [ ] Card: 4 varianti con tutti gli slot renderizzati correttamente
- [ ] Input: 2 varianti (text/textarea) × 3 size × 6 stati = 36 combinazioni
- [ ] Select: native + custom, keyboard navigation completa
- [ ] Modal: focus trap funzionante, Escape chiude, scroll bloccato
- [ ] Toast: coda gestita, auto-dismiss timer, posizioni multiple
- [ ] Navigation: sidebar scrollabile, submenu accordion, aria-current funzionante
- [ ] Avatar: fallback image → initials → placeholder funzionante
- [ ] Skeleton: animazione pulse visibile in tutti e 3 i temi
- [ ] Tooltip: posizioni corrette, delay rispettato, rich content renderizzato
- [ ] Temi: switching Dark/Light/Black funzionante con persistenza localStorage
- [ ] Accessibilità: Lighthouse a11y score ≥ 90 su ogni componente Storybook
- [ ] Tree-shaking: import singolo componente non include il bundle completo
- [ ] Package: `npm install @sycren/design-system` funziona con autenticazione GitHub Packages

---

## Età del Progetto

**🕐 16+** — Il design system viene avviato alla settimana 1 in parallelo alla landing page.
I componenti P0 (Button, Badge, Card, Input, Navigation, Toast, Tooltip) sono il prerequisito per la landing e devono essere pronti entro la fine della Settimana 2.
Il package completo con tutti i componenti e testing è target per il Mese 2–3 (parallelo a `sycren-studio` e `sycren-app`).

| Componenti | Target | Età |
|---|---|---|
| Tokens + Tailwind + Storybook setup | Settimana 1 | 16 |
| Button, Badge, Card, Input | Settimana 1–2 | 16 |
| Navigation, Toast, Tooltip | Settimana 2–3 | 16 |
| Select, Modal, Avatar, Skeleton | Mese 1–2 | 16–17 |
| Testing, a11y, CI/CD, publish | Mese 2–3 | 17 |
| Integrazione in tutti i moduli | Continuo | 16–18+ |

---

*Sycren — Design System TODO v1.0 · Giugno 2026 · Allineato a Bootstrap Roadmap v1.3*
