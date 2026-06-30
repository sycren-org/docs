# sycren-studio

> Client portal for Sycren Studios — project management, quotes, onboarding, and client communication (studio.sycren.com).

## Description

The agency division of Sycren. A full-featured client portal where clients can request quotes, track project progress, share files, and communicate with the team. Designed for startups and companies that need custom digital solutions.

## Tech Stack

- Next.js 14+ (App Router)
- TypeScript 5+
- Tailwind CSS 3.4+
- Prisma ORM (via sycren-db)
- Supabase (Auth + Database)
- Resend (Email notifications)

## Repository

`github.com/sycren/sycren-studio` (private)

## Dependencies

| Dependency | Type | Note |
|---|---|---|
| [[db.README\|sycren-db]] | Database | User, Project, Invoice schema |
| [[design-system.README\|sycren-design-system]] | UI | Shared components and design tokens |
| [[infra.README\|sycren-infra]] | Infrastructure | Docker, CI/CD, deployment |

## Integrations

- [[app.README\|sycren-app]] — SSO, unified billing, notifications (Fase 2)
- [[cloud.README\|sycren-cloud]] — Hosting for client projects

## Documentation

- [[studio.requirements]] — Full PRD
- [[studio.todo]] — Implementation plan
