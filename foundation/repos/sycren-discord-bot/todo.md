# sycren-discord-bot — TODO
> PRD v1.0 · Giugno 2026 · Fase 0 · Allineato a Bootstrap Roadmap v1.3

---

## Contesto (dalla Roadmap)

- 🕐 **16 anni** — Fase 0, parallelo alla landing page (Settimana 1-3)
- Priorità: **ALTA** — il server Discord è il punto di aggregazione community fin dal giorno 1
- Responsabile: Angelo (bot logic, AI, deploy) + Cosimo (slash command, UI embeds)
- Hosting: Railway / Fly.io (gratuito) o VPS Hetzner

---

## Setup Iniziale

- [ ] Creare applicazione Discord su Discord Developer Portal
- [ ] Ottenere Bot Token e Client ID
- [ ] Invitare bot al server Sycren con permessi (Send Messages, Manage Messages, Manage Channels, Moderate Members, Read Message History)
- [ ] Inizializzare progetto Node.js + TypeScript strict
- [ ] Installare dipendenze: discord.js v14, openai, dotenv, winston (logger)
- [ ] Configurare tsconfig.json (strict mode, ES2022 target)
- [ ] Creare `.env.example` con tutte le variabili
- [ ] Impostare struttura cartelle come da PRD §6
- [ ] Configurare GitHub Actions CI (lint + build su push)

---

## Fase 1: MVP (Settimana 1-2)

### Core Infrastructure
- [ ] Implementare `client.ts` con Discord client e intents (Guilds, GuildMessages, MessageContent, GuildMembers)
- [ ] Implementare `config.ts` — loader variabili ambiente con validazione
- [ ] Implementare `logger.ts` — winston logger con transport console + file
- [ ] Implementare `embed.ts` — helper per embed consistenti (colore Sycren indigo)
- [ ] Implementare slash command registration (dynamic loader da cartella commands/)
- [ ] Deployare bot su Railway / Fly.io con auto-restart

### Moderazione
- [ ] Implementare `moderation.service.ts` — bad words filtering con lista configurabile
- [ ] Implementare `messageCreate.ts` — scan messaggi e azione automatica (delete + warn)
- [ ] Implementare log moderation in canale #mod-log con embed dettagliato
- [ ] Implementare AI content scanning (`ai/moderator.ts`) — OpenAI per contenuti ambigui
- [ ] Implementare warn system (3 warn → timeout 1h, 5 warn → timeout 24h)

### Community & Onboarding
- [ ] Implementare `guildMemberAdd.ts` — welcome embed con regole server
- [ ] Implementare auto-role (ruolo "Membro" al join)
- [ ] Implementare verification captcha (opzionale)

### Ticket System
- [ ] Implementare comando `/ticket` con modal a dropdown (Bug / Question / Sales / Other)
- [ ] Implementare `ai/classifier.ts` — classificazione contenuto ticket via OpenAI
- [ ] Implementare `ticket.service.ts`:
  - Creazione canale privato #ticket-xxx
  - Assegnazione tag automatico
  - Mention team competente
  - Embed di conferma per l'utente
- [ ] Implementare button "Close ticket" con richiesta feedback
- [ ] Implementare `/close` command

### AI Assistant
- [ ] Implementare `ai/faq.ts` — risposta automatica a domande frequenti
- [ ] Creare knowledge base iniziale (faq.json con domande/risposte)
- [ ] Implementare contest awareness (il bot riconosce il canale)

### Utility Commands
- [ ] Implementare `/ping` — latenza bot
- [ ] Implementare `/roadmap` — embed con milestone corrente dal ROADMAP.md
- [ ] Implementare `/feedback` — raccolta feedback con sentiment analysis

---

## Fase 2: Produzione (parallelo a sviluppo piattaforma)

### Integrazioni
- [ ] Integrare `sycren-db` per salvare ticket, feedback, analytics
- [ ] Integrare notifiche `studio.sycren.com` (nuovo progetto = DM al cliente)
- [ ] Integrare alert `app.sycren.com` (incidente sicurezza = canale #security-alerts)
- [ ] Webhook per nuovi lead dalla waitlist della landing page

### Advanced Features
- [ ] Implementare `/status` — embed con stato servizi (uptime, API)
- [ ] Implementare `/invite` — referral link con tracking
- [ ] Implementare Level/XP system (messaggi → XP → ruoli)
- [ ] Dashboard web per configurare bot (React + Tailwind)
- [ ] Report settimanale community (messaggi, nuovi membri, ticket, sentiment)

---

## Deploy & Infrastruttura

- [ ] Dockerizzare il bot (Dockerfile multi-stage)
- [ ] Configurare Railway / Fly.io per hosting 24/7
- [ ] Implementare graceful shutdown (SIGINT/SIGTERM handler)
- [ ] Rate limiting sulle API call (Discord + OpenAI)
- [ ] Error tracking (Sentry o logging strutturato)
- [ ] Health check endpoint (opzionale)

---

## Verification Criteria

- [ ] Bot risponde ai comandi slash < 2 secondi
- [ ] Moderazione AI non produce falsi positivi > 5%
- [ ] Ticket system: dalla creazione alla chiusura in < 5 click
- [ ] Bot uptime ≥ 99% (Fase 0)
- [ ] Welcome message inviato entro 2 secondi dal join
- [ ] FAQ auto-reply non interferisce con conversazioni reali
- [ ] Nessun comando rotto dopo deploy (test manuale pre-commit)
- [ ] Tutti i token e secret in `.env`, mai committati

---

## Oltre il Bot — Evoluzione

| Feature | Quando | Priorità |
|---|---|---|
| Dashboard web configurazione | Fase 2 | P2 |
| Integrazione notification alerts | Fase 2 (app live) | P1 |
| Analytics community dashboard | Fase 2-3 | P2 |
| Multi-server support (clienti) | Fase 3 | P3 |
| Onboarding automation per clienti Studios | Fase 2 | P1 |

---

*Sycren Discord Bot — TODO v1.0 · Giugno 2026 · Fase 0*
