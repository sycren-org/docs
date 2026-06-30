# sycren-design-system — Project Requirements
> PRD v1.0 — Giugno 2026 — Uso interno — Allineato alla Bootstrap Roadmap v1.3

---

## 1. Contesto e Obiettivo

Sycren è una piattaforma unificata per sviluppo software, cloud hosting, gestione domini e cybersecurity. Ogni applicazione della piattaforma (`sycren-landing`, `sycren-app`, `sycren-studio`, `sycren-cloud`, `sycren-domains`, `sycren-security`) deve condividere lo stesso linguaggio visivo per garantire un'esperienza utente coerente attraverso tutti i touchpoint.

`sycren-design-system` è il pacchetto centralizzato che contiene:
- **Design tokens** (colori, tipografia, spaziature, raggio, ombre, transizioni)
- **UI Components** riutilizzabili (Button, Badge, Card, Input, Select, Modal, Toast, Navigation, Avatar, Skeleton, Tooltip)
- **Configurazione Tailwind** basata sui token
- **Storybook** interattivo per documentazione e sviluppo
- **Tema** Dark, Light e Black con switching via `data-theme` attribute

**Obiettivo primario:** Eliminare la duplicazione del codice UI tra i vari repository Sycren, garantendo che ogni componente sia sviluppato una volta sola, testato centralmente e consumato come dipendenza npm da tutte le applicazioni.

**Obiettivo secondario:** Accelerare lo sviluppo dei singoli moduli (landing, studio, app, cloud, domains, security) fornendo componenti pronti all'uso che rispettano le linee guida di accessibilità e design.

**Stato attuale (Giugno 2026):**
- [x] Organizzazione GitHub `sycren` creata con repository `sycren-design-system`
- [x] Repository privato su `github.com/sycren/sycren-design-system`
- [ ] Design tokens definiti
- [ ] Componenti sviluppati in Storybook
- [ ] Pacchetto npm pubblicato
- [ ] Integrato con `sycren-landing`

---

## 2. Design Tokens

Tutti i token sono definiti come CSS custom properties e mappati nella configurazione Tailwind.

### 2.1 Colori — Tema Dark (Default)

```css
:root,
[data-theme="dark"] {
  /* Sfondo */
  --color-bg-primary: #0A0A0F;
  --color-bg-secondary: #12121A;
  --color-bg-tertiary: #1A1A26;
  --color-bg-card: #14141F;
  --color-bg-elevated: #1E1E2E;

  /* Testo */
  --color-text-primary: #F0F0FF;
  --color-text-secondary: #A0A0B8;
  --color-text-tertiary: #6B6B80;
  --color-text-inverse: #0A0A0F;

  /* Accent */
  --color-accent: #6366F1;
  --color-accent-hover: #5558E6;
  --color-accent-soft: rgba(99, 102, 241, 0.12);
  --color-accent-border: rgba(99, 102, 241, 0.3);

  /* Division colors */
  --color-studios: #6366F1;
  --color-cloud: #38BDF8;
  --color-domains: #34D399;
  --color-security: #FBBF24;

  /* Semantic */
  --color-success: #22C55E;
  --color-warning: #F59E0B;
  --color-error: #EF4444;
  --color-info: #3B82F6;

  /* Borders */
  --color-border: rgba(255, 255, 255, 0.08);
  --color-border-hover: rgba(255, 255, 255, 0.15);
  --color-border-accent: rgba(99, 102, 241, 0.4);

  /* Overlay */
  --color-overlay: rgba(0, 0, 0, 0.6);
}
```

### 2.2 Colori — Tema Light

```css
[data-theme="light"] {
  --color-bg-primary: #FAFAFA;
  --color-bg-secondary: #F4F4F6;
  --color-bg-tertiary: #ECECF0;
  --color-bg-card: #FFFFFF;
  --color-bg-elevated: #FFFFFF;

  --color-text-primary: #0F0F1A;
  --color-text-secondary: #52525B;
  --color-text-tertiary: #A1A1AA;
  --color-text-inverse: #FFFFFF;

  --color-accent: #4F46E5;
  --color-accent-hover: #4338CA;
  --color-accent-soft: rgba(79, 70, 229, 0.1);
  --color-accent-border: rgba(79, 70, 229, 0.25);

  --color-studios: #4F46E5;
  --color-cloud: #0284C7;
  --color-domains: #059669;
  --color-security: #D97706;

  --color-border: rgba(0, 0, 0, 0.08);
  --color-border-hover: rgba(0, 0, 0, 0.15);
  --color-border-accent: rgba(79, 70, 229, 0.3);

  --color-overlay: rgba(0, 0, 0, 0.4);
}
```

