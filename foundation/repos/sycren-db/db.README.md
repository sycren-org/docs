# sycren-db

> Shared database package: Prisma schema, migrations, types and DB utilities for all Sycren applications.

## Description

Centralized database foundation used by `sycren-app`, `sycren-studio`, `sycren-cloud`, `sycren-domains`, and `sycren-security`. Contains the canonical Prisma schema, versioned migrations, generated TypeScript types, and shared database utilities.

## Tech Stack

- Prisma ORM
- TypeScript
- PostgreSQL (via Supabase)
- MongoDB
- Redis

## Repository

`github.com/sycren/sycren-db` (private)

## Consumers (bloccati da sycren-db)

| Service | Schema Entities Used |
|---|---|
| [[app.README\|sycren-app]] | User, Notification, Invoice |
| [[studio.README\|sycren-studio]] | User, Project, Invoice, ProjectMember |
| [[cloud.README\|sycren-cloud]] | User, Server |
| [[domains.README\|sycren-domains]] | User, Domain, DnsRecord |
| [[security.README\|sycren-security]] | User, SecurityRule, ThreatLog |
| [[discord-bot.README\|sycren-discord-bot]] | Analytics and logging (optional) |

## Documentation

- [[db.requirements]] — Full PRD with complete schema
- [[db.todo]] — Implementation plan
