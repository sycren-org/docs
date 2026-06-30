# sycren-domains

> Domain registrar and DNS management platform (domains.sycren.com).

## Description

The domain management division of Sycren. Allows users to search, register, renew, and transfer domains with an advanced DNS panel. Integrated directly with Sycren Cloud and Security.

## Tech Stack

- Next.js 14+ (App Router)
- TypeScript 5+
- Tailwind CSS 3.4+
- Prisma ORM (via sycren-db)
- Cloudflare API

## Repository

`github.com/sycren/sycren-domains` (private)

## Dependencies

| Dependency | Type | Note |
|---|---|---|
| [[db.README\|sycren-db]] | Database | Domain, DnsRecord schema |
| [[app.README\|sycren-app]] | SSO | Authentication and notifications |
| [[design-system.README\|sycren-design-system]] | UI | Shared components |
| [[infra.README\|sycren-infra]] | Infrastructure | Docker, CI/CD, deployment |

## Integrations

| Service | Integration |
|---|---|
| [[cloud.README\|sycren-cloud]] | Point domain to hosting |
| [[security.README\|sycren-security]] | DDoS protection on domain |

## Documentation

- [[domains.requirements]] — Full PRD
- [[domains.todo]] — Implementation plan