### 2.3 Colori — Tema Black

```css
[data-theme="black"] {
  --color-bg-primary: #000000;
  --color-bg-secondary: #080808;
  --color-bg-tertiary: #111111;
  --color-bg-card: #0A0A0A;
  --color-bg-elevated: #141414;

  --color-text-primary: #FFFFFF;
  --color-text-secondary: #A0A0A0;
  --color-text-tertiary: #666666;
  --color-text-inverse: #000000;

  --color-accent: #FFFFFF;
  --color-accent-hover: #E0E0E0;
  --color-accent-soft: rgba(255, 255, 255, 0.08);
  --color-accent-border: rgba(255, 255, 255, 0.2);

  --color-studios: #FFFFFF;
  --color-cloud: #CCCCCC;
  --color-domains: #AAAAAA;
  --color-security: #888888;

  --color-border: rgba(255, 255, 255, 0.1);
  --color-border-hover: rgba(255, 255, 255, 0.2);
  --color-border-accent: rgba(255, 255, 255, 0.3);

  --color-overlay: rgba(0, 0, 0, 0.8);
}
```

### 2.4 Tipografia

| Token | Valore | Utilizzo |
|---|---|---|
| `--font-sans` | `'Inter', sans-serif` | Testo corpo, UI, navigation |
| `--font-mono` | `'Geist Mono', monospace` | Codice, terminal block, numeri |
| `--text-xs` | `0.75rem` (12px) | Label, caption |
| `--text-sm` | `0.875rem` (14px) | Badge, subtitle piccolo |
| `--text-base` | `1rem` (16px) | Corpo testo |
| `--text-lg` | `1.125rem` (18px) | Sottotitoli |
| `--text-xl` | `1.25rem` (20px) | Titoli sezione secondari |
| `--text-2xl` | `1.5rem` (24px) | Titoli sezione |
| `--text-3xl` | `1.875rem` (30px) | Titoli pagina |
| `--text-4xl` | `2.25rem` (36px) | Hero, headline |
| `--text-5xl` | `3rem` (48px) | Hero grande (desktop) |
| `--leading-tight` | `1.1` | Titoli |
| `--leading-normal` | `1.5` | Corpo |
| `--leading-relaxed` | `1.625` | Testo lungo |
| `--font-weight-normal` | `400` | |
| `--font-weight-medium` | `500` | |
| `--font-weight-semibold` | `600` | |
| `--font-weight-bold` | `700` | |

### 2.5 Spacing

Scala basata su multipli di 4px (Tailwind default esteso):

| Token | Valore | Alias |
|---|---|---|
| `--space-0` | `0px` | |
| `--space-1` | `0.25rem` (4px) | |
| `--space-2` | `0.5rem` (8px) | |
| `--space-3` | `0.75rem` (12px) | |
| `--space-4` | `1rem` (16px) | |
| `--space-5` | `1.25rem` (20px) | |
| `--space-6` | `1.5rem` (24px) | |
| `--space-8` | `2rem` (32px) | |
| `--space-10` | `2.5rem` (40px) | |
| `--space-12` | `3rem` (48px) | |
| `--space-16` | `4rem` (64px) | |
| `--space-20` | `5rem` (80px) | |
| `--space-24` | `6rem` (96px) | |

### 2.6 Border Radius

| Token | Valore |
|---|---|
| `--radius-none` | `0px` |
| `--radius-sm` | `4px` |
| `--radius-md` | `8px` |
| `--radius-lg` | `12px` |
| `--radius-xl` | `16px` |
| `--radius-2xl` | `24px` |
| `--radius-full` | `9999px` |

### 2.7 Shadows

