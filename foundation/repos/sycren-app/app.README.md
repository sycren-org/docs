# sycren-app

> Central hub platform — SSO, dashboard, billing, and unified notifications (app.sycren.com).

## Description

The core of the Sycren ecosystem. A single dashboard that aggregates and manages all services across the four divisions through a single sign-on (SSO). Users access one environment to manage Studio projects, monitor Cloud servers, renew domains, and control infrastructure security.

## Tech Stack

- Next.js 14+ (App Router)
- TypeScript 5+
- Tailwind CSS 3.4+
- Prisma ORM (via sycren-db)
- Supabase (Auth + Database)
- Stripe (Payments)
- Resend (Email)

## Repository

`github.com/sycren/sycren-app` (private)

## Dependencies

| Dependency | Type | Note |
|---|---|---|
| [[db.README\|sycren-db]] | Database | Prisma schema, migrations, types |
| [[design-system.README\|sycren-design-system]] | UI | Shared components and design tokens |
| [[infra.README\|sycren-infra]] | Infrastructure | Docker, CI/CD, deployment |

## Consumers

| Service | Integration |
|---|---|
| [[cloud.README\|sycren-cloud]] | SSO, notifications, billing |
| [[domains.README\|sycren-domains]] | SSO, notifications |
| [[security.README\|sycren-security]] | SSO, notifications |

## Documentation

- [[app.requirements]] — Full PRD
- [[app.todo]] — Implementation plan
