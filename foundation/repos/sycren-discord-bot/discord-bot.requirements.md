# sycren-discord-bot — Project Requirements
> PRD v1.0 — Giugno 2026 — Uso interno — Allineato alla Bootstrap Roadmap v1.3

---

## 1. Contesto e Obiettivo

Sycren utilizza Discord come canale principale per la community di sviluppatori e potenziali clienti. Il bot automatizza moderazione, supporto e engagement del server, rendendo la community professionale e scalabile sin dalla Fase 0.

**Obiettivo primario:** Automatizzare la gestione del server Discord Sycren con AI, riducendo lo sforzo manuale di moderazione e supporto.

**Obiettivo secondario:** Raccogliere feedback, segnalazioni e lead attraverso il sistema di ticket AI, integrato con la pipeline clienti.

**Stato attuale:**
- [ ] Server Discord Sycren da creare
- [ ] Bot da sviluppare e deployare
- [ ] Integrazione AI da configurare (OpenAI API key)

---

## 2. Architettura del Bot

```
┌─────────────────────────────────────────────────────┐
│                   Discord Gateway                    │
│                                                      │
│  ┌──────────────────────────────────────────────┐   │
│  │          sycren-discord-bot (Node.js)         │   │
│  │                                                │   │
│  │  Slash Command Handler ───► /ticket            │   │
│  │                           /roadmap             │   │
│  │                           /invite              │   │
│  │                           /feedback            │   │
│  │                                                │   │
│  │  Event Handler ──────────► guildMemberAdd      │   │
│  │                           messageCreate        │   │
│  │                           interactionCreate    │   │
│  │                                                │   │
│  │  AI Module ──────────────► Content moderation  │   │
│  │                           Ticket classification│   │
│  │                           FAQ auto-reply       │   │
│  │                           Sentiment analysis   │   │
│  │                                                │   │
│  │  Database ───────────────► Supabase (analytics)│   │
│  │                           (opzionale Fase 0)   │   │
│  └──────────────────────────────────────────────┘   │
│                                                      │
└─────────────────────────────────────────────────────┘
```

---

## 3. Moduli Funzionali

### 3.1 Moderazione AI
| Feature | Descrizione | Priorità |
|---|---|---|
| Auto-mod filtro parole | Blocco automatico parole/violazioni configurabili | P0 |
| Content scanning AI | Scan messaggi con OpenAI per contenuti inappropriati | P0 |
| Timeout/mute automation | Applicazione automatica timeout su violazioni ripetute | P1 |
| Warn system | Sistema di avvertimenti con soglia → timeout/ban | P1 |
| Log moderation | Tutte le azioni di mod loggate in canale #mod-log | P0 |

### 3.2 Community & Onboarding
| Feature | Descrizione | Priorità |
|---|---|---|
| Welcome message | Messaggio embedded al join con server info | P0 |
| Auto-role | Assegnazione ruolo automatica (Membro) | P0 |
| Verification | Captcha verification al join (opzionale) | P1 |
| Level/XP system | Ruoli basati su attività (gamification) | P2 |

### 3.3 Ticket System
| Feature | Descrizione | Priorità |
|---|---|---|
| `/ticket` | Crea ticket con selezione categoria | P0 |
| AI classification | Classificazione automatica (bug/question/sales/other) | P0 |
| Auto-label + assign | Label automatica e notifica al team competente | P1 |
| Ticket close | `/close` con richiesta feedback | P0 |
| Transcript | Salvataggio transcript ticket in PDF/JSON | P2 |

### 3.4 AI Assistant
| Feature | Descrizione | Priorità |
|---|---|---|
| FAQ auto-reply | Risposta automatica a domande frequenti nei canali help | P0 |
| Context awareness | Il bot sa in quale canale/thread si trova per risposte pertinenti | P1 |
| Feedback collection | `/feedback` → analisi sentiment AI → salvataggio | P1 |
| Knowledge base | FAQ alimentate da documentazione Sycren | P2 |

### 3.5 Utility Commands
| Feature | Descrizione | Priorità |
|---|---|---|
| `/roadmap` | Mostra milestone corrente e progresso (da ROADMAP.md) | P0 |
| `/invite` | Genera link referral con tracking | P1 |
| `/ping` | Latenza bot | P0 |
| `/status` | Stato servizi Sycren (uptime, API) | P2 |

---

## 4. Flusso Ticket — End-to-End