| Token | Dark | Light | Black |
|---|---|---|---|
| `--shadow-sm` | `0 1px 2px rgba(0,0,0,0.4)` | `0 1px 2px rgba(0,0,0,0.05)` | `0 1px 2px rgba(0,0,0,0.6)` |
| `--shadow-md` | `0 4px 12px rgba(0,0,0,0.5)` | `0 4px 12px rgba(0,0,0,0.08)` | `0 4px 12px rgba(0,0,0,0.7)` |
| `--shadow-lg` | `0 8px 24px rgba(0,0,0,0.6)` | `0 8px 24px rgba(0,0,0,0.12)` | `0 8px 24px rgba(0,0,0,0.8)` |
| `--shadow-xl` | `0 12px 48px rgba(0,0,0,0.7)` | `0 12px 48px rgba(0,0,0,0.15)` | `0 12px 48px rgba(0,0,0,0.9)` |
| `--shadow-glow-accent` | `0 0 20px rgba(99,102,241,0.3)` | `0 0 20px rgba(79,70,229,0.15)` | `0 0 20px rgba(255,255,255,0.1)` |

### 2.8 Transitions

| Token | Valore |
|---|---|
| `--transition-fast` | `150ms cubic-bezier(0.4, 0, 0.2, 1)` |
| `--transition-base` | `250ms cubic-bezier(0.4, 0, 0.2, 1)` |
| `--transition-slow` | `400ms cubic-bezier(0.4, 0, 0.2, 1)` |
| `--transition-spring` | `500ms cubic-bezier(0.34, 1.56, 0.64, 1)` |

---

## 3. Component Catalog

### 3.1 Button

| Variant | Utilizzo |
|---|---|
| `primary` | Azione principale (fondo accent, testo bianco) |
| `secondary` | Azione secondaria (bordo accent, trasparente) |
| `ghost` | Azione terziaria (solo testo, nessun bordo) |
| `danger` | Azione distruttiva (rosso) |
| `link` | Sembra un link ma è un `<button>` |

**Size:** `sm` (32px), `md` (40px), `lg` (48px)

**Stati:** default, hover, active, disabled, loading (spinner interno)

**Accessibilità:** focus ring visibile, ruolo `button`, supporto `aria-disabled`, `aria-busy` per loading

### 3.2 Badge

| Variant | Utilizzo |
|---|---|
| `studios` | Indigo |
| `cloud` | Azzurro |
| `domains` | Verde |
| `security` | Ambra |
| `neutral` | Grigio generico |
| `success` | Verde semantico |
| `warning` | Giallo |
| `error` | Rosso |

**Size:** `sm`, `md`

**Stato:** `default` | `outline` (bordo, fondo trasparente)

**Contenuto:** icona opzionale a sinistra, testo, close button opzionale per badge rimovibili

### 3.3 Card

| Variant | Utilizzo |
|---|---|
| `default` | Bordo sottile, padding standard |
| `elevated` | Ombra md, sfondo più chiaro |
| `interactive` | Hover con bordo accent, cursore pointer |
| `highlighted` | Bordo accent sempre visibile, glow |

**Slot:** `header`, `content`, `footer`

**Stati:** `default`, `hover` (interactive only), `disabled`

### 3.4 Input

| Variant | Utilizzo |
|---|---|
| `default` | Testo singola riga |
| `textarea` | Testo multi-riga |

**Size:** `sm`, `md`, `lg`

**Stati:** default, hover, focus, error, disabled, readonly, loading (skeleton pulsing)

**Elementi:** label opzionale sopra, helper text sotto, error message sotto (rosso), icona sinistra/destra

**Accessibilità:** `aria-invalid` su errore, `aria-describedby` collegato a helper/error, label associata via `htmlFor`

### 3.5 Select / Dropdown

| Variant | Utilizzo |
|---|---|
| `default` | `<select>` nativo stilizzato |
| `custom` | Dropdown custom con option filter |

**Size:** `sm`, `md`, `lg`

**Stati:** default, hover, focus, error, disabled, open (menu visibile)

**Elementi:** label, placeholder, opzioni con icona opzionale, ricerca interna (custom only)

**Accessibilità:** `aria-expanded`, `aria-activedescendant`, navigazione frecce, `aria-selected`

### 3.6 Modal / Dialog

| Variant | Utilizzo |
|---|---|
| `default` | Dialog centrato con backdrop |
| `fullscreen` | Schermo intero (mobile) |

**Size:** `sm` (400px), `md` (560px), `lg` (720px), `xl` (960px)

**Elementi:** overlay con chiusura click, header con titolo + close button, content scrollabile, footer con azioni

**Stati:** closed, opening (animazione entrata), open, closing (animazione uscita)

