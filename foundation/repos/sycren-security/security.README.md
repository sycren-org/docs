# sycren-security

> Managed security layer for digital infrastructure (security.sycren.com).

## Description

The cybersecurity division of Sycren. Protects domains, servers, and applications through Web Application Firewall (WAF), anti-DDoS protection, continuous monitoring, and vulnerability analysis.

## Tech Stack

- Next.js 14+ (App Router)
- TypeScript 5+
- Tailwind CSS 3.4+
- Prisma ORM (via sycren-db)
- Cloudflare API (WAF, DDoS)

## Repository

`github.com/sycren/sycren-security` (private)

## Dependencies

| Dependency | Type | Note |
|---|---|---|
| [[db.README\|sycren-db]] | Database | SecurityRule, ThreatLog schema |
| [[app.README\|sycren-app]] | SSO | Authentication and notifications |
| [[design-system.README\|sycren-design-system]] | UI | Shared components |
| [[infra.README\|sycren-infra]] | Infrastructure | Docker, CI/CD, deployment |

## Protects

| Service | Protection Layer |
|---|---|
| [[cloud.README\|sycren-cloud]] | WAF and DDoS for cloud servers |
| [[domains.README\|sycren-domains]] | DNS-level protection |

## Documentation

- [[security.requirements]] — Full PRD
- [[security.todo]] — Implementation plan
