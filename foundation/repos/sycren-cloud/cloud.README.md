# sycren-cloud

> Managed cloud hosting and VPS provisioning platform (cloud.sycren.com).

## Description

The cloud infrastructure division of Sycren. Offers hosting solutions, VPS provisioning, automated deployments, and server monitoring with a focus on performance, reliability, and ease of management.

## Tech Stack

- Next.js 14+ (App Router)
- TypeScript 5+
- Tailwind CSS 3.4+
- Prisma ORM (via sycren-db)
- Docker
- Hetzner / DigitalOcean API

## Repository

`github.com/sycren/sycren-cloud` (private)

## Dependencies

| Dependency | Type | Note |
|---|---|---|
| [[db.README\|sycren-db]] | Database | User, server, invoice schema |
| [[app.README\|sycren-app]] | SSO | Authentication and billing |
| [[design-system.README\|sycren-design-system]] | UI | Shared components |
| [[infra.README\|sycren-infra]] | Infrastructure | Docker, CI/CD, deployment |

## Integrations

- [[domains.README\|sycren-domains]] — Point domain to cloud server
- [[security.README\|sycren-security]] — WAF and DDoS protection for servers

## Documentation

- [[cloud.requirements]] — Full PRD
- [[cloud.todo]] — Implementation plan