**Comportamento:** focus trap interno, chiusura con Escape, `aria-modal="true"`, `role="dialog"`, `aria-labelledby` collegato al titolo, blocco scroll body

### 3.7 Toast / Notification

| Variant | Utilizzo |
|---|---|
| `success` | Operazione completata |
| `error` | Errore |
| `warning` | Attenzione |
| `info` | Informazione generica |

**Posizioni:** `top-right`, `top-left`, `bottom-right`, `bottom-left`, `top-center`

**Durata:** auto-dismiss dopo 5s (info/success), persistente per error

**Elementi:** icona, messaggio, close button, progress bar opzionale

**Comportamento:** coda toast, max 5 visibili, animazione entrata slide, animazione uscita fade

### 3.8 Navigation

| Componente | Utilizzo |
|---|---|
| `Sidebar` | Navigazione verticale laterale (app, studio, cloud) |
| `TopNav` | Navigazione orizzontale superiore (landing) |
| `MobileNav` | Drawer laterale per mobile |
| `Breadcrumbs` | Navigazione gerarchica |

**Stati per item:** `default`, `hover`, `active` (pagina corrente), `disabled`

**Elementi:** icona + label, badge notifiche (optional), submenu (accordion)

**Accessibilità:** `role="navigation"`, `aria-current="page"`, keyboard arrow navigation per sidebar

### 3.9 Avatar

| Variant | Utilizzo |
|---|---|
| `image` | Foto profilo |
| `initials` | Iniziali (fallback) |
| `placeholder` | Icona generica |

**Size:** `xs` (24px), `sm` (32px), `md` (40px), `lg` (56px), `xl` (80px)

**Forma:** `circle` (default), `square` (con radius)

### 3.10 Skeleton

| Variant | Utilizzo |
|---|---|
| `text` | Riga singola / multi-riga |
| `circle` | Placeholder per avatar |
| `rect` | Placeholder per card/immagine |
| `card` | Placeholder composito per card |

**Animazione:** pulse shimmer con gradient

### 3.11 Tooltip

| Variant | Utilizzo |
|---|---|
| `default` | Testo semplice |
| `rich` | Testo con icona / contenuto HTML |

**Posizioni:** `top`, `right`, `bottom`, `left`

**Trigger:** `hover`, `focus`, `click`

**Delay:** 300ms show, 100ms hide

**Accessibilità:** `role="tooltip"`, `aria-describedby` sul trigger

### 3.12 Icons

Libreria **Lucide React** — tutti i componenti esportano icone come props.
Il design system non wrappa Lucide ma lo re-esporta come dipendenza peer per garantire versioni consistenti.

---

## 4. Theme Architecture

### 4.1 CSS Custom Properties

Tutti i token sono definiti come CSS custom properties sul `:root` e sui selettori `[data-theme="dark"]`, `[data-theme="light"]`, `[data-theme="black"]`.

```css
:root,
[data-theme="dark"] { /* token dark */ }
[data-theme="light"] { /* token light */ }
[data-theme="black"] { /* token black */ }
```

### 4.2 Data Attribute Switching

Il tema viene applicato impostando l'attributo `data-theme` sull'elemento `<html>`:

```ts
document.documentElement.setAttribute('data-theme', 'dark');
// Oppure: 'light', 'black'
```

### 4.3 localStorage Persistence

La scelta del tema viene persistita in `localStorage`:

```ts
const stored = localStorage.getItem('sycren-theme'); // 'dark' | 'light' | 'black'
if (stored) {
  document.documentElement.setAttribute('data-theme', stored);
}
```

### 4.4 prefers-color-scheme (First Visit)

Alla prima visita (nessun tema salvato in localStorage), si rispetta `prefers-color-scheme`:

```ts
const prefersDark = window.matchMedia('(prefers-color-scheme: dark)').matches;
const theme = prefersDark ? 'dark' : 'light';
// 'black' mai come default automatico — è una scelta esplicita
```

### 4.5 Tailwind Integration

