# Sycren — Knowledge Base

> Vault radice della documentazione Sycren. Usa i wikilink per navigare tra progetti, requisiti e TODO.

---

## Architettura della Piattaforma

```
                      ┌──────────────────┐
                      │  sycren-landing   │
                      │  (sycren.com)     │
                      └────────┬─────────┘
                               │
                      ┌────────▼─────────┐
                      │    sycren-db      │ ← Foundation bloccante
                      │  (database unico) │
                      └────────┬─────────┘
                               │
              ┌────────────────┼────────────────┐
              │                │                │
     ┌────────▼─────┐  ┌──────▼──────┐  ┌──────▼──────┐
     │ sycren-studio │  │  sycren-app  │  │sycren-dscord │
     │(studio.sycren)│  │(app.sycren)  │  │   -bot      │
     └────────────────┘  └──────┬──────┘  └─────────────┘
                                │ SSO
              ┌─────────────────┼──────────────────┐
              │                 │                  │
     ┌────────▼─────┐  ┌───────▼───────┐  ┌───────▼───────┐
     │ sycren-cloud  │  │sycren-domains  │  │sycren-security│
     │(cloud.sycren) │  │(domains.sycren)│  │(security.sycren│
     └────────────────┘  └───────────────┘  └───────────────┘

    ┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐
    │ sycren-design-sys │  │  sycren-infra    │  │   sycren-docs    │
    │ (UI condivisa)    │  │ (IaC + CI/CD)    │  │ (documentazione) │
    └──────────────────┘  └──────────────────┘  └──────────────────┘
```

---

## Documenti Fondazione

- [[foundation/README|README — Documento di Fondazione v1.0]]
- [[foundation/ROADMAP|ROADMAP — Bootstrap Roadmap v2.0]]

---

## Database Foundation

- [[db.README|sycren-db — README]]
- [[db.requirements|sycren-db — Requirements]]
- [[db.todo|sycren-db — TODO]]

---

## Landing Page

- [[landing.README|sycren-landing — README]]
- [[landing.requirements|sycren-landing — Requirements]]
- [[landing.todo|sycren-landing — TODO]]

---

## Hub Centrale (SSO)

- [[app.README|sycren-app — README]]
- [[app.requirements|sycren-app — Requirements]]
- [[app.todo|sycren-app — TODO]]

---

## Client Portal (Studios)

- [[studio.README|sycren-studio — README]]
- [[studio.requirements|sycren-studio — Requirements]]
- [[studio.todo|sycren-studio — TODO]]

---

## Cloud Infrastructure

- [[cloud.README|sycren-cloud — README]]
- [[cloud.requirements|sycren-cloud — Requirements]]
- [[cloud.todo|sycren-cloud — TODO]]

---

## Domain Management

- [[domains.README|sycren-domains — README]]
- [[domains.requirements|sycren-domains — Requirements]]
- [[domains.todo|sycren-domains — TODO]]

---

## Security

- [[security.README|sycren-security — README]]
- [[security.requirements|sycren-security — Requirements]]
- [[security.todo|sycren-security — TODO]]

---

## Discord Bot

- [[discord-bot.README|sycren-discord-bot — README]]
- [[discord-bot.requirements|sycren-discord-bot — Requirements]]
- [[discord-bot.todo|sycren-discord-bot — TODO]]

---

## Design System

- [[design-system.README|sycren-design-system — README]]
- [[design-system.requirements|sycren-design-system — Requirements]]
- [[design-system.todo|sycren-design-system — TODO]]

---

## Documentation Platform

- [[docs.README|sycren-docs — README]]
- [[docs.requirements|sycren-docs — Requirements]]
- [[docs.todo|sycren-docs — TODO]]

---

## Infrastructure as Code

- [[infra.README|sycren-infra — README]]
- [[infra.requirements|sycren-infra — Requirements]]
- [[infra.todo|sycren-infra — TODO]]