```
1. Utente scrive /ticket
       │
       ▼
2. Modal con dropdown: 🐛 Bug | ❓ Question | 💼 Sales | 💬 Other
       │
       ▼
3. AI classifica il contenuto + sentiment
       │
       ▼
4. Ticket creato in canale privato #ticket-xxx
   ├── Tag automatico: [bug]/[question]/[sales]
   ├── Mention al membro del team competente
   └── Messaggio di conferma all'utente in DM
       │
       ▼
5. Team risponde nel canale ticket
       │
       ▼
6. Utente chiude con /close
       │
       ▼
7. Feedback richiesto (rating 1-5 + commento)
       │
       ▼
8. Transcript salvato + ticket archiviato
```

---

## 5. Stack Tecnologico

| Layer | Tecnologia | Versione |
|---|---|---|
| Runtime | Node.js | 20 LTS |
| Language | TypeScript | 5+ (strict) |
| Discord Library | Discord.js | 14+ |
| AI | OpenAI API | GPT-4o-mini |
| Database | Supabase / PostgreSQL (via sycren-db) | — |
| Hosting | Railway / Fly.io | Gratuito Fase 0 |
| CI/CD | GitHub Actions | — |

### Variabili d'Ambiente
```bash
DISCORD_BOT_TOKEN=xxxxx
DISCORD_CLIENT_ID=xxxxx
DISCORD_GUILD_ID=xxxxx
OPENAI_API_KEY=sk-xxxxx
OPENAI_MODEL=gpt-4o-mini
SUPABASE_URL=xxxxx
SUPABASE_ANON_KEY=xxxxx
MOD_LOG_CHANNEL_ID=xxxxx
WELCOME_CHANNEL_ID=xxxxx
TICKET_CATEGORY_ID=xxxxx
BOT_PREFIX=/
```

---

## 6. Struttura del Progetto

```
sycren-discord-bot/
├── src/
│   ├── index.ts                  # Entry point
│   ├── client.ts                 # Discord client setup
│   ├── config.ts                 # Environment config
│   ├── commands/
│   │   ├── ticket.ts
│   │   ├── roadmap.ts
│   │   ├── invite.ts
│   │   ├── feedback.ts
│   │   ├── ping.ts
│   │   └── status.ts
│   ├── events/
│   │   ├── guildMemberAdd.ts     # Welcome + auto-role
│   │   ├── messageCreate.ts      # Moderation + FAQ
│   │   └── interactionCreate.ts  # Slash command handler
│   ├── ai/
│   │   ├── moderator.ts          # Content scanning
│   │   ├── classifier.ts         # Ticket classification
│   │   ├── faq.ts                # FAQ auto-reply
│   │   └── sentiment.ts          # Sentiment analysis
│   ├── services/
│   │   ├── ticket.service.ts
│   │   ├── moderation.service.ts
│   │   └── analytics.service.ts
│   ├── utils/
│   │   ├── logger.ts
│   │   ├── embed.ts              # Embed builders
│   │   └── database.ts           # Supabase client
│   └── types/
│       └── index.ts
├── .env.example
├── tsconfig.json
├── package.json
└── Dockerfile
```

---

## 7. Criteri di Completamento

**MVP (Fase 0):**
- [ ] Bot online 24/7 su server Discord Sycren
- [ ] Slash command `/ticket` funzionante con classificazione AI
- [ ] Moderazione automatica (bad words, spam detection)
- [ ] Welcome message con auto-role al join
- [ ] Comando `/roadmap` che mostra milestone corrente
- [ ] FAQ auto-reply su canali help
- [ ] Log moderation in canale privato #mod-log

**Produzione (Fase 2+):**
- [ ] Integrazione con `studio.sycren.com` (notifiche progetto in DM)
- [ ] Integrazione con `app.sycren.com` (alert sicurezza)
- [ ] Dashboard web configurazione bot
- [ ] Analytics e report community

---

## Related Notes

| Document | Description |
|---|---|
| [[discord-bot.README]] | sycren-discord-bot overview |
| [[discord-bot.todo]] | Implementation plan |
| [[db.requirements\|sycren-db requirements]] | Optional — analytics and logging |
| [[app.requirements\|sycren-app requirements]] | Future — notifications integration |
| [[studio.requirements\|sycren-studio requirements]] | Future — client support tickets |
| [[foundation/ROADMAP\|Bootstrap Roadmap]] | Build order — parallel to landing |

---

*Sycren — Discord Bot PRD v1.0 — Giugno 2026*
*Documento interno.*