```ts
// tailwind.config.ts
const config: Config = {
  darkMode: ['selector', '[data-theme="dark"]'],
  theme: {
    extend: {
      colors: {
        bg: {
          primary: 'var(--color-bg-primary)',
          secondary: 'var(--color-bg-secondary)',
          tertiary: 'var(--color-bg-tertiary)',
          card: 'var(--color-bg-card)',
          elevated: 'var(--color-bg-elevated)',
        },
        text: {
          primary: 'var(--color-text-primary)',
          secondary: 'var(--color-text-secondary)',
          tertiary: 'var(--color-text-tertiary)',
          inverse: 'var(--color-text-inverse)',
        },
        accent: {
          DEFAULT: 'var(--color-accent)',
          hover: 'var(--color-accent-hover)',
          soft: 'var(--color-accent-soft)',
          border: 'var(--color-accent-border)',
        },
        division: {
          studios: 'var(--color-studios)',
          cloud: 'var(--color-cloud)',
          domains: 'var(--color-domains)',
          security: 'var(--color-security)',
        },
      },
      fontFamily: {
        sans: ['Inter', 'sans-serif'],
        mono: ['Geist Mono', 'monospace'],
      },
    },
  },
};
```

---

## 5. Technical Stack

| Layer | Tecnologia | Versione Min |
|---|---|---|
| Framework UI | React | 18.x |
| Language | TypeScript | 5.x (strict) |
| Styling | Tailwind CSS | 3.4.x |
| Component Documentation | Storybook | 8.x |
| Icons | Lucide React | 0.400.x |
| Build | tsup / tsc | Latest |
| Package Registry | GitHub Packages (npm) | — |
| Testing | Vitest + Testing Library | Latest |
| CI/CD | GitHub Actions | — |

### 5.1 Package Structure

```
sycren-design-system/
├── src/
│   ├── tokens/
│   │   ├── colors.css
│   │   ├── typography.css
│   │   ├── spacing.css
│   │   └── index.css
│   ├── components/
│   │   ├── Button/
│   │   │   ├── Button.tsx
│   │   │   ├── Button.stories.ts
│   │   │   ├── Button.test.tsx
│   │   │   └── index.ts
│   │   ├── Badge/
│   │   ├── Card/
│   │   ├── Input/
│   │   ├── Select/
│   │   ├── Modal/
│   │   ├── Toast/
│   │   ├── Navigation/
│   │   ├── Avatar/
│   │   ├── Skeleton/
│   │   └── Tooltip/
│   ├── hooks/
│   │   ├── useTheme.ts
│   │   └── useToast.ts
│   ├── utils/
│   │   └── cn.ts (clsx + tailwind-merge)
│   └── index.ts
├── tailwind.config.ts
├── postcss.config.js
├── tsconfig.json
├── package.json
├── .storybook/
│   ├── main.ts
│   └── preview.ts
└── README.md
```

### 5.2 Peer Dependencies

```json
{
  "peerDependencies": {
    "react": "^18.0.0",
    "react-dom": "^18.0.0",
    "tailwindcss": "^3.4.0",
    "lucide-react": "^0.400.0"
  }
}
```

---

## 6. Accessibility Requirements

### 6.1 WCAG AA Compliance

Tutti i componenti devono rispettare i seguenti criteri WCAG 2.2 AA:

| Criterio | Descrizione |
|---|---|
| 1.1.1 | Testo alternativo per elementi non testuali |
| 1.4.3 | Contrasto minimo 4.5:1 per testo normale |
| 1.4.11 | Contrasto 3:1 per componenti UI |
| 2.1.1 | Tutte le funzionalità da tastiera |
| 2.4.3 | Focus order logico |
| 2.4.7 | Focus ring visibile |
| 3.3.2 | Label o istruzioni per input |
| 4.1.2 | Nome, ruolo, valore per componenti custom |

### 6.2 Keyboard Navigation

| Componente | Tasti Supportati |
|---|---|
| Button | Enter / Space |
| Select | Enter (open), Arrow Up/Down (navigate), Enter (select), Escape (close) |
| Modal | Escape (close), Tab (focus trap) |
| Toast | Escape (dismiss) |
| Navigation (sidebar) | Arrow Up/Down, Enter, Home, End |
| Tooltip | Escape (close) |
| Badge (closeable) | Backspace / Delete |

### 6.3 Focus Ring

- Deve essere visibile su tutti gli elementi interattivi
- Spessore: `2px`
- Offset: `2px`
- Colore: `--color-accent`
- Mai rimosso con `outline: none` senza sostituzione

### 6.4 ARIA Attributes

| Componente | Attributi Richiesti |
|---|---|
| Button | `aria-disabled` (se disabled), `aria-busy` (se loading) |
| Modal | `role="dialog"`, `aria-modal="true"`, `aria-labelledby` |
| Tooltip | `role="tooltip"`, `aria-describedby` sul trigger |
| Select | `aria-expanded`, `aria-activedescendant`, `role="listbox"` |
| Toast | `role="alert"`, `aria-live="polite"` (o "assertive" per error) |
| Navigation | `role="navigation"`, `aria-current="page"` |
| Input (error) | `aria-invalid="true"`, `aria-describedby` |

### 6.5 Reduced Motion

Tutti i componenti devono rispettare `prefers-reduced-motion`:

```css
@media (prefers-reduced-motion: reduce) {
  * {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

---

## 7. Integration Map

Il design system viene consumato dai seguenti repository Sycren:

| Repository | Sezioni/Uso |
|---|---|
| `sycren-landing` | Hero, Navbar, Division Cards, Pricing Cards, Form, Team, Footer |
| `sycren-app` | Hub Centrale, SSO, Dashboard, Notifiche |
| `sycren-studio` | Client Portal, Progetti, Preventivi, Comunicazione |
| `sycren-cloud` | VPS Dashboard, Deploy, Monitoring |
| `sycren-domains` | DNS Panel, Search, Registrar |
| `sycren-security` | WAF Dashboard, Report, Monitoring |

Ogni repository include `sycren-design-system` come dipendenza:

```bash
npm install @sycren/design-system
```

---

## 8. Completion Criteria

- [ ] Design tokens definiti e documentati per Dark, Light, Black
- [ ] Tailwind config completo basato su CSS custom properties
- [ ] Button, Badge, Card: implementati con tutte le varianti
- [ ] Input, Select, Modal, Toast: implementati con stati e accessibilità
- [ ] Navigation, Avatar, Skeleton, Tooltip: implementati
- [ ] Storybook deployato con documentazione interattiva per ogni componente
- [ ] Test unitari per tutti i componenti (Vitest + Testing Library)
- [ ] Keyboard navigation per tutti i componenti interattivi
- [ ] WCAG AA verificato per tutti i componenti
- [ ] Pacchetto npm pubblicato su GitHub Packages (`@sycren/design-system`)
- [ ] CI/CD configurato (test → build → publish)
- [ ] Integrato e verificato con `sycren-landing`
- [ ] Tree-shakeable (import di singoli componenti senza bundle completo)
- [ ] Documentazione README con esempi di utilizzo per ogni componente

---

## 9. Età e Priorità

**Età del progetto: 🕐 16+** (costruito in parallelo alla landing page, raffinato continuamente)

Il design system viene sviluppato **in parallelo** agli altri moduli, non come prerequisito. I componenti necessari per `sycren-landing` (Button, Badge, Card, Input, Navigation, Toast) hanno priorità massima. Il resto segue secondo la roadmap delle applicazioni che li consumano.

| Applicazione | Componenti Necessari | Priorità | Età |
|---|---|---|---|
| `sycren-landing` | Button, Badge, Card, Input, Navigation, Toast, Tooltip | **P0** | 16 |
| `sycren-studio` | Tutti | P1 | 16-17 |
| `sycren-app` | Tutti | P1 | 16-17 |
| `sycren-cloud` | Tutti | P2 | 17 |
| `sycren-domains` | Tutti | P2 | 17-18 |
| `sycren-security` | Tutti | P3 | 18+ |

---

## Related Notes

| Document | Description |
|---|---|
| [[design-system.README]] | Design system overview |
| [[design-system.todo]] | Implementation plan |
| [[landing.requirements\|sycren-landing requirements]] | Consumer — P0 components |
| [[app.requirements\|sycren-app requirements]] | Consumer — all components |
| [[studio.requirements\|sycren-studio requirements]] | Consumer — all components |
| [[cloud.requirements\|sycren-cloud requirements]] | Consumer — all components |
| [[domains.requirements\|sycren-domains requirements]] | Consumer — all components |
| [[security.requirements\|sycren-security requirements]] | Consumer — all components |
| [[foundation/ROADMAP\|Bootstrap Roadmap]] | Build order and timeline |

---

*Sycren — Design System PRD v1.0 — Giugno 2026*
*Allineato a Bootstrap Roadmap v1.3 — Documento interno. Non distribuire senza autorizzazione scritta.*
